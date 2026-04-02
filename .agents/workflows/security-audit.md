---
description: Run a comprehensive security audit across backend, frontend, dependencies, and infrastructure
---

# Security Audit Workflow

## When to Use
- Before every production release
- After adding authentication or authorization changes
- When integrating a new third-party service
- As a regular monthly security review
- After a security incident

## Steps

### 1. Dependency Vulnerability Scan

#### Backend
```bash
cd backend
pip install pip-audit
pip-audit --desc --fix
```
- Review all findings. Fix **critical** and **high** severity immediately.
- For **medium/low**, create tickets and fix in the next sprint.

#### Frontend
```bash
cd frontend
npm audit
npm audit fix
```
- Run `npm audit --json` for machine-readable output.
- Never use `npm audit fix --force` without reviewing the changes.

### 2. Secrets Scan
Check for accidentally committed secrets:
```bash
# Install trufflehog or gitleaks
trufflehog git file://. --only-verified
# or
gitleaks detect --source . --verbose
```
- [ ] No API keys, tokens, or passwords in source code
- [ ] No secrets in Dockerfiles or CI config
- [ ] `.env` files are listed in `.gitignore`
- [ ] `.env.example` files contain only placeholder values

### 3. Authentication & Authorization Review
Consult `.agents/rules/security.md`:

- [ ] **Password hashing:** bcrypt with ≥12 rounds (check `core/security.py`)
- [ ] **JWT tokens:** Access tokens expire in ≤30 minutes
- [ ] **Refresh tokens:** Stored in database, revocable
- [ ] **Protected routes:** All non-public endpoints use `Depends(get_current_user)`
- [ ] **Role-based access:** Admin endpoints check user roles
- [ ] **Rate limiting:** Auth endpoints limited (≤10 req/min per IP)
- [ ] **Account lockout:** Brute force protection after N failed attempts

### 4. Input Validation Review
- [ ] All API endpoints use Pydantic schemas for request validation
- [ ] No raw SQL queries (all via SQLAlchemy ORM)
- [ ] File upload endpoints validate: file type, file size, filename sanitization
- [ ] No `dangerouslySetInnerHTML` without DOMPurify sanitization
- [ ] URL parameters validated and sanitized
- [ ] No user input used directly in file paths (path traversal prevention)

### 5. CORS & Headers Review
Check `backend/app/main.py`:
- [ ] `allow_origins` lists specific origins (not `["*"]`)
- [ ] `allow_credentials` is `False` unless specifically needed
- [ ] `allow_methods` restricted to needed methods

Check security headers (via middleware or reverse proxy):
- [ ] `Strict-Transport-Security` → `max-age=31536000; includeSubDomains`
- [ ] `X-Content-Type-Options` → `nosniff`
- [ ] `X-Frame-Options` → `DENY`
- [ ] `Content-Security-Policy` → `default-src 'self'`
- [ ] `Referrer-Policy` → `strict-origin-when-cross-origin`

### 6. Database Security Review
- [ ] Database user has **minimum required privileges** (no superuser for app)
- [ ] Connection uses SSL in production
- [ ] No raw SQL string interpolation anywhere in codebase
- [ ] Sensitive columns (PII) are identified and documented
- [ ] Soft-delete implemented for user data (GDPR compliance)

### 7. Logging & Monitoring Review
- [ ] All auth events logged (login success/failure, token refresh, password reset)
- [ ] All 403/401 responses logged
- [ ] No sensitive data in logs (passwords, tokens, full credit cards, PII)
- [ ] Logs include request IDs for tracing
- [ ] Alerting configured for: error spike, auth failure spike, unusual traffic patterns

### 8. Document Findings
Create a security audit report:
```markdown
## Security Audit — <Date>

### Summary
- Dependencies: X critical, Y high, Z medium
- Secrets scan: ✅ Clean / ❌ Found N issues
- Auth review: ✅ Pass / ❌ N findings

### Findings
| # | Severity | Category | Finding | Remediation | Status |
|---|---|---|---|---|---|
| 1 | Critical | Deps | lodash CVE-2021-XXXXX | Update to 4.17.21 | Open |
| 2 | High | Auth | No rate limiting on /login | Add rate limiter | Open |

### Action Items
- [ ] Fix all critical findings before next release
- [ ] Create tickets for high/medium findings
- [ ] Schedule re-audit after fixes are deployed
```

### 9. Incident Response Reminder
If a vulnerability is actively exploited:
1. **Contain:** Disable the affected endpoint or revoke compromised credentials.
2. **Communicate:** Notify the team lead and security contact.
3. **Fix:** Apply the hotfix (see `hotfix.md` workflow).
4. **Recover:** Restore from backup if data was compromised.
5. **Post-mortem:** Document the incident and prevention measures.
