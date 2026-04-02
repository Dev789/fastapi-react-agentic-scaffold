---
description: AI-assisted code review checklist to run before approving any pull request
---

# Code Review Workflow

## When to Use
Run this checklist on every pull request before approving. The AI assistant can perform this review by reading the diff and checking each item.

## Steps

### 1. Understand the Change
- Read the PR title, description, and linked ticket.
- Understand **what** changed and **why** before looking at code.
- Ask: "Does this PR do one thing, or is it mixing concerns?"

### 2. Architecture & Design Review
Consult `.agents/rules/architect.md`:
- [ ] Does the change follow the existing project structure?
- [ ] Are new files in the correct directories per the canonical folder structure?
- [ ] Is business logic in `services/` (not in route handlers or components)?
- [ ] Are there any new dependencies? Are they justified?
- [ ] Does this introduce any tight coupling between layers?

### 3. Backend Review
Consult `.agents/rules/fastapi-rules.md`:
- [ ] Pydantic schemas separate from SQLAlchemy models?
- [ ] DB access goes through `crud/` or `services/`, not directly in endpoints?
- [ ] `Depends()` used for DB sessions and auth?
- [ ] Appropriate HTTP status codes (201 for create, 204 for delete)?
- [ ] Error handling: `HTTPException` with meaningful messages, no raw 500s?
- [ ] No N+1 queries (check eager loading)?
- [ ] Pagination implemented for list endpoints?

### 4. Frontend Review
Consult `.agents/rules/react-rules.md`:
- [ ] One component per file, named in PascalCase?
- [ ] No business logic in components (extracted to hooks or Redux)?
- [ ] Props defined with named interfaces?
- [ ] `useEffect` used only for actual side-effects?
- [ ] Error and loading states handled?
- [ ] Accessible: semantic HTML, keyboard navigable, `alt` on images?
- [ ] No inline styles — Tailwind classes used?

### 5. Security Review
Consult `.agents/rules/security.md`:
- [ ] No hardcoded secrets, tokens, or API keys?
- [ ] User input validated on the backend (Pydantic schemas)?
- [ ] No `dangerouslySetInnerHTML` without DOMPurify?
- [ ] Auth-protected routes use `Depends(get_current_user)`?
- [ ] No sensitive data logged (passwords, tokens, PII)?
- [ ] CORS configured with explicit origins (not `*`)?

### 6. Database Review
Consult `.agents/rules/dba.md`:
- [ ] Schema changes accompanied by Alembic migration?
- [ ] Migration has a working `downgrade()` function?
- [ ] New tables include audit columns (`id`, `created_at`, `updated_at`)?
- [ ] Naming follows conventions (snake_case, plural tables)?
- [ ] Indices added for frequently queried columns?

### 7. Testing Review
Consult `.agents/rules/qa.md`:
- [ ] New code has corresponding tests?
- [ ] Tests cover error cases and edge conditions, not just happy path?
- [ ] Test data created via factories (not hardcoded)?
- [ ] Mocks are realistic (not `Mock(return_value=True)`)?
- [ ] No tests that depend on execution order?

### 8. Code Quality
- [ ] No dead code (commented-out blocks, unused imports)?
- [ ] No `console.log()` or `print()` statements?
- [ ] No TODO/FIXME without a linked ticket?
- [ ] Variable and function names are descriptive?
- [ ] Complex logic has comments explaining **why**, not **what**?

### 9. Provide Feedback
Categorize your comments:
- **🔴 Blocker:** Must fix before merge (security issue, bug, broken test).
- **🟡 Suggestion:** Should fix, but not a merge blocker (naming, minor refactor).
- **🟢 Nit:** Optional stylistic preference. Prefix with "nit:".
- **💡 Question:** Seeking clarification, not requesting a change.

### 10. Final Decision
- **Approve:** All blockers resolved, tests pass, code meets standards.
- **Request Changes:** Blockers exist. List them clearly.
- **Comment:** Need more context before deciding.
