# Phase 42: Super Admin Module Control

Enable Super Admin to show/hide modules and pages per tenant, with plan-based defaults and dynamic sidebar filtering.

## Proposed Changes

### Database (Prisma Schema)

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

Add two new models:

- **`TenantModuleSettings`** — 1:1 with Tenant, stores a JSON `modules` field holding `{ moduleKey: { enabled: boolean, pages: {} } }`
- **`ModuleChangeLog`** — audit trail for every module enable/disable action by Super Admin

Add relation fields to `Tenant` and `SuperAdminUser`.

---

### Backend — Module Constants & Service

#### [NEW] [modules.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/common/constants/modules.constants.ts)

Define `MODULES` registry and `PLAN_DEFAULTS` mapping (BASIC/PROFESSIONAL/ENTERPRISE → enabled module keys).

#### [NEW] [module-settings.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/module-settings.service.ts)

Methods:
- `getTenantModules(tenantId)` — merges saved settings with plan defaults
- `updateModule(tenantId, moduleKey, enabled, adminId, reason?)` — toggle whole module
- `bulkUpdate(tenantId, updates[], adminId)` — batch update
- `applyPlan(tenantId, planType, adminId)` — reset to plan defaults
- `getChangeLog(tenantId)` — fetch audit entries
- `getMyModules(tenantId)` — tenant-side endpoint (returns enabled modules only)

---

### Backend — Controller & Module Registration

#### [MODIFY] [super-admin.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin.controller.ts)

Add endpoints under `/super-admin/tenants/:id/modules`:
- `GET` — get tenant module settings
- `PATCH /:moduleKey` — enable/disable module
- `PATCH /bulk` — batch update
- `POST /apply-plan` — apply plan defaults
- `GET /change-log` — fetch change history

#### [MODIFY] [super-admin.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin.module.ts)

Register `ModuleSettingsService` as provider.

---

### Backend — Tenant-Side Endpoint

#### [MODIFY] [auth or tenant controller]

Add `GET /api/my-modules` — returns the calling user's tenant module settings. Used by frontend to filter sidebar/routes.

---

### Frontend — Module Constants & Context

#### [NEW] [modules.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/constants/modules.constants.ts)

Mirror of backend `MODULES` with `key`, `nameAr`, `icon`, `path`, `category`, `isCore`, `isPremium`, `defaultEnabled`.

#### [NEW] [useModules.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/useModules.ts)

Custom hook that:
- Fetches `/api/my-modules` on login
- Caches in zustand store (5-min stale)
- Exposes `isModuleEnabled(key)` helper

---

### Frontend — Sidebar Integration

#### [MODIFY] [Sidebar.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/layout/Sidebar.tsx)

Add a `moduleKey` field to each `NavItem` and `NavGroup`. In `filterItems()`, add check: if `moduleKey` is set and `!isModuleEnabled(moduleKey)` → hide item. This is the minimal-impact integration: existing role/permission checks stay, module check is layered on top.

---

### Frontend — Super Admin Module Management UI

#### [NEW] [SAModuleControlPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/SAModuleControlPage.tsx)

Standalone page accessible from `SATenantDetailsPage` via a "إدارة الأقسام" button. Features:
- All modules listed with toggles (grouped by category)
- Quick-apply plan buttons (BASIC/PROFESSIONAL/ENTERPRISE)
- Change reason input
- Change log viewer
- Core module protection (can't disable)

#### [MODIFY] [superAdmin.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/superAdmin.ts)

Add API methods for module management endpoints.

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)

Add route `/super-admin/tenants/:id/modules` pointing to `SAModuleControlPage`.

---

### Frontend — Disabled Module Page

#### [NEW] [ModuleDisabledPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/ModuleDisabledPage.tsx)

Shown when user navigates to a disabled module's URL directly. Clean Arabic message + "ترقية الباقة" CTA.

---

## Design Decisions

> [!IMPORTANT]
> - **No page-level control in V1** — The user spec includes per-page toggles, but implementing module-level toggles first is simpler and covers 95% of use cases. Page-level can be added later without schema changes (the JSON structure supports it).
> - **Store in zustand, not React Context** — Consistent with existing auth store pattern. No need for a Provider wrapper.
> - **Plan enum uses `PROFESSIONAL`** not `PRO` — matching existing `PlanType` enum in schema.

## Verification Plan

### Automated Tests
- `npx prisma migrate deploy` — verify migration runs
- Backend: verify `/super-admin/tenants/:id/modules` endpoints return correct data
- Frontend: verify sidebar hides disabled modules
- Browser test: toggle a module → verify sidebar updates

### Manual Verification
- Deploy to production → SA enables/disables modules for a tenant → tenant sees changes
