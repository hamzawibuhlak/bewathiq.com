# Feature-Level Toggle Control

## Constants
- [ ] Update frontend `modules.constants.ts` — features as objects with keys
- [ ] Update backend `modules.constants.ts` — features as objects + `getDefaultModulesByPlan` with features

## Backend
- [ ] Update `module-settings.service.ts` — add `updateFeature()`, merge feature defaults
- [ ] Update `super-admin.controller.ts` — add feature toggle endpoint

## Frontend
- [ ] Update `useModules.ts` — add `isFeatureEnabled(moduleKey, featureKey)`
- [ ] Update `superAdmin.ts` — add `updateTenantFeature()` API method
- [ ] Update `SAModuleControlPage.tsx` — feature toggles in expanded cards

## Deploy & Verify
- [ ] Deploy to production
- [ ] Verify feature toggle works from SA page
