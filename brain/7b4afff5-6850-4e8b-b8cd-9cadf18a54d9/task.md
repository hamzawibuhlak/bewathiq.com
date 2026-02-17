# Feature-Level Toggle Control

- [x] Update frontend constants (`modules.constants.ts`) — `FeatureDefinition` objects with key/labelAr/isCore
- [x] Update backend constants (`modules.constants.ts`) — add features array, update `getDefaultModulesByPlan`
- [x] Backend service (`module-settings.service.ts`) — add `updateFeature()`, deep merge, cascade logic
- [x] Backend controller (`super-admin.controller.ts`) — add `PATCH .../features/:featureKey` endpoint
- [x] Frontend API (`superAdmin.ts`) — add `updateTenantFeature()` method
- [x] Frontend hook (`useModules.ts`) — add `isFeatureEnabled(moduleKey, featureKey)`
- [x] SA Module Control Page — feature toggle UI with expand/collapse, lock for core features
- [x] Fix TS build error (Lock icon `title` prop)
- [x] Deploy to production
- [x] Verify on production — feature toggles visible and functional
