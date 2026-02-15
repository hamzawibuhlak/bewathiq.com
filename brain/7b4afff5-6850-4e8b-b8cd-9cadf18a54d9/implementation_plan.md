# Phase 39B: Super Admin — Legal Content Management & AI Configuration

The Legal AI Search page is deployed but empty — no legal content exists in the database yet, and the Anthropic API key is not configured. This phase adds a Super Admin management page to populate and manage legal content (regulations, precedents, terms), and configures the AI connection.

## Proposed Changes

### 1. Server Configuration (Manual Step)

Add `ANTHROPIC_API_KEY` to the production `.env` file. This enables Claude AI responses in the Legal Search.

---

### 2. Backend — CRUD Endpoints for Precedents & Terms

#### [MODIFY] [legal-library.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/legal-library/legal-library.service.ts)

Add missing CRUD methods:
- `createPrecedent(data)` — Create a legal precedent (court decision)
- `updatePrecedent(id, data)` — Update existing precedent
- `deletePrecedent(id)` — Delete a precedent
- `createTerm(data)` — Create a legal glossary term
- `updateTerm(id, data)` — Update existing term
- `deleteTerm(id)` — Delete a term
- `updateRegulation(id, data)` — Update existing regulation (create already exists)

#### [MODIFY] [legal-library.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/legal-library/legal-library.controller.ts)

Add corresponding REST endpoints:
- `POST /precedents`, `PATCH /precedents/:id`, `DELETE /precedents/:id`
- `POST /terms`, `PATCH /terms/:id`, `DELETE /terms/:id`
- `PATCH /regulations/:id`

---

### 3. Frontend — Super Admin Legal Content Page

#### [NEW] [SALegalContentPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin/SALegalContentPage.tsx)

A full-featured management page with 3 tabs:

| Tab | Content | Actions |
|-----|---------|---------|
| الأنظمة واللوائح | Regulations list with articles | Add / Edit / Delete |
| السوابق القضائية | Precedents (court decisions) | Add / Edit / Delete |
| المصطلحات القانونية | Legal terms/glossary | Add / Edit / Delete |

Each tab has:
- Search and filter capabilities
- Modal forms for Add/Edit
- Bulk content stats at top
- Inline delete with confirmation

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)

Add route: `<Route path="legal-content" element={<SALegalContentPage />} />`

#### [MODIFY] [SALayout](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/super-admin)

Add "المحتوى القانوني" nav item with BookOpen icon to the Super Admin sidebar.

---

## Verification Plan

### Automated
- TypeScript compilation (frontend + backend)
- Deploy to production and verify

### Manual
- Open Super Admin panel → navigate to Legal Content page
- Add regulation with articles, precedent, and term
- Navigate to Legal AI Search page and verify content appears in search results
