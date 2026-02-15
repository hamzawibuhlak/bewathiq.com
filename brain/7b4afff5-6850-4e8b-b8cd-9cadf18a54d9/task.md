# Phase 38: Dynamic Forms System

## Part 1 — Database + Backend Foundation
- [x] Add Prisma models: `Form`, `FormField`, `FormSubmission`, `FormFieldAnswer`, `FormTemplate`
- [x] Add `form` entity type to `EntityCodeService.generateFlatCode`
- [x] Create `FormsModule` + `FormsService` + `FormsController`
  - [x] CRUD endpoints for forms
  - [x] Public endpoints: `GET /public/forms/:slug` + `POST /public/forms/:slug/submit`
  - [x] Submissions endpoints: list, view, update status
- [x] Register module in `AppModule`

## Part 2 — Frontend: API + Hooks + Routes
- [x] Create `frontend/src/api/forms.ts` with all API methods
- [x] Create `frontend/src/hooks/useForms.ts` with React Query hooks
- [x] Add routes in `App.tsx`: forms, forms/new, forms/:id, forms/:id/submissions
- [x] Add public route: `/f/:slug` for form submission
- [x] Add sidebar navigation link

## Part 3 — Frontend: Forms List Page
- [x] Create `FormsListPage.tsx` — list all forms with stats, search, filters

## Part 4 — Frontend: Form Builder
- [x] Create `FormBuilderPage.tsx` — main builder UI with tabs (Build/Preview/Settings)
- [x] `FieldTypePicker` component — sidebar with draggable field types
- [x] `SortableField` component — drag & drop field cards
- [x] `FieldEditor` sidebar — edit selected field properties
- [x] `FormPreview` component — live preview tab
- [x] `FormSettings` component — settings tab

## Part 5 — Frontend: Public Form + Submissions
- [x] Create `PublicFormPage.tsx` — public form rendering + submission
- [x] Create `FormSubmissionsPage.tsx` — view submissions for a form

## Part 6 — Verify + Deploy
- [x] Run `prisma generate` ✅
- [x] Frontend build ✅
- [ ] Deploy to production server
- [ ] Run `prisma db push` / SQL migration
- [ ] Test form creation, submission, and viewing
