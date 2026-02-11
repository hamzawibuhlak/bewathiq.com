# Phase 32: Call Center WebRTC + WhatsApp QR — Walkthrough

## Overview

Phase 32 integrates two communication channels into the Watheeq dashboard:

1. **Call Center** — WebRTC-based browser phone using JsSIP, connecting to UCM6301 PBX
2. **WhatsApp QR** — QR-code-based WhatsApp connection using the Baileys library (no Meta Business API required)

---

## Database Schema

Added 4 new models + 3 enums to `prisma/schema.prisma`:

| Model | Purpose |
|---|---|
| `SipExtension` | Maps users to PBX extensions with AES-encrypted SIP passwords |
| `CallRecord` | Logs all calls with auto-client matching |
| `WhatsappSession` | One-per-tenant session for Baileys connection state |
| `WhatsappBaileysMessage` | Message history with client/agent linking |

Relations added to `Tenant`, `User`, `Client`, and `Case` models.

---

## Backend Changes

### Call Center Module ([call-center/](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center))

| File | Responsibility |
|---|---|
| [sip-extension.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/sip-extension.service.ts) | AES encrypt/decrypt SIP passwords, CRUD for extensions |
| [call-record.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-record.service.ts) | Log calls, auto-match clients by phone, call statistics |
| [call-center.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center.controller.ts) | REST endpoints for extensions + call records |
| [call-center.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center.module.ts) | Module registration |

### WhatsApp Baileys Service

| File | Changes |
|---|---|
| [whatsapp-baileys.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp-baileys.service.ts) | New — QR session management, message send/receive, auto-reconnect, session restore on startup |
| [whatsapp.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp.controller.ts) | Added 5 QR endpoints: `qr/connect`, `qr/disconnect`, `qr/status`, `qr/send`, `qr/messages` |
| [whatsapp.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/whatsapp/whatsapp.module.ts) | Added `WhatsappBaileysService` + `WebSocketModule` |

### WebSocket + App Module

| File | Changes |
|---|---|
| [websocket.gateway.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/websocket/websocket.gateway.ts) | Added `broadcastWhatsAppQR()` and `broadcastWhatsAppStatus()` |
| [app.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/app.module.ts) | Registered `CallCenterModule` |

---

## Frontend Changes

### API Services

| File | Purpose |
|---|---|
| [callCenter.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/callCenter.ts) | SIP extension CRUD + call record operations |
| [whatsappQr.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/whatsappQr.ts) | QR connect/disconnect/status/send/messages |

### Components

| File | Description |
|---|---|
| [Softphone.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/call-center/Softphone.tsx) | Floating softphone with dialpad, in-call controls, call history |
| [WhatsappConnect.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/whatsapp/WhatsappConnect.tsx) | QR code display, status badge, connect/disconnect buttons |

### Hooks

| File | Changes |
|---|---|
| [useSoftphone.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/useSoftphone.ts) | New — JsSIP WebRTC hook with full call lifecycle management |
| [use-websocket.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/use-websocket.ts) | Added `useWhatsAppQR` and `useWhatsAppStatus` hooks |
| [websocket.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/lib/websocket.ts) | Added `whatsapp:qr` and `whatsapp:status` event types |

---

## Dependencies Installed

### Backend
- `@whiskeysockets/baileys` — WhatsApp Web API
- `qrcode` + `@types/qrcode` — QR code generation
- `crypto-js` + `@types/crypto-js` — AES encryption for SIP passwords

### Frontend
- `jssip` — SIP/WebRTC client
- `qrcode.react` — QR code React component (available for future use)

---

## Verification

| Check | Result |
|---|---|
| Backend `tsc --noEmit` | ✅ Zero errors |
| Frontend `tsc --noEmit` | ✅ Zero errors |
| Prisma generate | ✅ Success |

---

## Next Steps (Pre-Deployment)

1. **Prisma migration** — `npx prisma migrate dev --name phase32_call_center_whatsapp_qr`
2. **Environment variables** — Set `WHATSAPP_SESSIONS_PATH`, `SIP_PASSWORD_ENCRYPTION_KEY`
3. **UCM6301 configuration** — Enable WebRTC, TLS certificates, port forwarding for WSS
4. **Integration testing** — Test QR scanning across networks, SIP registration, call flows
