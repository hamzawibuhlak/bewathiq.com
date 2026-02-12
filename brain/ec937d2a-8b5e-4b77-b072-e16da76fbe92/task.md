# Phase 36: Complete Call Center Integration

## Database
- [ ] Add `CallCenterSettings` model to schema.prisma
- [ ] Add new fields to `SipExtension` (gdmsExtensionId, registrationStatus, etc.)
- [ ] Add new fields to `CallRecord` (gdmsCallId, recordingSize, recordingDownloaded)
- [ ] Add `callCenterSettings` relation to `Tenant` model
- [ ] Run migration

## Backend Services
- [ ] Create `gdms-api.service.ts` — GDMS API client with encrypt/decrypt, testConnection, extension management, call logs sync, recording management
- [ ] Create `call-center-settings.service.ts` — getSettings, updateSettings, testConnection, autoAssignExtension
- [ ] Create `call-center-settings.controller.ts` — GET/PUT settings, POST test-connection, POST auto-assign
- [ ] Update `call-center.module.ts` with new services and controller

## Frontend
- [ ] Create `CallCenterSettingsPage.tsx` — 4 sections (UCM, GDMS, Extensions, Recording)
- [ ] Add API functions to `callCenter.ts` (getSettings, updateSettings, testConnection)
- [ ] Add route `settings/call-center` in `App.tsx`
- [ ] Add lazy import for `CallCenterSettingsPage`

## Deploy & Verify
- [ ] Build and deploy to production
- [ ] Verify settings page loads
- [ ] Verify save/edit flow works
