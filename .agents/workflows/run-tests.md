---
description: Run the full test suite across backend, frontend, and E2E with coverage reporting
---

# Run Tests Workflow

## When to Use
- Before creating a pull request
- Before merging to `develop` or `main`
- After a refactor to verify zero behavior change
- As part of CI pipeline validation

## Steps

### 1. Backend Unit Tests
```bash
cd backend
pytest tests/unit/ -v --tb=short
```
- These test individual functions (CRUD operations, services, utilities) in isolation.
- They should run fast (< 30 seconds) and not require a database connection.
- Failures here mean business logic is broken.

### 2. Backend Integration Tests
```bash
cd backend
pytest tests/integration/ -v --tb=short
```
- These test API endpoints end-to-end using `httpx.AsyncClient` against a test database.
- They verify request/response contracts, auth guards, and database interactions.
- Failures here mean the API contract is broken.

### 3. Backend Coverage Report
```bash
cd backend
pytest tests/ -v --cov=app --cov-report=term-missing --cov-fail-under=80
```
- **Minimum threshold:** 80% line coverage.
- `--cov-report=term-missing` shows which lines are not covered.
- `--cov-fail-under=80` fails the command if coverage drops below 80%.
- Review uncovered lines — prioritize covering:
  - Error handling branches
  - Auth guard edge cases
  - Boundary conditions (empty lists, null values, max limits)

### 4. Frontend Unit / Component Tests
```bash
cd frontend
npm run test
```
- Uses Vitest + React Testing Library.
- Tests component rendering, user interactions, and Redux state changes.
- Failures here mean UI behavior is broken.

### 5. Frontend Coverage Report
```bash
cd frontend
npm run test -- --coverage
```
- **Minimum threshold:** 70% line coverage.
- Review uncovered components — prioritize covering:
  - Form validation and submission
  - Error state rendering
  - Loading state rendering
  - Conditional UI logic

### 6. E2E Tests (When Configured)
```bash
cd frontend
npx playwright test
```
- Runs cross-browser end-to-end tests against a running application.
- Prerequisites: backend and frontend dev servers must be running.
- Tests critical user flows: signup → login → core action → logout.
- Run with UI for debugging: `npx playwright test --ui`

### 7. Lint Check
```bash
# Backend
cd backend
ruff check .
ruff format --check .

# Frontend
cd frontend
npm run lint
npx prettier --check src/
```
- Linting is part of the test workflow — code that doesn't pass lint is not shippable.

### 8. Full Suite (All-in-One)
Run everything in sequence to validate before a PR:
```bash
# Backend
cd backend
ruff check .
ruff format --check .
pytest tests/ -v --cov=app --cov-report=term-missing --cov-fail-under=80

# Frontend
cd frontend
npm run lint
npm run test -- --coverage
```

---

## Interpreting Results

### Test Failed
| Symptom | Likely Cause | Action |
|---|---|---|
| Unit test fails | Business logic bug | Fix the code, not the test (unless test is wrong) |
| Integration test fails | API contract changed | Update schema/endpoint or update test to match new contract |
| Coverage below threshold | New code lacks tests | Write tests for uncovered lines before merging |
| Lint fails | Code style violation | Run `ruff check . --fix` / `npm run lint -- --fix` |
| E2E test fails | UI flow broken | Check if backend is running, then debug with `--ui` flag |

### Flaky Tests
If a test passes sometimes and fails other times:
- Check for: test ordering dependencies, shared mutable state, time-sensitive assertions, network calls.
- Fix: use factories for test data, isolate DB state per test, mock external calls.
- Never skip or ignore a flaky test — fix it or delete it.

---

## CI Integration
This workflow maps to the GitHub Actions pipeline:
```yaml
# Simplified CI flow
jobs:
  backend:
    steps:
      - ruff check .
      - pytest tests/ --cov=app --cov-fail-under=80
  frontend:
    steps:
      - npm run lint
      - npm run test -- --coverage
  e2e:
    needs: [backend, frontend]
    steps:
      - npx playwright test
```

## Checklist
- [ ] All backend unit tests pass
- [ ] All backend integration tests pass
- [ ] Backend coverage ≥ 80%
- [ ] All frontend tests pass
- [ ] Frontend coverage ≥ 70%
- [ ] Lint passes (backend + frontend)
- [ ] E2E tests pass (if configured)
- [ ] No flaky tests introduced
