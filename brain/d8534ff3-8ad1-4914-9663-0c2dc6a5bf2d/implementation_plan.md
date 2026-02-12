# Phase 34: Advanced RBAC — نظام صلاحيات متقدم

## Goal

Replace the existing hardcoded `SuperAdminRole` enum system with a flexible, database-driven RBAC (Role-Based Access Control) system. Custom roles with granular per-resource/per-action permissions, a visual Permissions Matrix UI, and backend `PermissionGuard` on every endpoint.

## Current State

- `SuperAdminUser.role` field uses `SuperAdminRole` enum (OWNER/MANAGER/SUPPORT/SALES/MODERATOR)
- [staff.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/staff.service.ts) has hardcoded `ROLE_PERMISSIONS` map
- [super-admin-dashboard.guard.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/guards/super-admin-dashboard.guard.ts) checks `@SetMetadata('superAdminRole')` for single-role gates
- JWT payload includes `role` string from enum
- Frontend SALayout sidebar shows 5 nav items with no permission filtering

---

## Proposed Changes

### Database Layer

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

Add two new models and update `SuperAdminUser`:

1. **`CustomRole`** model — name, description, color, icon, `isSystem` flag, `isActive`
2. **`RolePermission`** model — `roleId`, `resource`, `action`, `accessLevel` (NONE/VIEW/EDIT/FULL)
3. **`AccessLevel` enum** — NONE, VIEW, EDIT, FULL
4. **`SuperAdminUser`** — add `customRoleId` + relation to `CustomRole`, keep `role` field for backward compat during migration

> [!IMPORTANT]
> The old `SuperAdminRole` enum and `role` field will be kept temporarily. Once migration seeds the system roles and maps existing users, the enum stays as a fallback but `customRoleId` takes precedence.

---

### Backend Services

#### [NEW] [roles.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/roles.service.ts)

CRUD for custom roles: `getAllRoles`, `getRoleDetails`, `createRole`, `updateRole`, `deleteRole`, `cloneRole`, `assignRoleToUser`, `getPermissionTemplates`

#### [NEW] [permission.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/permission.service.ts)

Permission checking: `checkPermission`, `getUserPermissions`, `checkMultiplePermissions`. OWNER bypass logic.

#### [NEW] [permission.guard.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/guards/permission.guard.ts)

NestJS CanActivate guard that reads `@RequirePermission()` decorator metadata and delegates to `PermissionService`.

#### [NEW] [require-permission.decorator.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/decorators/require-permission.decorator.ts)

`@RequirePermission(resource, action, level)` decorator using `SetMetadata`.

#### [MODIFY] [super-admin.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin.controller.ts)

- Add `@RequirePermission()` decorators to all existing endpoints
- Add new roles CRUD endpoints: `GET/POST/PATCH/DELETE /roles`, `POST /roles/:id/clone`

#### [MODIFY] [super-admin.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin.module.ts)

Register new services, guard, and controllers.

#### [MODIFY] [super-admin-auth.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin-auth.service.ts)

Include `customRoleId` in JWT payload and `getMe` response. Return user permissions map.

#### [MODIFY] [super-admin-dashboard.guard.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/guards/super-admin-dashboard.guard.ts)

Keep backward compat with `@SetMetadata('superAdminRole')` but also support new `PermissionGuard`.

#### [MODIFY] [staff.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/staff.service.ts)

Update `addStaff` to accept `customRoleId`, update `getStaff` to include role relation. Remove hardcoded `ROLE_PERMISSIONS`.

---

### Frontend

#### [NEW] [RolesListPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/roles/RolesListPage.tsx)

Grid of role cards — name, color, user count, system badge. Create/edit/delete/clone actions.

#### [NEW] [RoleEditorPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/roles/RoleEditorPage.tsx)

Sidebar for role metadata (name, desc, color) + main area with PermissionsMatrix. Create and edit modes.

#### [NEW] [PermissionsMatrix.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/super-admin/PermissionsMatrix.tsx)

Interactive table: rows = resources & actions, columns = NONE/VIEW/EDIT/FULL. Color-coded toggle buttons.

#### [MODIFY] [superAdmin.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/superAdmin.ts)

Add API methods: `getRoles`, `getRoleDetails`, `createRole`, `updateRole`, `deleteRole`, `cloneRole`, `getMyPermissions`.

#### [MODIFY] [SALayout.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/SALayout.tsx)

Add "الصلاحيات" nav item. Filter nav items by user permissions.

#### [MODIFY] [superAdmin.store.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/stores/superAdmin.store.ts)

Add `permissions` map and `hasPermission(resource, action, level)` helper.

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)

Add routes: `/super-admin/roles`, `/super-admin/roles/:id`

---

## Verification Plan

### Automated Tests
1. Run `npx prisma migrate deploy` — verify migration succeeds
2. Run `npx prisma db seed` — verify system roles (Owner, Manager, Support) are created
3. Start backend and test CRUD: `POST /super-admin/roles`, `GET /super-admin/roles`, etc.
4. Test permission guard: support user tries `hard_delete` → 403 Forbidden

### Manual Verification
1. Login as Owner → see all nav items, all features accessible
2. Create custom "Data Analyst" role → assign to staff → verify limited access
3. Permissions Matrix UI: toggle permissions, save, verify they persist
4. Delete role with users assigned → error message appears
