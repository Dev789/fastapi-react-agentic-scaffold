---
description: New team member onboarding guide from clone to first merged PR
---

# Onboard Developer Workflow

## Purpose
Get a new team member productive in **one day**. By the end of this workflow, they will have:
- A working local environment
- An understanding of the project architecture
- A merged PR (even a small one)

## Steps

### 1. Access Setup (Before Day 1)
The team lead should ensure:
- [ ] GitHub repo access granted
- [ ] CI/CD platform access (GitHub Actions — view permissions at minimum)
- [ ] Communication channels joined (Slack/Teams)
- [ ] Ticket board access (Jira/Linear/GitHub Issues)
- [ ] Cloud dashboard access (if applicable)
- [ ] `.env` values shared securely (password manager, not email/chat)

### 2. Local Environment Setup
Follow the `.agents/workflows/setup-local.md` workflow:
```bash
git clone <repository-url>
cd <project-name>
# Follow all steps in setup-local.md
```
**Checkpoint:** Backend at `localhost:8000/docs`, frontend at `localhost:5173`.

### 3. Understand the Architecture
Read these files in order:
1. **`README.md`** — Project overview, tech stack, folder structure.
2. **`agents.md`** — How AI personas work, code conventions, git workflow.
3. **`.agents/README.md`** — Index of all personas, prompts, and workflows.

Then explore the codebase:
- **Backend:** Start with `backend/app/main.py` → trace a single endpoint from router → CRUD → model → schema.
- **Frontend:** Start with `frontend/src/main.tsx` → `App.tsx` → trace a single page from route → component → API call → Redux slice.

### 4. Understand the Persona System
Read at least these two rule files:
- `.agents/rules/fastapi-rules.md` (if backend-focused)
- `.agents/rules/react-rules.md` (if frontend-focused)

Try asking your AI assistant: *"Explain the folder structure of this project based on agents.md"*. This validates the AI is reading the rules correctly.

### 5. Run the Test Suite
```bash
# Backend
cd backend
pytest tests/ -v

# Frontend
cd frontend
npm run test
```
**Checkpoint:** All tests pass. If they don't, fix the environment (not the tests).

### 6. Make a Practice PR
Pick a small, low-risk task:
- Fix a typo in documentation
- Add a missing test for an existing function
- Improve an error message
- Add a missing `alt` attribute to an image

Follow the `create-feature.md` workflow:
```bash
git checkout develop
git pull origin develop
git checkout -b feature/onboarding-<your-name>
# Make your change
git add -A
git commit -m "chore: <your small change>"
git push origin feature/onboarding-<your-name>
```
Create a PR and request review from your onboarding buddy.

### 7. Review Process Walkthrough
- Your buddy reviews using the `code-review.md` workflow.
- Address any feedback.
- Merge your first PR! 🎉

### 8. Deep Dives (Day 2+)
Based on your role, dive deeper:

#### Backend Developer
- Read `fastapi-rules.md` fully
- Read `dba.md` (database conventions)
- Try the `create-endpoint.md` prompt to generate a new resource
- Run a migration with `create-migration.md`

#### Frontend Developer
- Read `react-rules.md` fully
- Understand the Redux store structure in `src/app/`
- Try the `create-component.md` prompt to generate a component
- Run Lighthouse on the frontend

#### Full-Stack
- Read both rule files
- Trace a complete feature: API endpoint → database → frontend component → Redux → display

### 9. Onboarding Checklist
Share this with the new developer:
- [ ] Local environment running
- [ ] Can access: repo, CI/CD, ticket board, chat
- [ ] Read: README, agents.md, .agents/README.md
- [ ] Read: relevant persona rules (frontend/backend)
- [ ] All tests pass locally
- [ ] First PR created, reviewed, and merged
- [ ] Knows how to use AI prompts (`create-endpoint`, `create-component`)
- [ ] Knows the git workflow (feature branches, conventional commits)
- [ ] Assigned first real ticket
