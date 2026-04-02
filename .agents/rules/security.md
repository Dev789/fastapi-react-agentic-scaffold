# Security Rules

## 1. OWASP Top 10 Awareness
- All code changes must be evaluated against the [OWASP Top 10](https://owasp.org/www-project-top-ten/):
  1. Broken Access Control
  2. Cryptographic Failures
  3. Injection
  4. Insecure Design
  5. Security Misconfiguration
  6. Vulnerable & Outdated Components
  7. Identification & Authentication Failures
  8. Software & Data Integrity Failures
  9. Security Logging & Monitoring Failures
  10. Server-Side Request Forgery (SSRF)

## 2. Authentication & Authorization
- **Pattern:** OAuth2 with JWT bearer tokens (short-lived access + long-lived refresh).
- Hash passwords with `bcrypt` (minimum 12 rounds) via `passlib`.
- Never store plaintext passwords, tokens, or API keys.
- Implement **role-based access control (RBAC)**. Define roles in the database, enforce via `Depends()` guards.
- Rate-limit authentication endpoints (login, register, password reset) to prevent brute force.

## 3. Input Validation
- **All** user input must be validated via Pydantic schemas on the backend. Never trust frontend validation alone.
- SQL injection: prevented by SQLAlchemy's parameterized queries. Never use raw SQL string interpolation.
- XSS: React's JSX auto-escapes by default. Never use `dangerouslySetInnerHTML` unless sanitized with DOMPurify.
- Path traversal: validate and sanitize file paths. Never construct file paths from user input directly.

## 4. CORS Policy
- Define explicit allowed origins in backend settings. Never use `allow_origins=["*"]` in production.
- Restrict allowed methods and headers to only what the frontend needs.
- Disable `allow_credentials` unless specifically needed for cookie-based auth.

## 5. CSRF Protection
- For cookie-based sessions: implement CSRF tokens (double-submit cookie pattern).
- For JWT bearer tokens in `Authorization` header: CSRF is mitigated by default (tokens are not auto-sent by browsers).

## 6. Rate Limiting
- Apply rate limits at the API gateway or middleware level.
- Suggested defaults:
  - General API: 100 requests/minute per user.
  - Auth endpoints: 10 requests/minute per IP.
  - File uploads: 5 requests/minute per user.
- Return `429 Too Many Requests` with a `Retry-After` header.

## 7. Dependency Scanning
- **Backend:** Run `pip-audit` in CI to detect known vulnerabilities in Python packages.
- **Frontend:** Run `npm audit` in CI. Fail on critical/high severity.
- Use Dependabot or Renovate for automated dependency update PRs.
- Review changelogs before merging dependency updates.

## 8. Secrets Management
- Never commit secrets to version control. Use `.env` files locally (listed in `.gitignore`).
- In CI: use GitHub Actions encrypted secrets.
- In production: use a secrets manager (AWS Secrets Manager, HashiCorp Vault, or similar).
- Rotate secrets on a defined schedule (minimum: every 90 days for API keys).

## 9. Security Headers
Ensure the following HTTP headers are set (via middleware or reverse proxy):
- `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Content-Security-Policy: default-src 'self'`
- `Referrer-Policy: strict-origin-when-cross-origin`

## 10. Logging & Incident Response
- Log all authentication events (login success/failure, token refresh, password reset).
- Log all authorization failures (403 responses).
- **Never log:** passwords, tokens, full credit card numbers, or PII in plaintext.
- Define an incident response runbook: detect → contain → eradicate → recover → post-mortem.
