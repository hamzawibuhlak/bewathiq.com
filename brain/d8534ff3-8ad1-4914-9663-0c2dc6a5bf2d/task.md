# Phase 31: Slug Routing & Multi-Step Registration

## Backend
- [x] Database schema: `slug`, `PlanType`, `planStartDate`, `planEndDate` on Tenant
- [x] `register.dto.ts`: new fields (slug, city, licenseNumber, planType)
- [x] `auth.service.ts`: checkSlugAvailability, updated register/login/getMe
- [x] `auth.controller.ts`: GET /auth/check-slug/:slug endpoint
- [x] `jwt.strategy.ts`: tenantSlug in JWT payload

## Frontend
- [x] `types/index.ts`: Tenant.slug, Tenant.planType, RegisterRequest.slug/city/licenseNumber/planType
- [x] `auth.store.ts`: getTenantSlug() helper, tenant interface with slug+planType
- [x] `use-auth.ts`: slug-based redirect on login/register success
- [x] `App.tsx`: all routes under `/:slug/*`, SlugRedirect for backward compat
- [x] `Sidebar.tsx`: slug-aware paths via useParams
- [x] `Header.tsx`: slug-aware quick actions, settings nav, messages link
- [x] `Breadcrumbs.tsx`: slug-aware home link, skip slug in auto-generated crumbs
- [x] `OwnerLayout.tsx`: slug-aware back-to-dashboard link
- [x] `RegisterPage.tsx`: 3-step wizard (office info + slug check, owner details, plan selection)

## Deployment
- [ ] Run `npx prisma generate` to regenerate Prisma client (resolves backend lint errors)
- [ ] Run `npx prisma migrate dev` to apply schema changes
- [ ] Test registration flow end-to-end
- [ ] Test slug-based routing
