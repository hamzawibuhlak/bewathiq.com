# Phase 32: Call Center WebRTC + WhatsApp QR Integration

Integrate UCM6301 PBX via WebRTC (JsSIP) for browser-based calling and Baileys for WhatsApp QR-based messaging — both accessible directly from the Watheeq dashboard.

> [!IMPORTANT]
> **UCM6301 Setup Required First** — WebRTC, TLS, and extensions must be configured on the PBX before the softphone will connect. Port 8089 (WSS) must be open externally.

## Proposed Changes

### Database Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

Add new models and enums after the existing `CallRecording` model (~line 1325):

- **`SipExtension`** — Maps each user to a UCM SIP extension (1:1 `userId @unique`)
- **`WhatsappSession`** — Baileys session per tenant (`tenantId @unique`)
- **`WhatsappMessage`** — All WA messages in/out with auto-client linking
- **Enums**: `WhatsappStatus`, `MessageDirection`, `MessageStatus`
- **Relations** added to `Tenant`, `User`, `Client` models

> [!NOTE]
> Existing `Call`, `CallRecording`, `CallDirection`, `CallStatus` models are kept as-is. The new `SipExtension` model adds WebRTC config alongside them.

---

### Backend — Call Center (SIP Extensions + Call Records)

#### [NEW] [sip-extension.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/sip-extension.service.ts)
- `getExtensions(tenantId)` — List all SIP extensions
- `assignExtension(tenantId, data)` — Assign extension to user (AES encrypted password)
- `getExtensionForUser(userId, tenantId)` — Return WSS URL + connection config
- `deleteExtension(id, tenantId)`

#### [NEW] [call-record.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-record.service.ts)
- `logCall()` — Auto-match client by phone number
- `updateCallStatus()` — Update status/duration/timestamps
- `addNotes()` — Link notes + case to call
- `getCallHistory()` — Filtered/paginated history
- `getStats()` — Today's call analytics

#### [NEW] [call-center.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center.controller.ts)
- `GET /call-center/extension` — My extension config
- `POST /call-center/extension` — Assign extension (OWNER/ADMIN)
- `GET /call-center/extensions` — All tenant extensions
- `DELETE /call-center/extension/:id` — Remove extension
- `POST /call-center/calls` — Log a call
- `PATCH /call-center/calls/:callId` — Update call status
- `GET /call-center/calls` — Call history
- `GET /call-center/stats` — Call stats

#### [NEW] [call-center.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center.module.ts)

---

### Backend — WhatsApp Baileys Service

#### [NEW] [whatsapp-baileys.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp-baileys.service.ts)
- `initSession(tenantId)` — Create Baileys socket, emit QR via WebSocket
- `sendMessage(tenantId, phone, message)` — Send WA message
- `disconnect(tenantId)` — Logout + delete session files
- `getSessionStatus(tenantId)` — Current status (CONNECTED/DISCONNECTED/QR_PENDING)
- `getMessages(tenantId, filters)` — Message history
- `restoreActiveSessions()` — On server startup, reconnect saved sessions

#### [MODIFY] [whatsapp.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp.controller.ts)
- Add new endpoints for Baileys: `POST /whatsapp/qr/connect`, `POST /whatsapp/qr/disconnect`, `GET /whatsapp/qr/status`, `POST /whatsapp/qr/send`, `GET /whatsapp/qr/messages`

#### [MODIFY] [whatsapp.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp.module.ts)
- Add `WhatsappBaileysService` as provider

#### [MODIFY] [websocket.gateway.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/websocket/websocket.gateway.ts)
- Add `broadcastWhatsAppQR()` and `broadcastWhatsAppStatus()` methods

---

### Frontend — Softphone

#### [NEW] [useSoftphone.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/useSoftphone.ts)
- JsSIP UA integration with states: idle → registering → registered → ringing/calling → in-call
- `register()`, `makeCall()`, `answerCall()`, `hangUp()`, `toggleMute()`, `toggleHold()`

#### [NEW] [Softphone.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/call-center/Softphone.tsx)
- Floating button with status bar, dialpad popup, incoming call card, active call controls

#### [NEW] [callCenter.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/callCenter.ts)

---

### Frontend — WhatsApp QR

#### [NEW] [WhatsappConnect.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/whatsapp/WhatsappConnect.tsx)
- 3-state UI: Disconnected → QR Pending → Connected
- Real-time QR display via WebSocket

#### [NEW] [whatsappQr.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/whatsappQr.ts)

#### [MODIFY] [use-websocket.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/use-websocket.ts)
- Add `useWhatsAppQR()` hook for QR and status events

---

### App Module Registration

#### [MODIFY] [app.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/app.module.ts)
- Import and register `CallCenterModule`

---

### Dependencies

**Backend** — `@whiskeysockets/baileys`, `@hapi/boom` (new installs)
**Frontend** — `jssip`, `@types/jssip` (new installs)

> [!NOTE]
> `socket.io`, `@nestjs/websockets`, `@nestjs/platform-socket.io`, `qrcode` are already installed.

## Verification Plan

### Automated Tests
- `curl` API tests for call-center and whatsapp-qr endpoints after deploy
- Database sync verification via `prisma db push`

### Manual Verification
- WebRTC softphone registration against UCM6301 (requires UCM setup)
- WhatsApp QR scan from mobile phone
- Real-time message delivery via WebSocket
