# Phase 35: Tenant RBAC — نظام صلاحيات شامل

## Overview

Replace the current simple `UserRole` enum + `Permission`/`RolePermission` system with a comprehensive tenant-scoped RBAC that allows each office owner to create custom roles with **120+ granular permissions** across **11 modules**. Each permission has an **access level** (NONE/VIEW/EDIT/FULL) and an optional **scope** (OWN/ASSIGNED/TEAM/ALL).

> [!IMPORTANT]
> **Backward Compatibility**: We keep the existing `role: UserRole` field on `User` for backward compatibility. The new `tenantRoleId` is optional. The new `TenantPermissionGuard` will check `tenantRole` first, then fall back to the old `role` enum if no tenant role is assigned. This means existing `@Roles()` decorators continue to work.

---

## Proposed Changes

### Database & Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/prisma/schema.prisma)

Add two new enums and three new models:

```prisma
enum TenantAccessLevel { NONE, VIEW, EDIT, FULL }
enum AccessScope { OWN, ASSIGNED, TEAM, ALL }

model TenantRole {
  id, name, nameEn, description, color, icon, isActive, isSystem
  tenantId → Tenant (cascade delete)
  createdBy
  permissions → TenantRolePermission[]
  users → User[]
  @@unique([tenantId, name])
}

model TenantRolePermission {
  id, roleId → TenantRole, resource, action
  accessLevel: TenantAccessLevel (default NONE)
  scope: AccessScope (default ALL)
  @@unique([roleId, resource, action])
}

model PermissionCache {
  id, userId (unique), permissions: Json, expiresAt
}
```

Add to `User` model:
```prisma
tenantRoleId  String?
tenantRole    TenantRole? @relation(...)
```

Add to `Tenant` model:
```prisma
tenantRoles   TenantRole[]
```

> [!NOTE]
> The existing `AccessLevel` enum (line 114) is already defined. We'll rename the new one to `TenantAccessLevel` to avoid conflicts.

---

### Backend — Core RBAC Module

#### [NEW] [tenant-roles.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/tenant-roles.module.ts)

NestJS module exporting `TenantPermissionService`, `TenantRolesService`, `TenantRolesController`. Imports `PrismaModule` and `CacheConfigModule`.

#### [NEW] [tenant-permission.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/tenant-permission.service.ts)

Core service with:
- `checkPermission(userId, resource, action, requiredLevel, resourceOwnerId)` — cached (2-5ms hit)
- `loadUserPermissions(userId)` — DB query, builds permission map
- `evaluatePermission()` — checks level hierarchy + scope
- `buildScopeFilter(userId, resource, action)` — returns Prisma `where` clause
- `getUserPermissionsMap(userId)` — returns full permission map for frontend
- `clearUserCache(userId)` / `clearRoleCache(roleId)` — cache invalidation

#### [NEW] [tenant-roles.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/tenant-roles.service.ts)

CRUD for tenant roles:
- `findAll(tenantId)` — lists roles with `_count` of users
- `findOne(id, tenantId)` — role with permissions
- `create(tenantId, dto)` — creates role + permissions
- `update(id, tenantId, dto)` — updates role + clears cache
- `delete(id, tenantId)` — checks no assigned users, deletes
- `cloneRole(id, tenantId, newName)` — duplicates role + permissions
- `seedDefaultRoles(tenantId)` — creates 5 template roles
- `getTemplates()` — returns 5 pre-built permission sets
- `assignRoleToUser(userId, roleId, tenantId)` — assigns + clears cache

#### [NEW] [tenant-roles.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/tenant-roles.controller.ts)

Endpoints (under `/:slug/tenant-roles`):
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | List all roles |
| `GET` | `/:id` | Get role details with permissions |
| `POST` | `/` | Create role with permissions |
| `PATCH` | `/:id` | Update role + permissions |
| `DELETE` | `/:id` | Delete role |
| `POST` | `/:id/clone` | Clone role |
| `POST` | `/:id/assign` | Assign role to user |
| `GET` | `/templates` | Get role templates |
| `GET` | `/my-permissions` | Get current user's permission map |

All protected by `OwnerGuard` except `my-permissions` (any authenticated user).

#### [NEW] [require-tenant-permission.decorator.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/decorators/require-tenant-permission.decorator.ts)

```typescript
@RequireTenantPermission('cases', 'create', 'EDIT')
```
Modeled after the existing super-admin decorator.

