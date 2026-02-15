# Phase 41 — Wathiq Mobile App Rebuild (React Native 0.76.6)

## Week 1: Setup & Core (Days 1-5)

### Day 1-2: Project Setup ✅
- [x] Initialize React Native 0.76.6 project (`com.bewathiq.mobile`)
- [x] Install core dependencies (navigation, paper, zustand, react-query, axios)
- [x] Create theme system (colors, typography, spacing, Paper theme)
- [x] Create TypeScript types (all models matching backend)
- [x] Create constants & formatters (Arabic labels, date/currency)
- [x] Create API service with axios interceptors (tenant-aware)
- [x] Create auth store with Zustand + AsyncStorage persistence
- [x] Create 8 API modules (auth, cases, clients, hearings, documents, invoices, legal, forms)
- [x] Create 3 common components (StatCard, LoadingSpinner, EmptyState)
- [x] Verify Metro bundler starts successfully

### Day 3-4: Core Screens ✅
- [x] Login screen (tenant slug + email + password, validation)
- [x] Home/Dashboard screen (stats grid, quick actions, today's hearings, recent cases)
- [x] Cases list screen (search, status filters, rich case cards)
- [x] Case details screen (status, info grid, client, actions, timeline)
- [x] Clients list screen (search, avatars, metadata)
- [x] Calendar/Hearings screen (grouped by date, status filters)
- [x] Profile screen (user info, role badge, settings menu, logout)

### Day 5: Navigation & Auth Flow ✅
- [x] Bottom tab navigator (Home, Cases, Calendar, Clients, Profile)
- [x] Nested stack navigators (Cases stack with details)
- [x] Auth-based routing (Login vs Main app)
- [x] App.tsx entry point (RTL, React Query, Paper, Navigation)
- [x] Legal AI Search screen (keyword/AI mode toggle)

## Week 2: Features (Days 6-10)
- [ ] Call Center integration (WebRTC/SRTP)
- [ ] WhatsApp chat integration
- [ ] Documents management
- [ ] Invoices & billing
- [ ] Forms submissions
- [ ] Push notifications (FCM)

## Week 3: Polish (Days 11-15)
- [ ] Offline support (WatermelonDB)
- [ ] Performance optimization
- [ ] iOS pod install & build
- [ ] Android build & test
- [ ] App Store / Play Store submission
