# Phase 31 — صفحة إعداد الكول سنتر ✅

## ملخص المشكلة

صفحة **ربط وإعداد الكول سنتر** كانت غير موجودة رغم أن الـ Backend APIs جاهزة.

## ما تم إنجازه

### ملف جديد
- [CallCenterSetupPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/calls/CallCenterSetupPage.tsx) — صفحة إعداد الكول سنتر تتضمن:
  - حالة ربط السنترال (متصل/غير متصل)
  - جدول Extensions الموظفين مع CRUD كامل
  - نافذة تعيين Extension جديد
  - دليل الربط

### ملفات معدلة
- [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx) — إضافة route `calls/setup`
- [IntegrationsPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/owner/IntegrationsPage.tsx) — تحويل `CALL_CENTER` → `calls/setup`
- [deploy.md](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/.agent/workflows/deploy.md) — تحديث لـ SSH بدون كلمة مرور

## التحقق

| الفحص | النتيجة |
|-------|---------|
| TypeScript Build | ✅ `tsc --noEmit` — لا أخطاء |
| Docker Build | ✅ Backend + Frontend built |
| Deployment | ✅ All 4 containers healthy |

```
NAMES                   STATUS
watheeq_frontend_prod   Up 27 seconds (healthy)
watheeq_backend_prod    Up 27 seconds (healthy)
watheeq_postgres_prod   Up 43 hours (healthy)
watheeq_redis_prod      Up 43 hours (healthy)
```

## كيفية الوصول

1. **من الشريط الجانبي** → "مركز الاتصالات" (لمشاهدة سجل المكالمات)
2. **من صفحة الشركة** → الربط والتكاملات → كرت "مركز الاتصال" → إعداد (لإعداد Extensions)
3. **رابط مباشر**: `/{slug}/calls/setup`
