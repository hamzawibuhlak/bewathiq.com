# Wathiq v1 Production — Walkthrough

## ✅ ما تم بناءه

### Backend
- **Schema**: `CaseNote`, `CaseActivity`, `TaskComment` models
- **TaskStatus**: `OPEN → IN_PROGRESS → DONE → REVIEWED`
- **CaseNotesService**: MEMO (محامي), MANAGER_NOTE (مالك فقط), SHARED
- **CaseActivityService**: Timeline auto-logging
- **TasksService**: Role filtering + status workflow

### Frontend
- **Case Workspace**: `/cases/[id]` with 4 tabs
- **NotesTab**: Create/Edit/Delete notes (role-based visibility)
- **TasksTab**: Status workflow + manager review with comments
- **TimelineTab**: Activity log

---

## 🚀 خطوات النشر

### 1. Backend (على السيرفر)

```bash
ssh root@72.62.135.166
cd /var/www/wathiq/apps/api-server

# نسخ schema والملفات من الجهاز المحلي
# (من جهازك المحلي):
scp apps/api-server/prisma/schema.prisma root@72.62.135.166:/var/www/wathiq/apps/api-server/prisma/
scp -r apps/api-server/src/modules/cases/* root@72.62.135.166:/var/www/wathiq/apps/api-server/src/modules/cases/
scp -r apps/api-server/src/modules/tasks/* root@72.62.135.166:/var/www/wathiq/apps/api-server/src/modules/tasks/

# على السيرفر:
npx prisma migrate dev --name wathiq_v1
npm run build
pm2 restart wathiq-api
```

### 2. Frontend (على السيرفر)

```bash
# من جهازك المحلي:
scp -r apps/web-portal/src root@72.62.135.166:/var/www/wathiq/apps/web-portal/
scp apps/web-portal/src/lib/api.ts root@72.62.135.166:/var/www/wathiq/apps/web-portal/src/lib/

# على السيرفر:
cd /var/www/wathiq/apps/web-portal
npm run build
pm2 restart wathiq-web
```

---

## 🧪 خطوات التحقق (بعد النشر)

### إنشاء قضية تجريبية

```bash
# على السيرفر - إنشاء Client أولاً
curl -X POST http://localhost:3000/api/clients \
  -H "Authorization: Bearer {OWNER_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"name":"عميل تجريبي","email":"test@example.com"}'

# إنشاء Case
curl -X POST http://localhost:3000/api/cases \
  -H "Authorization: Bearer {OWNER_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "title":"قضية تجريبية",
    "clientId":"{CLIENT_ID}",
    "assignedLawyerId":"{LAWYER_USER_ID}"
  }'
```

### تحقق الأدوار

| Role | Login | Cases List | Notes |
|------|-------|------------|-------|
| **OWNER** | ✅ | يرى كل القضايا | MEMO + MANAGER + SHARED |
| **LAWYER** | ✅ | قضاياه فقط | MEMO + SHARED (لا يرى MANAGER) |
| **ACCOUNTANT** | ✅ | No access | N/A |

### تحقق Tasks

1. Login كـ LAWYER → إنشاء task → Status = OPEN
2. LAWYER يضغط "بدء العمل" → IN_PROGRESS
3. LAWYER يضغط "إنهاء" → DONE
4. Login كـ OWNER → يرى task بحالة DONE
5. OWNER يضغط "مراجعة" + يضيف تعليق → REVIEWED

---

## 📂 الملفات الجديدة/المعدلة

### Backend
- `prisma/schema.prisma` — NoteType, CaseNote, CaseActivity, TaskComment
- `src/modules/cases/case-notes.service.ts`
- `src/modules/cases/case-notes.controller.ts`
- `src/modules/cases/case-activity.service.ts`
- `src/modules/cases/dto/case-note.dto.ts`
- `src/modules/cases/cases.module.ts`
- `src/modules/tasks/tasks.service.ts`
- `src/modules/tasks/tasks.controller.ts`
- `src/modules/tasks/tasks.module.ts`
- `src/modules/tasks/dto/create-task.dto.ts`

### Frontend
- `src/lib/api.ts` — Cases, Notes, Tasks, Activities APIs
- `src/app/cases/[id]/page.tsx` — Case Workspace
- `src/app/cases/[id]/components/OverviewTab.tsx`
- `src/app/cases/[id]/components/NotesTab.tsx`
- `src/app/cases/[id]/components/TasksTab.tsx`
- `src/app/cases/[id]/components/TimelineTab.tsx`
