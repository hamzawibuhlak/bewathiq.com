# Phase 42: Super Admin Module Control

## Database
- [x] Add `TenantModuleSettings` model to Prisma schema
- [x] Add `ModuleChangeLog` model to Prisma schema
- [x] Add relations to `Tenant` and `SuperAdminUser`
- [ ] Run migration (on deploy)

## Backend
- [x] Create `modules.constants.ts` with module registry + plan defaults
- [x] Create `module-settings.service.ts` with CRUD methods
- [x] Add module management endpoints to `super-admin.controller.ts`
- [x] Register service in `super-admin.module.ts`
- [x] Add tenant-side `GET /users/my-modules` endpoint

## Frontend — Core
- [x] Create `modules.constants.ts` (frontend mirror)
- [x] Create `useModules.ts` hook (fetch + zustand cache)
- [x] Add `moduleKey` to sidebar nav items
- [x] Integrate module check in `Sidebar.tsx` `filterItems()`

## Frontend — Super Admin UI
- [x] Create `SAModuleControlPage.tsx`
- [x] Add module management API methods to `superAdmin.ts`
- [x] Add route in `App.tsx`
- [x] Add "إدارة الأقسام" button in `SATenantDetailsPage.tsx`

## Deployment & Verification
- [ ] Deploy to production
- [ ] Verify SA can toggle modules
- [ ] Verify tenant sidebar reflects changes
