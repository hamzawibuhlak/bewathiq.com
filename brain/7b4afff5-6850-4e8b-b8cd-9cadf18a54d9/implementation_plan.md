# Email OTP Verification for New Registration

Adds email verification via 6-digit OTP when registering a new law firm. The registration flow changes from "register → auto-login" to "register → verify email → login".

## User Review Required

> [!IMPORTANT]
> **The existing SA SMTP Integration page** (`/super-admin/integrations/smtp`) already saves SMTP settings to SystemConfig and has test-email functionality. Per our spec you requested a separate `/admin/settings/smtp` page, but it would duplicate existing functionality. **I propose reusing the existing page** and only adding the backend OTP logic. Let me know if you still want a separate page.

> [!WARNING]
> **Breaking change for registration flow:** After this change, new registrations will NOT receive a JWT token immediately — they must verify email first. Existing tenants are unaffected (they're already `isActive: true`).

---

## Proposed Changes

### Database — Prisma Schema

#### [MODIFY] [schema.prisma](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/prisma/schema.prisma)

1. **Add `registrationStatus` and `emailVerifiedAt` to Tenant model:**
```prisma
registrationStatus  String    @default("ACTIVE")
// Values: PENDING_EMAIL | ACTIVE | SUSPENDED
emailVerifiedAt     DateTime?
```
> Default is `ACTIVE` so existing tenants are unaffected.

2. **Add `EmailVerificationToken` model:**
```prisma
model EmailVerificationToken {
  id         String   @id @default(uuid())
  email      String
  tenantId   String?
  token      String   // bcrypt-hashed OTP
  expiresAt  DateTime // +15 min from creation
  isUsed     Boolean  @default(false)
  attempts   Int      @default(0) // max 5
  createdAt  DateTime @default(now())
  @@index([email])
  @@map("email_verification_tokens")
}
```

---

### Backend — Email Verification Service

#### [NEW] [email-verification.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/auth/email-verification.service.ts)

Methods:
- `generateAndSendOTP(email, tenantId?)` — generates 6-digit code, hashes with bcrypt, saves to DB, sends via existing `EmailService`
- `verifyOTP(email, code)` — finds valid token, checks attempts (≤5), compares with bcrypt, activates tenant/user on success
- `resendOTP(email)` — enforces 60s cooldown, deletes old token, creates new one

OTP HTML template embedded in the service (professional design with Wathiq branding, large OTP display, 15-minute expiry warning).

---

### Backend — Auth Flow Modifications

#### [MODIFY] [auth.service.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/auth/auth.service.ts)

**`register()` changes:**
- Create Tenant with `registrationStatus: 'PENDING_EMAIL'`, `isActive: false`
- Create User with `isActive: false`
- Call `emailVerificationService.generateAndSendOTP(email, tenantId)`
- Return `{ success: true, email, requiresVerification: true }` — **no JWT**

**New `verifyEmail(email, code)` method:**
- Calls `emailVerificationService.verifyOTP()`
- Sets User `isActive: true`, Tenant `registrationStatus: 'ACTIVE'`, `isActive: true`, `emailVerifiedAt: now()`
- Returns JWT tokens for auto-login

**`login()` changes:**
- If tenant `registrationStatus === 'PENDING_EMAIL'`, return `{ requiresVerification: true, email }` instead of error

#### [MODIFY] [auth.controller.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/auth/auth.controller.ts)

Add endpoints:
```
POST /auth/verify-email    { email, code }
POST /auth/resend-otp      { email }
```

#### [MODIFY] [auth.module.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/backend/src/auth/auth.module.ts)

Register `EmailVerificationService` as provider.

---

### Frontend — Verify Email Page

#### [NEW] [VerifyEmailPage.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/pages/auth/VerifyEmailPage.tsx)

6-box OTP input with:
- Auto-focus next input on digit entry
- Backspace returns to previous box
- Paste fills all boxes
- Auto-submit on 6th digit
- 60→0 countdown timer, then "Resend" button
- Shake animation on error
- Remaining attempts display
- "Wrong email? Go back" link

---

### Frontend — Register Flow Modifications

#### [MODIFY] [use-auth.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/use-auth.ts)

Update `useRegister` `onSuccess`:
```diff
- login(response.user, response.accessToken);
- navigate(response.redirectTo);
+ if (response.requiresVerification) {
+   navigate('/verify-email', { state: { email: response.email }, replace: true });
+ } else {
+   login(response.user, response.accessToken);
+   navigate(response.redirectTo);
+ }
```

#### [MODIFY] [App.tsx](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/App.tsx)

Add route:
```tsx
<Route path="/verify-email" element={<VerifyEmailPage />} />
```

#### [MODIFY] [use-auth.ts](file:///Users/hamzabuhlakq/Downloads/succes-mark/projects-2026/wathiq%20system%20projec/watheeq-mvp/frontend/src/hooks/use-auth.ts)

Update `useLogin` to handle `requiresVerification` response (redirect to verify page instead of error).

---

## Verification Plan

### Automated Tests
1. `npm run build` — both backend and frontend compile without errors
2. `npx prisma migrate deploy` — migration runs successfully

### Manual Verification (via browser)
1. Register new account → redirected to `/verify-email`
2. Check email inbox → OTP received with professional template
3. Enter wrong code → error + shake + attempts count
4. Enter correct code → auto-login + dashboard redirect
5. Test resend with cooldown (button disabled for 60s)
6. Existing users → login works normally (unaffected)
