# Phase 39: وثيق AI — Legal AI Search Engine

## Part 1 — Database Schema Enhancements
- [x] Extend `LegalSearchLog` with AI-specific fields (queryType, aiResponse, aiTokensUsed, responseTime, cached)
- [x] Add `LegalArticleNote` model (private notes/highlights on articles)
- [x] Add `LegalAIUsage` model (monthly AI quota tracking per tenant/user)
- [ ] Extend `LegalRegulation` with `officialUrl`, `pdfUrl` fields
- [x] Extend `RegulationArticle` with `keywords`, `chapter`, `section` fields
- [x] Extend `CaseLegalReference` with `purpose`, `highlightedText`, `usedInDocument` fields
- [x] Run prisma generate + verify

## Part 2 — Backend: AI Search Service
- [x] Install `@anthropic-ai/sdk` npm package
- [x] Create `LegalAIService` with Claude integration
  - [x] `askAI()` — AI question answering with context from existing DB
  - [x] `buildContext()` — Build context from search results
  - [x] AI system prompt for Saudi legal expert
  - [x] Response caching via existing CacheService
  - [x] AI usage quota tracking
- [x] Enhance `LegalLibraryService.aiSearch()` with real AI
- [x] Add article notes CRUD to LegalAIService
- [x] Add AI usage stats endpoint
- [x] Update LegalLibraryController with new endpoints

## Part 3 — Frontend: Legal AI Search Page
- [x] Create `frontend/src/pages/legal-library/LegalAISearchPage.tsx`
- [x] Add AI search / keyword search toggle
- [x] Display AI answer with confidence bar, citations, source counts
- [x] Add example queries grid
- [x] Add API methods (`aiSearch`, `getAIUsage`, `createArticleNote`, etc.)
- [x] Add route in `App.tsx`
- [x] Add sidebar link (البحث الذكي / Sparkles icon)to existing legal library

## Part 4 — Verify + Build
- [x] Run prisma generate
- [x] TypeScript compile check (frontend)
- [x] Deploy to production
- [x] Run `prisma db push` on production
