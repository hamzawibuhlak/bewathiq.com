# Phase 31: Slug Routing & Multi-Step Registration — Walkthrough

## What Changed

### Backend
| File | Change |
|------|--------|
| `schema.prisma` | Added `PlanType` enum, `slug` (unique), `planType`, `planStartDate`, `planEndDate` to Tenant |
| `register.dto.ts` | Added `slug`, `city`, `licenseNumber`, `planType` fields with validation |
| `auth.service.ts` | New `checkSlugAvailability()`, updated `register`/`login`/`getMe`/`generateToken` with slug support |
| `auth.controller.ts` | New `GET /auth/check-slug/:slug` endpoint |
| `jwt.strategy.ts` | `tenantSlug` added to JWT payload |

### Frontend
| File | Change |
|------|--------|
| `types/index.ts` | `Tenant.slug`, `Tenant.planType`, `RegisterRequest` with slug/city/licenseNumber/planType |
| `auth.store.ts` | `getTenantSlug()` helper, tenant interface with slug+planType |
| `use-auth.ts` | Login/register redirect uses `response.redirectTo` → `/:slug/dashboard` |
| `App.tsx` | All app routes under `/:slug/*`, `SlugRedirect` for backward compat (`/dashboard` → `/:slug/dashboard`) |
| `Sidebar.tsx` | All nav paths are relative, prepended with `/${slug}/` via `useParams` |
| `Header.tsx` | Quick actions, settings nav, messages link use slug prefix |
| `Breadcrumbs.tsx` | Home link → `/:slug/dashboard`, slug segment skipped in auto-generated breadcrumbs |
| `OwnerLayout.tsx` | Back-to-dashboard link → `/:slug/dashboard` |
| `RegisterPage.tsx` | Complete rewrite: 3-step wizard with real-time slug availability check |

## URL Structure

```
Before: /dashboard, /cases, /settings/profile
After:  /watheeq-law/dashboard, /watheeq-law/cases, /watheeq-law/settings/profile
```

## Registration Flow
1. **Step 1** — Office name, slug (with instant availability check), city, license number
2. **Step 2** — Owner name, email, phone, password
3. **Step 3** — Plan selection (Basic/Professional/Enterprise)

## Remaining Steps
- Run `npx prisma generate` + `npx prisma migrate dev` on the server
- Test end-to-end registration and slug routing
