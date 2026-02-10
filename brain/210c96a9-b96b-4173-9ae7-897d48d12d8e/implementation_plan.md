# Wathiq v1 Production — Implementation Plan

## Goal
Build production-ready Case Workspace with:
- Case Notes (Lawyer Memos, Manager Notes, Shared Notes)
- Tasks with status workflow (OPEN → DONE → REVIEWED)
- Activity Timeline (auto-logged events)
- Real permissions (LAWYER: own cases only, OWNER: all, ACCOUNTANT: none)

---

## Current State Analysis

### ✅ Already Exists
- `Case` model with `assignedLawyerId`
- `CasesService` with role-based filtering (`LAWYER` sees only assigned cases)
- `Task` model with `assignedToUserId` and status (TODO, IN_PROGRESS, DONE)
- `TasksService` basic CRUD (no role filtering)
- Auth guards, tenant isolation, encryption utilities

### ❌ Missing
- `CaseNote` model (type: MEMO, MANAGER_NOTE, SHARED)
- `CaseActivity` model (timeline logging)
- `TaskComment` model for manager review comments
- Task status `REVIEWED`
- Tasks role filtering (LAWYER sees only own case tasks)
- Frontend Case workspace

---

## Proposed Changes

### Backend

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq-app-project/apps/api-server/prisma/schema.prisma)
- Add `NoteType` enum: `MEMO`, `MANAGER_NOTE`, `SHARED`
- Add `CaseNote` model (id, caseId, authorId, type, content, createdAt)
- Add `CaseActivity` model (id, caseId, userId, action, details, createdAt)
- Add `TaskComment` model (id, taskId, authorId, content, createdAt)
- Modify `TaskStatus` enum: add `REVIEWED`

---

#### [NEW] [case-notes.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq-app-project/apps/api-server/src/modules/cases/case-notes.service.ts)
- `create(caseId, type, content, authorId, officeId)`
- `findByCaseId(caseId, userId, userRole)` — filter by type visibility
- `update(noteId, content, userId)`
- `delete(noteId, userId)`

#### [NEW] [case-activity.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq-app-project/apps/api-server/src/modules/cases/case-activity.service.ts)
- `log(caseId, userId, action, details)` — auto-log events
- `getByCaseId(caseId)` — list activity timeline

#### [MODIFY] [tasks.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq-app-project/apps/api-server/src/modules/tasks/tasks.service.ts)
- Add role filtering (LAWYER sees only own case tasks)
- Add status transition validation: OPEN → DONE (assignee) → REVIEWED (manager)
- Add comments support

---

### Frontend

#### [MODIFY] [cases/[id]/page.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq-app-project/apps/web-portal/src/app/cases/[id]/page.tsx)
- Rebuild as Case Workspace with tabs:
  - Overview (client info)
  - Notes (memos, manager, shared)
  - Tasks
  - Timeline

#### [NEW] Case notes components
- NotesTab with 3 sub-tabs (Memos, Manager, Shared)
- Note editor (create/edit/save)

#### [NEW] Tasks components
- TaskList with status badges
- Status change buttons
- Manager comment panel

---

## Verification Plan

### API Tests (curl commands on server)

1. **Create Case Note**
```bash
curl -X POST http://localhost:3000/api/cases/{caseId}/notes \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"type":"MEMO","content":"Test memo"}'
```

2. **Get Case Notes (filter by type)**
```bash
curl http://localhost:3000/api/cases/{caseId}/notes \
  -H "Authorization: Bearer {token}"
```

3. **Create Task with comment**
```bash
curl -X POST http://localhost:3000/api/tasks \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"title":"Task 1","caseId":"{caseId}","assignedToUserId":"{lawyerId}"}'
```

4. **Update Task status to DONE**
```bash
curl -X PATCH http://localhost:3000/api/tasks/{taskId} \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"status":"DONE"}'
```

5. **Manager marks REVIEWED with comment**
```bash
curl -X PATCH http://localhost:3000/api/tasks/{taskId} \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"status":"REVIEWED","comment":"Approved"}'
```

---

### Manual Verification (Role-based)

#### Test OWNER (hamza2hb@gmail.com)
1. Login → Navigate to Cases
2. Should see ALL cases
3. Open any case → See all notes tabs
4. Can add MANAGER_NOTE
5. Can review tasks (mark REVIEWED)

#### Test LAWYER (hambutrade@gmail.com)
1. Login → Navigate to Cases
2. Should see ONLY assigned cases
3. Open assigned case → See only MEMO and SHARED tabs
4. Cannot see MANAGER_NOTE tab
5. Can mark own tasks as DONE, cannot mark REVIEWED

#### Test ACCOUNTANT (wasmaltheeqa.lwr@gmail.com)
1. Login → Navigate to Cases
2. Should see EMPTY list or access denied
3. Cannot access any case workspace

---

## Implementation Order

1. Schema changes + migration
2. CaseNote service + controller
3. CaseActivity service
4. Tasks service updates
5. Frontend Case workspace
6. Build + Deploy
7. Verify all roles
