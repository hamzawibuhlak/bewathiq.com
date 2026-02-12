# Phase 34: Advanced RBAC System

## Database
- [x] Add `CustomRole`, `SAPermission` models and `AccessLevel` enum to `schema.prisma`
- [x] Add `customRoleId` field & relation to `SuperAdminUser`
- [ ] Run migration & seed system roles (Owner, Manager, Support)

## Backend Services
- [x] Create `roles.service.ts` — CRUD for custom roles
- [x] Create `permission.service.ts` — Permission checking logic
- [x] Create `permission.guard.ts` — NestJS guard
- [x] Create `require-permission.decorator.ts`
- [x] Update `super-admin.controller.ts` — Add role endpoints + permission decorators
- [x] Update `super-admin.module.ts` — Register new providers
- [x] Update `super-admin-auth.service.ts` — Include permissions in JWT/getMe
- [x] Update `staff.service.ts` — Use customRoleId, remove hardcoded map

## Frontend
- [x] Add roles API methods to `superAdmin.ts`
- [x] Update `superAdmin.store.ts` — Add permissions state + `hasPermission` helper
- [x] Create `PermissionsMatrix.tsx` component
- [x] Create `RolesListPage.tsx`
- [x] Create `RoleEditorPage.tsx`
- [x] Update `SALayout.tsx` — Add roles nav item
- [x] Update `App.tsx` — Add roles routes

## Verification
- [/] Deploy to production
- [ ] Test migration & seed
- [ ] Test permission guard blocks unauthorized actions
- [ ] Test UI: create role, assign, verify access control
