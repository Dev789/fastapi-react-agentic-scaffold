---
name: pr-reviewer
description: Review pull requests and local code changes against the repository's architecture, security, database, testing, and quality rules. Use before approving a PR, when auditing a diff, or when preparing structured review feedback for changed files.
---

# PR Reviewer

## Steps

### 1. Understand the Change
- Read the PR title, description, and linked ticket.
- Understand what changed and why before looking at code.
- Ask whether the PR does one thing or mixes concerns.

### 2. Architecture and Design Review
Consult `.agents/rules/architect.md`:
- [ ] Does the change follow the existing project structure?
- [ ] Are new files in the correct directories per the canonical folder structure?
- [ ] Is business logic in `services/` and not in route handlers or components?
- [ ] Are new dependencies justified?
- [ ] Does the change introduce tight coupling between layers?

### 3. Backend Review
Consult `.agents/rules/fastapi-rules.md`:
- [ ] Are Pydantic schemas separate from SQLAlchemy models?
- [ ] Does DB access go through `crud/` or `services/` rather than endpoints?
- [ ] Are `Depends()` injections used for DB sessions and auth?
- [ ] Are HTTP status codes appropriate, including `201` for create and `204` for delete?
- [ ] Is error handling clear, with meaningful `HTTPException` responses?
- [ ] Are N+1 query risks addressed?
- [ ] Is pagination implemented for list endpoints?

### 4. Frontend Review
Consult `.agents/rules/react-rules.md`:
- [ ] Is each component in its own file with a PascalCase name?
- [ ] Is business logic extracted into hooks or Redux instead of components?
- [ ] Are props defined with named interfaces?
- [ ] Is `useEffect` reserved for real side effects?
- [ ] Are loading and error states handled?
- [ ] Is the UI accessible, including semantic HTML, keyboard support, and `alt` text?
- [ ] Are Tailwind classes used instead of inline styles?

### 5. Security Review
Consult `.agents/rules/security.md`:
- [ ] Are there no hardcoded secrets, tokens, or API keys?
- [ ] Is user input validated on the backend with Pydantic schemas?
- [ ] Is `dangerouslySetInnerHTML` avoided or sanitized with DOMPurify?
- [ ] Do protected routes use `Depends(get_current_user)`?
- [ ] Is sensitive data excluded from logs?
- [ ] Is CORS configured with explicit origins rather than `*`?

### 6. Database Review
Consult `.agents/rules/dba.md`:
- [ ] Are schema changes accompanied by an Alembic migration?
- [ ] Does the migration have a working `downgrade()` function?
- [ ] Do new tables include audit columns (`id`, `created_at`, `updated_at`)?
- [ ] Does naming follow conventions such as snake_case and plural tables?
- [ ] Are indexes added for frequently queried columns?

### 7. Testing Review
Consult `.agents/rules/qa.md`:
- [ ] Does new code have corresponding tests?
- [ ] Do tests cover error cases and edge conditions, not just happy paths?
- [ ] Is test data created via factories rather than hardcoded values?
- [ ] Are mocks realistic?
- [ ] Are tests independent of execution order?

### 8. Code Quality
- [ ] Is dead code removed, including commented-out blocks and unused imports?
- [ ] Are `console.log()` and `print()` statements absent?
- [ ] Are TODO or FIXME notes linked to a ticket?
- [ ] Are names descriptive?
- [ ] Do comments explain why rather than what?

### 9. Provide Feedback
Categorize comments as:
- `Blocker`: Must fix before merge.
- `Suggestion`: Should fix, but not a merge blocker.
- `Nit`: Optional stylistic preference. Prefix with `nit:`.
- `Question`: Clarification request rather than change request.

### 10. Final Decision
- Approve when blockers are resolved and the change meets the repo standards.
- Request changes when blockers remain.
- Comment when more context is needed before deciding.
