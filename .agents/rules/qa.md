# Quality Assurance (QA) Rules

## 1. Backend Testing
- **Framework:** `pytest` + `pytest-asyncio`.
- **Mock external services.** Never make real HTTP calls or connect to production databases in tests.
- Use `httpx.AsyncClient` with FastAPI's `TestClient` for integration tests.
- Organize tests to mirror the source structure:
  ```
  tests/
  ├── conftest.py           # Shared fixtures (async client, test DB, factories)
  ├── unit/
  │   ├── test_user_crud.py
  │   └── test_auth_service.py
  └── integration/
      ├── test_user_endpoints.py
      └── test_auth_flow.py
  ```

## 2. Frontend Testing
- **Framework:** Vitest + React Testing Library.
- Test **behavior**, not implementation. Assert on what the user sees, not internal state.
- Place test files adjacent to the component: `UserCard.tsx` → `UserCard.test.tsx`.
- Mock API calls with `msw` (Mock Service Worker) for consistent, realistic mocking.

## 3. Test Database Isolation
- Use a **dedicated test database** (not the development database).
- Each test session: create tables → run tests → drop tables. Use fixtures for setup/teardown.
- Use **factory functions** (or `factory_boy`) to generate test data — never hardcode IDs or rely on insertion order.

## 4. Coverage Requirements
- **Minimum coverage:** 80% line coverage for backend, 70% for frontend.
- Cover edge cases and null inputs, not just the happy path.
- Critical paths (authentication, payment, data mutation) must have 100% branch coverage.
- Coverage reports must be generated in CI and fail the pipeline if below threshold.

## 5. Test Naming Convention
- Use descriptive names: `test_<function>_<scenario>_<expected_result>`.
- Examples:
  - `test_create_user_with_duplicate_email_returns_409`
  - `test_login_with_expired_token_returns_401`

## 6. E2E Testing
- **Tool:** Playwright for cross-browser end-to-end tests.
- Cover critical user flows: signup → login → core action → logout.
- Run E2E tests in CI against the `staging` environment before production deploy.
- E2E tests must not depend on specific test data — use setup/teardown fixtures.

## 7. CI Integration
- Tests run on **every pull request**. A failing test blocks merge.
- Test execution order: Unit → Integration → E2E.
- Use parallel test execution where possible to keep CI under 10 minutes.

## 8. Code Review Checklist (QA Perspective)
- [ ] Are there tests for the new/changed code?
- [ ] Do tests cover error cases and boundary conditions?
- [ ] Are mocks realistic and not overly permissive?
- [ ] Is test data created via factories (not hardcoded)?
- [ ] Do integration tests clean up after themselves?
