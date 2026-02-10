# Phase 24: HR Management Module

Comprehensive HR system for Watheeq with Saudi Labor Law compliance (GOSI, EOSB, WPS).

## Proposed Changes

### Database Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)
- Add 15 new enums: `Gender`, `MaritalStatus`, `ContractType`, `EmploymentStatus`, `EmployeeDocumentType` (renamed from `DocumentType` to avoid conflict), `AttendanceStatus`, `LeaveStatus`, `PayrollStatus`, `PaymentMethod`, etc.
- Add 12 new models: `Employee`, `Department`, `Position`, `EmployeeDocument`, `Attendance`, `AttendanceSettings`, `LeaveType`, `LeaveRequest`, `LeaveBalance`, `Payroll`, `PayrollSettings`
- Add HR relations to `Tenant` and `User` models

> [!IMPORTANT]
> The existing `DocumentType` enum conflicts with HR's document type. Renaming HR version to `EmployeeDocumentType`.

---

### Backend HR Module

#### [NEW] [employee.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/employee.service.ts)
- CRUD operations, EOSB calculation (Saudi Law), org chart, employee stats, encrypted national IDs

#### [NEW] [attendance.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/attendance.service.ts)
- Clock in/out, late detection with grace period, attendance reports

#### [NEW] [leave.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/leave.service.ts)
- Leave requests, approval workflow, balance tracking, business day calculation

#### [NEW] [payroll.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/payroll.service.ts)
- Monthly payroll generation, GOSI (10%+12%), overtime, deductions, WPS bank file

#### [NEW] [hr.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/hr.controller.ts)
- REST endpoints under `/api/hr/*` using `@Controller('hr')`

#### [NEW] [hr.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/hr/hr.module.ts)

#### [MODIFY] [app.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/app.module.ts)
- Import `HrModule`

---

### Frontend Pages

#### [NEW] `frontend/src/pages/hr/EmployeesListPage.tsx`
#### [NEW] `frontend/src/pages/hr/EmployeeDetailsPage.tsx`
#### [NEW] `frontend/src/pages/hr/AttendancePage.tsx`
#### [NEW] `frontend/src/pages/hr/LeaveManagementPage.tsx`
#### [NEW] `frontend/src/pages/hr/PayrollPage.tsx`

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx) — Add HR routes
#### [MODIFY] [Sidebar.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/components/layout/Sidebar.tsx) — Add HR nav items

## Verification Plan

### Automated
- `npx prisma migrate dev` — verify schema compiles
- `npm run build` — verify backend compiles
- `npm run build` — verify frontend compiles

### Production
- Deploy to server, rebuild container, run migrations
- Verify `/api/hr/employees` returns 200
- Verify HR pages load in browser
