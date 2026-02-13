# Phase 35: Tenant RBAC System

## Part 1: Database & Schema (Days 1-2)
- [ ] Add `TenantAccessLevel` and `AccessScope` enums to Prisma schema
- [ ] Add `TenantRole` model with tenant isolation
- [ ] Add `TenantRolePermission` model with scope field
- [ ] Add `PermissionCache` model
- [ ] Add `tenantRoleId` to `User` model (keep old `role` field for backward compat)
- [ ] Add `tenantRoles` relation to `Tenant` model
- [ ] Create and run Prisma migration
- [ ] Create seed script: 5 default roles + 120+ permission actions per tenant

## Part 2: Backend Services (Days 2-4)
- [ ] Create `tenant-roles/` module directory
- [ ] Implement `TenantPermissionService` with caching (checkPermission, buildScopeFilter, cache management)
- [ ] Implement `TenantRolesService` (CRUD, clone, templates, assign)
- [ ] Implement `TenantRolesController` (5 endpoints under `/:slug/tenant-roles`)
- [ ] Create `@RequireTenantPermission()` decorator
- [ ] Create `TenantPermissionGuard`
- [ ] Create DTOs: CreateTenantRoleDto, UpdateTenantRoleDto
- [ ] Wire module into `AppModule`

## Part 3: Service Integration (Day 4-5)
- [ ] Integrate permission checks into CasesService (scope filtering)
- [ ] Integrate into ClientsService
- [ ] Integrate into DocumentsService
- [ ] Integrate into HearingsService
- [ ] Integrate into TasksService
- [ ] Integrate into InvoicesService
- [ ] Add `GET /api/:slug/my-permissions` endpoint for frontend

## Part 4: Frontend — API & Hooks (Day 5)
- [ ] Add `tenantRolesApi` to API layer
- [ ] Create `usePermissions` hook (fetch + check permissions)
- [ ] Create `PermissionGate` component (conditional rendering)

## Part 5: Frontend — Pages & UI (Days 6-7)
- [ ] Build `RolesListPage` (grid with colors, badges, progress)
- [ ] Build `RoleEditorPage` (name, description, color, template selector)
- [ ] Build `TenantPermissionsMatrix` (accordion with 11 modules, 120+ actions)
- [ ] Build `PermissionSummary` component
- [ ] Build `TemplateSelector` component (5 templates)
- [ ] Build `AssignRoleModal` component
- [ ] Add routes to App.tsx

## Part 6: Frontend Integration (Day 7)
- [ ] Integrate `usePermissions` into sidebar navigation (hide unauthorized items)
- [ ] Add role column to UsersPage
- [ ] Add "assign role" action in users list

## Part 7: Testing & Verification (Day 8)
- [ ] Test role CRUD (create, edit, clone, delete)
- [ ] Test permission matrix save/load
- [ ] Test scope filtering (OWN/ASSIGNED/ALL)
- [ ] Test cache invalidation on role change
- [ ] Test frontend permission gating
- [ ] Deploy to production
