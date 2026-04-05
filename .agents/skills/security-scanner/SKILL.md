---
name: security-scanner
description: Audit dependencies, secrets handling, auth, input validation, CORS, headers, logging, and operational security controls. Use before releases, after auth or third-party changes, or when investigating elevated security risk.
---

# Security Scanner

## Steps

### 1. Dependency Vulnerability Scan

#### Backend
```bash
cd backend
pip install pip-audit
pip-audit --desc --fix
```

- Fix critical and high severity findings immediately.
- Create tickets for medium and low severity findings.

#### Frontend
```bash
cd frontend
npm audit
npm audit fix
```

- Use `npm audit --json` for machine-readable output.
- Never use `npm audit fix --force` without reviewing the dependency impact.

### 2. Secrets Scan
Check for accidentally committed secrets:

```bash
trufflehog git file://. --only-verified
# or
gitleaks detect --source . --verbose
```

- [ ] No API keys, tokens, or passwords in source code
- [ ] No secrets in Dockerfiles or CI config
- [ ] `.env` files are listed in `.gitignore`
- [ ] `.env.example` files contain only placeholder values

### 3. Authentication and Authorization Review
Consult `.agents/rules/security.md`:
- [ ] Password hashing uses bcrypt with >=12 rounds
- [ ] Access tokens expire in <=30 minutes
- [ ] Refresh tokens are stored in the database and can be revoked
- [ ] Protected routes use `Depends(get_current_user)`
- [ ] Admin endpoints enforce role checks
- [ ] Auth endpoints are rate-limited
- [ ] Brute-force protection or lockout exists for repeated failures

### 4. Input Validation Review
- [ ] All API endpoints use Pydantic schemas for request validation
- [ ] Raw SQL string interpolation is absent
- [ ] File uploads validate type, size, and filenames
- [ ] `dangerouslySetInnerHTML` is absent or sanitized
- [ ] URL parameters are validated
- [ ] User input is not used directly in file paths

### 5. CORS and Headers Review
Check `backend/app/main.py`:
- [ ] `allow_origins` lists specific origins rather than `["*"]`
- [ ] `allow_credentials` is enabled only when needed
- [ ] `allow_methods` is restricted to required methods

Check security headers:
- [ ] `Strict-Transport-Security` => `max-age=31536000; includeSubDomains`
- [ ] `X-Content-Type-Options` => `nosniff`
- [ ] `X-Frame-Options` => `DENY`
- [ ] `Content-Security-Policy` => `default-src 'self'`
- [ ] `Referrer-Policy` => `strict-origin-when-cross-origin`

### 6. Database Security Review
- [ ] The application DB user has minimum required privileges
- [ ] Production DB connections use SSL
- [ ] Raw SQL string interpolation is absent
- [ ] Sensitive columns are identified and documented
- [ ] Soft delete or retention handling exists where required

### 7. Logging and Monitoring Review
- [ ] Auth events are logged
- [ ] 401 and 403 responses are logged
- [ ] Sensitive data is excluded from logs
- [ ] Logs include request IDs
- [ ] Alerting exists for error spikes and suspicious auth patterns

### 8. Document Findings
Create a security audit report:

```markdown
## Security Audit - <Date>

### Summary
- Dependencies: X critical, Y high, Z medium
- Secrets scan: clean/found issues
- Auth review: pass/found issues

### Findings
| # | Severity | Category | Finding | Remediation | Status |
|---|---|---|---|---|---|
| 1 | Critical | Dependencies | Vulnerable package version | Upgrade package | Open |
| 2 | High | Auth | Missing login rate limiting | Add rate limiter | Open |

### Action Items
- [ ] Fix all critical findings before release
- [ ] Create tickets for high and medium findings
- [ ] Schedule a re-audit after fixes ship
```

### 9. Incident Response Reminder
If a vulnerability is actively exploited:
1. Contain the issue by disabling the affected path or revoking credentials.
2. Notify the team lead and security owner.
3. Apply the hotfix.
4. Recover data or service integrity if needed.
5. Document the post-mortem and prevention steps.
