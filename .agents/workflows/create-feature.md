---
description: End-to-end workflow for developing a new feature from branch to PR
---

# Create Feature Workflow

## Prerequisites
- Local environment is running (see `setup-local.md` workflow)
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

### 3. Backend — Create the Endpoint
Use the prompt template: `.agents/prompts/create-endpoint.md`
- Model → Schema → CRUD → Router → Register → Migration → Tests

### 4. Frontend — Create the Component
Use the prompt template: `.agents/prompts/create-component.md`
- Component → Hook → Types → Redux Slice → Tests

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

### 7. Commit Changes
```bash
git add -A
git commit -m "feat(<scope>): <short description>

- Added <resource> model, schemas, CRUD, and router
- Added <ComponentName> component with Redux integration
- Added unit and integration tests

Closes #<ticket-id>"
```

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
