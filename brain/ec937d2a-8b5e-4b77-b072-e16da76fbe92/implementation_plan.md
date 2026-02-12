# Phase 36: Complete Call Center Integration

UCM6301 + GDMS integration with settings page, auto-extension creation, recording management and connection testing.

## Proposed Changes

### Database Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

**Add `CallCenterSettings` model** (after `CallRecord` at line ~3653):
- UCM connection fields: `ucmHost`, `ucmPort` (default 8089), `ucmWebsocketPath` ("/ws")
- GDMS API fields: `gdmsApiKey`, `gdmsApiSecret` (both encrypted), `gdmsDeviceId`, `gdmsAccountId`
- Extension range: `extensionPrefix`, `extensionStart`/`extensionEnd`, `defaultPassword`
- Recording: `enableRecording`, `recordingFormat`, `autoDeleteDays`
- Advanced: `stunServer`, `enableNat`, `rtpPortStart`/`rtpPortEnd`
- Status: `isConnected`, `lastSync`, `lastError`, `syncAttempts`
- Unique on `tenantId`, relation to `Tenant`

**Extend `SipExtension`**: add `gdmsExtensionId`, `createdViaGdms`, `lastRegistered`, `registrationStatus`

**Extend `CallRecord`**: add `gdmsCallId`, `recordingSize`, `recordingDownloaded`

**Add to `Tenant`**: `callCenterSettings CallCenterSettings?`

---

### Backend

#### [NEW] [gdms-api.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/gdms-api.service.ts)
- AES-256-CBC encrypt/decrypt for credentials
- `getApiClient()` — axios client with GDMS auth headers
- `testConnection()` — validate GDMS device access, update status
- `createExtensionOnGdms()` / `deleteExtensionFromGdms()` — remote extension management
- `syncExtensionStatus()` — registration state sync
- `syncCallLogs()` — CDR import from GDMS
- `getRecordingDownloadUrl()` / `downloadAndStoreRecording()` — recording management

#### [NEW] [call-center-settings.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center-settings.service.ts)
- `getSettings()` — returns settings with masked secrets
- `updateSettings()` — encrypts secrets before saving
- `testConnection()` — wrapper
- `autoAssignExtension()` — finds next available number + creates on GDMS + saves DB

#### [NEW] [call-center-settings.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center-settings.controller.ts)
- `GET /call-center/settings`
- `PUT /call-center/settings`
- `POST /call-center/settings/test-connection`
- `POST /call-center/settings/auto-assign-extension`

#### [MODIFY] [call-center.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/call-center/call-center.module.ts)
- Register `GdmsApiService`, `CallCenterSettingsService`, `CallCenterSettingsController`

---

### Frontend

#### [NEW] [CallCenterSettingsPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/settings/CallCenterSettingsPage.tsx)
- 4 card sections: UCM Connection, GDMS API, Extension Settings, Recording Settings
- Connection status badge (green/red)
- Edit mode toggle + save/cancel
- Test connection button
- Last sync timestamp

#### [MODIFY] [callCenter.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/callCenter.ts)
- Add `getSettings()`, `updateSettings()`, `testConnection()`

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)
- Add lazy import for `CallCenterSettingsPage`
- Add route `<Route path="call-center" element={<CallCenterSettingsPage />} />`

## Verification Plan

### Automated Tests
- Build both frontend and backend successfully
- Run `npx prisma migrate dev` without errors

### Manual Verification
- Deploy to production
- Navigate to `/:slug/settings/call-center`
- Verify settings form loads, saves, and masks secrets
- Verify "Test Connection" button sends request