#### [NEW] [tenant-permission.guard.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/guards/tenant-permission.guard.ts)

Guard that reads `@RequireTenantPermission` metadata and calls `TenantPermissionService.checkPermission()`. Falls back to allowing OWNER/SUPER_ADMIN.

#### [NEW] [DTOs](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/dto/)

- `CreateTenantRoleDto` — name, description, color, permissions array
- `UpdateTenantRoleDto` — partial of above
- `AssignRoleDto` — userId, roleId

#### [NEW] [permission-map.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/constants/permission-map.constants.ts)

The full `COMPLETE_TENANT_PERMISSION_MAP` from the spec (11 modules, 120+ actions) as a shared TypeScript constant.

#### [NEW] [role-templates.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/tenant-roles/constants/role-templates.constants.ts)

5 pre-built role templates (Senior Lawyer, Junior Lawyer, Secretary, Accountant, Intern).

---

### Backend — Module Wiring

#### [MODIFY] [app.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/backend/src/app.module.ts)

Import `TenantRolesModule`.

---

### Frontend — API Layer

#### [NEW] [tenantRoles.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/api/tenantRoles.ts)

API functions: `getRoles()`, `getRole(id)`, `createRole()`, `updateRole()`, `deleteRole()`, `cloneRole()`, `assignRole()`, `getTemplates()`, `getMyPermissions()`.

#### [NEW] [usePermissions.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/hooks/usePermissions.ts)

Hook that:
- Fetches current user's permission map on mount
- Provides `can(resource, action, level?)` helper
- Caches permissions in memory

#### [NEW] [PermissionGate.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/components/common/PermissionGate.tsx)

```tsx
<PermissionGate resource="cases" action="create" level="EDIT">
  <Button>إنشاء قضية</Button>
</PermissionGate>
```

---

### Frontend — Pages

#### [NEW] [TenantRolesListPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/pages/settings/TenantRolesListPage.tsx)

Grid of role cards showing name, color, user count, permissions progress (X/120 enabled). CRUD actions for owner.

#### [NEW] [TenantRoleEditorPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/pages/settings/TenantRoleEditorPage.tsx)

Full-page role editor with:
- Role info section (name, nameEn, description, color, icon)
- Template selector (5 templates, one-click apply)
- `TenantPermissionsMatrix` component
- Save/Cancel with loading state

#### [NEW] [TenantPermissionsMatrix.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/components/tenant/TenantPermissionsMatrix.tsx)

Accordion-based permissions matrix:
- 11 collapsible module sections
- Each module: header (icon, label, progress badge), quick actions (set all FULL/VIEW/NONE)
- Each action row: label, 4 level radio buttons (NONE/VIEW/EDIT/FULL), scope dropdown (OWN/ASSIGNED/ALL)
- Summary footer with statistics

---

### Frontend — Routing

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq system projec/watheeq-mvp/frontend/src/App.tsx)

Add routes:
```
/settings/roles → TenantRolesListPage
/settings/roles/new → TenantRoleEditorPage
/settings/roles/:id → TenantRoleEditorPage
```

---

## Verification Plan

### Manual Verification (primary — no test framework detected)

1. **Database migration**: Run `npx prisma migrate dev` locally, verify tables `tenant_roles`, `tenant_role_permissions`, `permission_cache` are created, and `users` table has `tenantRoleId` column.

2. **Backend API testing via browser/curl**:
   - `GET /api/:slug/tenant-roles` — returns default roles
   - `POST /api/:slug/tenant-roles` — creates custom role with permissions
   - `PATCH /api/:slug/tenant-roles/:id` — updates permissions
   - `DELETE /api/:slug/tenant-roles/:id` — deletes non-system role
   - `POST /api/:slug/tenant-roles/:id/clone` — clones role
   - `GET /api/:slug/tenant-roles/my-permissions` — returns current user's permission map

3. **Frontend testing in browser**:
   - Navigate to `/settings/roles` — see roles grid
   - Click "إنشاء دور جديد" — see editor with matrix
   - Expand/collapse accordion sections
   - Click "كامل للكل" on Cases module — all actions set to FULL
   - Save role → verify in database
   - Assign role to user → verify cache cleared
   - Login as assigned user → verify restricted UI items are hidden

4. **Permission enforcement**:
   - Login as user with VIEW-only cases → attempt POST to create case → should get 403
   - Login as user with OWN scope → attempt to view another user's case → should not appear in list

5. **Production deployment**: Follow `/deploy` workflow after verification.
