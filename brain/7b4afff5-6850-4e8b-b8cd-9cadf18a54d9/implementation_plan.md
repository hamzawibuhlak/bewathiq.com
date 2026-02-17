# Feature-Level Toggle Control

تحويل نظام التحكم بالأقسام من مستوى القسم فقط إلى مستوى كل ميزة داخل القسم.

## Proposed Changes

### Data Structure Change

**قبل (حالياً):**
```json
{ "cases": { "enabled": true } }
```

**بعد:**
```json
{
  "cases": {
    "enabled": true,
    "features": {
      "create_edit": true,
      "link_clients": true,
      "track_status": false,
      "workspace": true
    }
  }
}
```

---

### Frontend Constants

#### [MODIFY] [modules.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/constants/modules.constants.ts)

- تحويل `features: string[]` إلى `features: FeatureDefinition[]`
- كل ميزة تحصل على `key` + `labelAr` + `isCore` (اختياري)

```ts
interface FeatureDefinition {
    key: string;
    labelAr: string;
    isCore?: boolean; // لا يمكن تعطيلها
}
```

---

### Frontend Hook

#### [MODIFY] [useModules.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/useModules.ts)

- إضافة `isFeatureEnabled(moduleKey, featureKey): boolean`
- تحديث interface لتشمل `features` object

---

### Backend Service

#### [MODIFY] [module-settings.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/module-settings.service.ts)

- إضافة `updateFeature(tenantId, moduleKey, featureKey, enabled, ...)`
- تعديل `getTenantModules` ليرجع features مع defaults

---

### Backend Controller

#### [MODIFY] [super-admin.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/super-admin/super-admin.controller.ts)

- إضافة `PATCH tenants/:id/modules/:moduleKey/features/:featureKey`

---

### SA Module Control Page

#### [MODIFY] [SAModuleControlPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/SAModuleControlPage.tsx)

- كل ميزة تصبح toggle بدل نص فقط
- عند تعطيل القسم بالكامل، جميع المميزات تعطّل تلقائياً

---

### Frontend API

#### [MODIFY] [superAdmin.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/api/superAdmin.ts)

- إضافة `updateTenantFeature(tenantId, moduleKey, featureKey, enabled, reason?)`

---

### Backend Constants

#### [MODIFY] [modules.constants.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/common/constants/modules.constants.ts)

- نفس تحديث الـ frontend constants (features as objects with keys)
- تحديث `getDefaultModulesByPlan` ليشمل الـ features

## Verification Plan

### Manual Verification
1. تفعيل/تعطيل ميزة واحدة داخل قسم من صفحة SA
2. التأكد إن تعطيل القسم بالكامل يعطّل كل المميزات
3. التأكد إن الـ changelog يسجل تعديلات المميزات
