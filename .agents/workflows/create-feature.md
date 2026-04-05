---
description: End-to-end workflow for developing a new feature from branch to PR
---

# Create Feature Workflow

## Prerequisites
- Local environment is running (see `setup-local.md`)
- You have a ticket/issue number for the feature

## Steps

### 1. Create a Feature Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/<ticket-id>-<short-description>
```

### 2. Define Requirements
- Review the ticket's user story and acceptance criteria.
- Consult `.agents/rules/ba.md` for requirements standards.
- Clarify any ambiguities **before** writing code.

### 3. Backend - Create the Endpoint
Use the skill: `.agents/skills/scaffold-backend-endpoint/SKILL.md`
- Model -> Schema -> CRUD -> Router -> Register -> Migration -> Tests

### 4. Frontend - Create the Component
Use the skill: `.agents/skills/scaffold-frontend-component/SKILL.md`
- Component -> Hook -> Types -> Redux slice -> Tests

### 5. Run Linting
```bash
# Backend
cd backend
ruff check . --fix
ruff format .

# Frontend
cd frontend
npm run lint -- --fix
npx prettier --write src/
```

### 6. Run Tests
```bash
# Backend
cd backend
pytest tests/ -v --cov=app --cov-report=term-missing

# Frontend
cd frontend
npm run test
```

### 7. Commit Changes (Atomic Commits)
Per `agents.md` Section 4.1, each commit must represent **one logical change**. Never bundle unrelated layers into a single commit. Use the following sequence:

```bash
# Backend - one commit per layer
git add backend/app/models/
git commit -m "feat(models): add <Resource> model with audit columns"

git add backend/app/schemas/
git commit -m "feat(schemas): add <Resource> create/update/out schemas"

git add backend/app/crud/
git commit -m "feat(crud): add <Resource> CRUD operations"

git add backend/app/api/
git commit -m "feat(api): add <Resource> endpoints with pagination"

git add backend/alembic/
git commit -m "feat(migration): add <resource>s table migration"

# Frontend - one commit per layer
git add frontend/src/types/ frontend/src/api/
git commit -m "feat(frontend): add <Resource> types and API client"

git add frontend/src/features/ frontend/src/components/ frontend/src/pages/
git commit -m "feat(frontend): add <ComponentName> component with Redux integration"

# Tests — can be one commit if all tests are for the same feature
git add backend/tests/ frontend/src/**/*.test.*
git commit -m "test: add <Resource> unit and integration tests"

# Docs
git add CHANGELOG.md
git commit -m "docs: update CHANGELOG for <Resource> feature (Closes #<ticket-id>)"
```

> **Rule of thumb:** If a commit message needs the word "and" to describe unrelated layers, split it.


### 8. Push and Create PR
```bash
git push origin feature/<ticket-id>-<short-description>
```
Create a Pull Request:
- **Title:** `feat(<scope>): <short description>`
- **Description:** Link to ticket, summary of changes, screenshots (if UI)
- **Reviewers:** Assign at least one peer reviewer
- **Labels:** `frontend`, `backend`, or both

### 9. Address Review Feedback
- Respond to all comments.
- Push fixes as additional commits (do not force-push during review).
- Re-request review once all feedback is addressed.

### 10. Merge
- Squash merge into `develop` after approval.
- Delete the feature branch after merge.
- Verify the `dev` environment deployment succeeds.
