# Phase 36: Call Center Settings Integration

## Backend
- [x] Add `CallCenterSettings` model to Prisma schema
- [x] Extend `SipExtension` with GDMS fields
- [x] Extend `CallRecord` with GDMS/recording fields
- [x] Add `callCenterSettings` relation to `Tenant`
- [x] Create `gdms-api.service.ts` (encryption, connection test, extension mgmt, call sync, recording)
- [x] Create `call-center-settings.service.ts` (CRUD, auto-assign, sync)
- [x] Create `call-center-settings.controller.ts` (GET/PUT settings, test, sync, auto-assign)
- [x] Update `call-center.module.ts` with new providers

## Frontend
- [x] Add `CallCenterSettingsData` interface to `callCenter.ts`
- [x] Add settings API functions (getSettings, updateSettings, testConnection, autoAssign, syncCallLogs)
- [x] Create `CallCenterSettingsPage.tsx` (5 sections: UCM, GDMS, Extensions, Recording, Advanced)
- [x] Add lazy import and route `/settings/call-center` in `App.tsx`

## Deployment
- [x] Sync code to production server
- [x] Fix 7 TypeScript null-check errors in `gdms-api.service.ts`
- [x] Build backend image (no-cache)
- [x] Build frontend image (no-cache)
- [x] Apply schema changes with `prisma db push` (v5.22.0)
- [x] Verify all containers healthy
