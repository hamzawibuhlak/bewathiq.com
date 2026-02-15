# Phase 38: Dynamic Forms System — نماذج تفاعلية للعملاء

A Google Forms-like system for creating dynamic forms, sharing them publicly with clients, and collecting responses.

## Proposed Changes

### Database Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

5 new models: `Form`, `FormField`, `FormSubmission`, `FormFieldAnswer`, `FormTemplate`. Relations to existing `Tenant`, `User`, `Case` models. Supports code generation (`test_FM00001`), slug-based public access, and conditional logic via JSON fields.

---

### Backend — Forms Module

#### [MODIFY] [entity-code.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/common/services/entity-code.service.ts)
Add `'form'` to the entity type union and config map (`FM` prefix, 5 digits).

#### [NEW] `backend/src/forms/forms.module.ts`
NestJS module importing `EntityCodeModule`, registering controller + service.

#### [NEW] `backend/src/forms/forms.service.ts`
- `create()` — generate code, unique slug, create form with fields
- `findAll()` — list forms with submission counts, search/filter
- `findById()` — get form with fields for editing
- `findBySlug()` — public endpoint, strip sensitive data
- `update()` — update form + replace fields
- `delete()` — only if no submissions
- `submitForm()` — validate required fields, save answers, return success message
- `getSubmissions()` — list submissions with answers and filters
- `updateSubmissionStatus()` — mark as reviewed/processed

#### [NEW] `backend/src/forms/forms.controller.ts`
Protected endpoints under `/forms` + public endpoints under `/public/forms`.

#### [MODIFY] [app.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/app.module.ts)
Import `FormsModule`.

---

### Frontend — API Layer

#### [NEW] `frontend/src/api/forms.ts`
API client: `getAll`, `getById`, `create`, `update`, `delete`, `getBySlug` (public), `submitPublic`, `getSubmissions`, `updateSubmissionStatus`.

#### [NEW] `frontend/src/hooks/useForms.ts`
React Query hooks: `useForms`, `useForm`, `useCreateForm`, `useUpdateForm`, `useDeleteForm`, `useFormSubmissions`, `useSubmitPublicForm`.

---

### Frontend — Pages

#### [NEW] `frontend/src/pages/forms/FormsListPage.tsx`
List all forms with submission count, status badge (active/inactive), search, and create button.

#### [NEW] `frontend/src/pages/forms/FormBuilderPage.tsx`
Main builder UI with 3 tabs:
- **Build**: Left sidebar with field type picker, center canvas with drag & drop sortable fields, right sidebar with field property editor
- **Preview**: Live preview of the form as clients would see it
- **Settings**: Form title, description, notifications, success message, theme

> [!NOTE]
> We will **not** use `@dnd-kit` (requires npm install). Instead, we'll use a simpler manual drag approach with HTML5 drag-and-drop + `arrayMove` utility, keeping it dependency-free.

#### [NEW] `frontend/src/pages/forms/FormSubmissionsPage.tsx`
Table/card view of all submissions for a specific form, with status filter and detail modal.

#### [NEW] `frontend/src/pages/public/PublicFormPage.tsx`
Standalone public page (no auth, no sidebar) for clients to fill and submit forms. Responsive, styled with the form's accent color.

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)
Add routes:
```
/:slug/forms → FormsListPage
/:slug/forms/new → FormBuilderPage
/:slug/forms/:id → FormBuilderPage (edit mode)
/:slug/forms/:id/submissions → FormSubmissionsPage
/f/:slug → PublicFormPage (outside ProtectedRoute)
```

#### [MODIFY] Sidebar
Add "النماذج" link to sidebar navigation.

---

## Scope Decisions

> [!IMPORTANT]
> To keep this manageable, the following are **deferred** to a future phase:
> - Signature pad field (complex canvas)
> - Email notifications on submission (no MailService exists)
> - CSV/Excel export of submissions
> - Form templates system
> - Rate limiting on public submit
>
> **Included in this phase**: 10 field types (shortText, longText, email, phone, number, date, dropdown, radio, checkbox, fileUpload), form builder with drag-and-drop, public form page, submissions viewer, conditional logic support (data saved but UI simplified).

## Verification Plan

### Automated Tests
- Test backend endpoints via browser: create form, submit publicly, view submissions
- Verify form builder drag & drop works in browser

### Manual Verification
- Create a form with multiple field types
- Open the public link and submit as a client
- View the submission in the dashboard
