---
description: Step-by-step production deployment with staging validation and rollback plan
---

# Deploy to Production Workflow

## Prerequisites
- All tickets in the release are in "Done" status.
- `develop` branch is stable and all CI checks pass.
- You have production deployment access.

## Steps

### 1. Freeze Scope
- Announce code freeze in the team channel.
- No new feature merges to `develop` after this point.
- Only critical bug fixes are allowed during the release window.

### 2. Create Release Branch
```bash
git checkout develop
git pull origin develop
git checkout -b release/<version>
```
Version format: `v<major>.<minor>.<patch>` (e.g., `v1.2.0`).

### 3. Update Version & Changelog
- Bump version in `backend/pyproject.toml` and `frontend/package.json`.
- Update `CHANGELOG.md` with user-facing changes:
  ```markdown
  ## [1.2.0] - 2026-04-02
  ### Added
  - User registration endpoint
  - Product search with filters
  ### Fixed
  - Order total calculation with zero quantity (BUG-123)
  ```
- Commit: `chore: bump version to v1.2.0`

### 4. Run Full Regression
```bash
# Backend
cd backend
pytest tests/ -v --cov=app --cov-report=term-missing

# Frontend
cd frontend
npm run test -- --coverage

# E2E (if configured)
npx playwright test
```
**All tests must pass. No exceptions.**

### 5. Deploy to Staging
```bash
git push origin release/<version>
```
- CI/CD pipeline deploys to staging automatically (or trigger manually).
- Wait for deployment to complete.

### 6. Staging Smoke Tests
Manually verify on the staging environment:
- [ ] App loads without errors
- [ ] Login/logout works
- [ ] Core user flows work (create, read, update, delete)
- [ ] New features from this release work as expected
- [ ] API docs load at `/docs`
- [ ] Health check returns 200 at `/health`
- [ ] No errors in application logs

### 7. Merge to Main
```bash
git checkout main
git pull origin main
git merge release/<version> --no-ff
git tag -a v<version> -m "Release v<version>"
git push origin main --tags
```

### 8. Deploy to Production
- CI/CD pipeline deploys to production on merge to `main` (or trigger manually).
- Monitor deployment progress.

### 9. Production Verification (First 15 Minutes)
- [ ] Health check returns 200
- [ ] Application loads without errors
- [ ] No spike in error rates (check monitoring dashboard)
- [ ] No spike in response latency
- [ ] Database migrations applied successfully
- [ ] Test one critical user flow manually

### 10. Backport to Develop
```bash
git checkout develop
git merge main --no-ff
git push origin develop
```
Delete the release branch:
```bash
git branch -d release/<version>
git push origin --delete release/<version>
```

### 11. Announce
- Post release notes in the team channel.
- Notify stakeholders.
- Close the release ticket.

---

## Rollback Plan
If something goes wrong in production:

### Immediate Rollback (< 5 min)
```bash
# Deploy the previous Docker image (tagged with previous commit SHA)
docker pull <registry>/<image>:<previous-sha>
docker-compose up -d backend
```

### Database Rollback (if migration caused issues)
```bash
cd backend
alembic downgrade -1
```

### Post-Rollback
- [ ] Announce rollback in team channel
- [ ] Create incident ticket
- [ ] Investigate root cause
- [ ] Write post-mortem (see `security-audit.md` incident process)
