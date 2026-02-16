# Add Remaining Sections to Wathiq Mobile

The mobile app has 12 functional screens already. This plan adds missing detail screens and new sections to match the web app.

## Current State

| Screen | Status | Has API |
|--------|--------|---------|
| Home/Dashboard | ✅ Complete | ✅ |
| Cases List + Details | ✅ Complete | ✅ |
| Calendar/Hearings List | ✅ Complete | ✅ |
| Clients List | ✅ Complete | ✅ |
| Profile | ✅ Complete | ✅ |
| Documents | ✅ Complete | ✅ |
| Tasks | ✅ Complete | ✅ |
| Invoices | ✅ Complete | ✅ |
| Forms | ✅ Complete | ✅ |
| Legal Library | ✅ Complete | ✅ |
| Legal Search | ✅ Complete | ✅ |
| Settings | ✅ Complete | Local |

## Proposed Changes

### Phase 1 — Detail Screens

#### [NEW] ClientDetailsScreen.tsx
`WathiqMobile/src/screens/clients/ClientDetailsScreen.tsx`
- Client info card (name, phone, email, type, nationalId)
- Tab-like sections: Cases, Invoices, Documents
- Edit button → CreateClientScreen
- Uses `clientsApi.getById(id)`

#### [NEW] CreateClientScreen.tsx
`WathiqMobile/src/screens/clients/CreateClientScreen.tsx`
- Form: name, email, phone, clientType, nationalId, address
- Uses `clientsApi.create()` / `clientsApi.update()`

#### [NEW] HearingDetailsScreen.tsx
`WathiqMobile/src/screens/calendar/HearingDetailsScreen.tsx`
- Hearing info: title, date, time, court, case, status, notes
- Status change button
- Uses `hearingsApi.getById(id)`

#### [NEW] CreateHearingScreen.tsx
`WathiqMobile/src/screens/calendar/CreateHearingScreen.tsx`
- Form: title, date, time, court, courtRoom, caseId, notes
- Uses `hearingsApi.create()`

---

### Phase 2 — New Sections

#### [NEW] NotificationsScreen.tsx
`WathiqMobile/src/screens/notifications/NotificationsScreen.tsx`
- List notifications with read/unread status
- Mark as read, mark all as read
- Uses `GET /notifications`

#### [NEW] AccountingScreen.tsx
`WathiqMobile/src/screens/accounting/AccountingScreen.tsx`
- Financial summary cards: revenue, expenses, outstanding
- Recent transactions list
- Uses `GET /accounting/dashboard`

#### [NEW] ReportsScreen.tsx
`WathiqMobile/src/screens/reports/ReportsScreen.tsx`
- Statistics overview cards
- Cases by status, revenue chart
- Uses `GET /reports/summary`

#### [NEW] LegalDocumentsScreen.tsx
`WathiqMobile/src/screens/legal-documents/LegalDocumentsScreen.tsx`
- List legal document drafts
- Uses `GET /legal-documents`

---

### Phase 3 — Navigation Integration

#### [MODIFY] AppNavigator.tsx
- Add imports for all new screens
- Add `ClientDetails`, `CreateClient`, `HearingDetails`, `CreateHearing` to `ScreenStack`
- Add `Notifications`, `Accounting`, `Reports`, `LegalDocuments` to `ScreenStack`

#### [MODIFY] DrawerContent.tsx
- Add new items: الإشعارات, المحاسبة, التقارير, المستندات القانونية
- Add notification badge count

## Verification Plan

### Automated Tests
- Metro bundler compiles without errors
- All screens render without crashes
- API calls connect to correct endpoints
- Navigation between screens works
