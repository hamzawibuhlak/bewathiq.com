# Drawer Navigation + Missing Screens for WathiqMobile

## Goal
Replace the current bottom-tab-only navigation with a **Drawer (sidebar) + Bottom Tabs** pattern, matching the web frontend's sidebar structure. Add missing screens for key sections.

## Proposed Changes

### Navigation Architecture

```
Drawer (sidebar) ← NEW
├── الرئيسية (Dashboard) — existing HomeScreen
├── إدارة العمل (Work Management)
│   ├── العملاء — existing ClientsListScreen
│   ├── القضايا — existing CasesListScreen → CaseDetailsScreen
│   ├── الجلسات — existing CalendarScreen
│   ├── المستندات — NEW DocumentsScreen
│   ├── المهام — NEW TasksScreen
│   ├── المكتبة القانونية — NEW LegalLibraryScreen
│   ├── البحث الذكي — existing LegalSearchScreen
│   └── النماذج — NEW FormsScreen
├── التواصل (Communication)
│   └── الرسائل — NEW placeholder
├── المالية (Finance)
│   ├── الفواتير — NEW InvoicesScreen
│   └── المصروفات — NEW placeholder
├── الإعدادات (Settings)
│   └── الملف الشخصي — existing ProfileScreen
└── تسجيل الخروج (Logout)
```

### Component Changes

---

#### [NEW] [DrawerContent.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/WathiqMobile/src/navigation/DrawerContent.tsx)
- Custom drawer content with user info header, collapsible nav groups, and logout
- Styled to match the web sidebar (Arabic RTL, purple primary color)

#### [MODIFY] [AppNavigator.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/WathiqMobile/src/navigation/AppNavigator.tsx)
- Wrap the existing MainTabNavigator inside a Drawer Navigator
- The bottom tabs keep: Home, Cases, Hearings, Clients, Profile (5 core tabs)
- The drawer adds access to everything else: Documents, Tasks, Invoices, Forms, Legal Library, Settings, Logout

#### New Screens (placeholder with title + "coming soon" for now):

| Screen | File | API Module |
|--------|------|------------|
| DocumentsScreen | `screens/documents/DocumentsScreen.tsx` | `api/documents.ts` ✅ |
| TasksScreen | `screens/tasks/TasksScreen.tsx` | needs new |
| InvoicesScreen | `screens/invoices/InvoicesScreen.tsx` | `api/invoices.ts` ✅ |
| FormsScreen | `screens/forms/FormsScreen.tsx` | `api/forms.ts` ✅ |
| LegalLibraryScreen | `screens/legal/LegalLibraryScreen.tsx` | `api/legal.ts` ✅ |
| SettingsScreen | `screens/settings/SettingsScreen.tsx` | N/A |

## Verification Plan
- Build the app (`react-native run-ios`)
- Verify drawer opens with hamburger menu icon
- Verify all drawer items navigate correctly
- Verify bottom tabs still work
- Take screenshots to confirm
