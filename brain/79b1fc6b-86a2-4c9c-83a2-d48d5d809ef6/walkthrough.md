# Pre-Launch Security Activation Checklist

## Status: 📋 READY — Execute Before Public Launch

---

## Phase D1 — Session Security (Approved)

### Migrations
```bash
# 1. Backup database first
pg_dump -U wathiq_user wathiq_db > backup_pre_d1.sql

# 2. Run migration
npx prisma migrate deploy

# 3. Verify Session table exists
npx prisma db pull
```

### Verification
- [ ] Session table created
- [ ] Login creates session record
- [ ] Logout revokes session
- [ ] Token rotation works

---

## Phase D2 — Data Encryption (Design Approved)

### Pre-Migration
- [ ] Generate encryption key: `openssl rand -base64 32`
- [ ] Store key securely (NOT in git)
- [ ] Add `ENCRYPTION_KEY` to `.env.production`

### Migrations
```bash
# 1. Backup database
pg_dump -U wathiq_user wathiq_db > backup_pre_d2.sql

# 2. Add schema changes (when approved)
# 3. Run migration
npx prisma migrate deploy
```

### Verification
- [ ] Encrypt/decrypt test passes
- [ ] New notes fields work
- [ ] No plaintext in DB

---

## Safe Migration Steps

| Step | Action |
|------|--------|
| 1 | Enable maintenance mode |
| 2 | Backup database |
| 3 | Run `prisma migrate deploy` |
| 4 | Verify tables |
| 5 | Restart PM2 |
| 6 | Smoke test |
| 7 | Disable maintenance mode |

---

## Rollback Procedure

```bash
# If migration fails
psql -U wathiq_user wathiq_db < backup_pre_d1.sql
pm2 restart wathiq-api
```

---

**Execute this checklist before public launch.**
