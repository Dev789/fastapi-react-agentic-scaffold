# 🤖 Agentic Development Scaffold — FastAPI + React (Vite)

> **Build full-stack applications with AI as your pair programmer.** This repository is a pre-configured scaffold that teaches AI coding assistants *how* to write code for your project — enforcing your architecture, conventions, and quality standards from the very first prompt.

> **Note for AI agents:** This README is **illustrative only** — it is not normative. For authoritative rules, consult `agents.md` (see §0 Instruction Hierarchy). If any example here conflicts with `agents.md` or a persona rule, the higher-authority source wins.

---

## What Is This?

This is **not** a boilerplate with pre-built code. It's a **rule system** — a set of structured instructions that turn any AI coding assistant (GitHub Copilot, Cursor, Windsurf, Cline, Aider, ChatGPT, Claude, etc.) into a team of specialized engineers who already understand your stack, your conventions, and your quality bar.

Think of it as onboarding documentation — but for AI agents instead of human developers.

### The Problem It Solves

When you ask an AI to "create a user registration endpoint," it has no idea about your project's:
- Folder structure or naming conventions
- Auth pattern (JWT? Sessions? OAuth2?)
- Database conventions (audit columns? soft deletes? naming?)
- Testing strategy (what framework? what coverage?)
- Error handling patterns

So you get generic code that doesn't fit. You spend time correcting it. Every prompt, every time.

### The Solution

This scaffold provides **persona rules**, **skills**, and **workflows** that pre-load all of those decisions. When an AI assistant reads these files, it generates code that is *already consistent* with your architecture — no corrections needed.

---

## How It Works

```
┌──────────────────────────────────────────────────────────┐
│                   You (Developer)                        │
│                                                          │
│   "Create a products endpoint with CRUD operations"      │
└──────────────────────┬───────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────┐
│                 AI Coding Assistant                      │
│                                                          │
│   1. Reads agents.md → finds routing table               │
│   2. Routes to fastapi-rules.md (backend task)           │
│   3. Also consults dba.md (schema) + qa.md (tests)       │
│   4. Loads scaffold-backend-endpoint skill               │
│                                                          │
│   Output: Model → Schema → CRUD → Router → Migration     │
│           → Unit Tests → Integration Tests               │
│           All following YOUR project's conventions.      │
└──────────────────────────────────────────────────────────┘
```

---

## Quick Start — Your First Agentic Project

### Step 1: Clone This Scaffold

```bash
git clone https://github.com/<your-username>/python-react-agentic-scaffold.git my-project
cd my-project
rm -rf .git && git init   # Start fresh git history
```

### Step 2: Understand the Structure

```
my-project/
├── agents.md                  # 🧠 START HERE — Global AI directives & routing table
├── .agentignore               # Files AI assistants should skip when indexing
├── .agents/
│   ├── README.md              # Index of all personas, skills, and workflows
│   ├── rules/                 # 🎭 AI personas (one per SDLC role)
│   │   ├── react-rules.md     #    → Frontend: React + Vite + Tailwind + Redux
│   │   ├── fastapi-rules.md   #    → Backend: FastAPI + PostgreSQL + SQLAlchemy
│   │   ├── architect.md       #    → System design, ADRs, API versioning
│   │   ├── security.md        #    → Auth, OWASP, input validation, rate limiting
│   │   ├── devops.md          #    → Docker, CI/CD (GitHub Actions), IaC
│   │   ├── dba.md             #    → Migrations, naming, indexing, soft-delete
│   │   ├── qa.md              #    → Testing strategy, coverage, E2E
│   │   ├── ba.md              #    → User stories, acceptance criteria, NFRs
│   │   └── pm.md              #    → Sprint planning, tickets, releases
│   ├── skills/                # Reusable skill folders
│   │   ├── scaffold-backend-endpoint/
│   │   ├── scaffold-frontend-component/
│   │   ├── scaffold-db-migration/
│   │   ├── pr-reviewer/
│   │   ├── performance-profiler/
│   │   └── security-scanner/
│   └── workflows/             # 🔄 Step-by-step development workflows (10 total)
│       ├── setup-local.md     #    → Bootstrap local environment
│       ├── create-feature.md  #    → End-to-end feature development
│       ├── fix-bug.md         #    → Structured debugging + regression tests
│       ├── refactor.md        #    → Safe refactoring with test guards
│       ├── add-third-party-api.md  # → External API integration
│       ├── write-adr.md       #    → Architecture Decision Records
│       ├── deploy-production.md #  → Production deployment + rollback
│       ├── hotfix.md          #    → Emergency production fixes
│       └── onboard-developer.md #  → New team member onboarding
├── README.md                  # This file
└── .gitignore
```

### Step 3: Configure Your AI Assistant

Most AI coding assistants automatically read project-level instruction files. Here's how to set up the most common ones:

| Assistant | Setup |
|---|---|
| **Tools that read repo instructions** | Use `agents.md` as the canonical project instruction file. |
| **GitHub Copilot** | Keep `.github/copilot-instructions.md` mirrored from `agents.md` if you use Copilot-style tooling. |
| **Manual assistants** | Paste `agents.md`, then attach `.agents/README.md` or the relevant rule and skill files. |

> **Tip:** The `agents.md` file contains a routing table that tells the AI *which* persona file to read based on the task. The AI handles the rest.

### Step 4: Start Building

Open your AI assistant and try these example requests:

#### 🟢 Backend — Create an Endpoint
```
Use the scaffold-backend-endpoint skill to generate a full vertical slice 
for a "products" resource. Follow all rules in fastapi-rules.md and dba.md.
```
The AI will generate: model, schemas, CRUD, router, migration, and tests — all following your project conventions.

#### 🔵 Frontend — Create a Component
```
Use the scaffold-frontend-component skill to generate a ProductCard component 
that displays a product's name, price, and image. Follow react-rules.md.
```
The AI will generate: component file, custom hook (if needed), types, Redux slice, and test file.

#### 🟡 Database — Create a Migration
```
Use the scaffold-db-migration skill to add a "categories" table with 
name (unique), description, and a foreign key to products. Follow dba.md.
```
The AI will generate: the model, the Alembic migration, a review checklist, and a rollback test.

#### 🔴 Full Feature — End to End
```
Follow the create-feature workflow to build a complete "user registration" 
feature — backend endpoint, frontend form, and tests.
```
The AI will follow the 10-step workflow: branch → requirements → backend → frontend → lint → test → PR.

---

## Customizing for Your Project

This scaffold ships with **opinionated defaults**. You should customize them to match your team's preferences:

### Change the Tech Stack
| Decision | Where to Change | Default |
|---|---|---|
| CSS framework | `react-rules.md` §1 | Tailwind CSS |
| State management | `react-rules.md` §1 | Redux Toolkit |
| Auth strategy | `fastapi-rules.md` §7, `security.md` §2 | JWT + OAuth2 |
| CI/CD platform | `devops.md` §4 | GitHub Actions |
| E2E test tool | `qa.md` §6 | Playwright |
| Database | `fastapi-rules.md` §1 | PostgreSQL + asyncpg |

### Add Your Own Rules
Create new persona files in `.agents/rules/` for domain-specific guidance:
```markdown
# Data Science Rules
- **Notebooks:** Use only for exploration. Production code goes in `.py` files.
- **Models:** Version with MLflow. Never commit model binaries to git.
```

### Add Your Own Skills
Create new skill folders in `.agents/skills/` for repetitive tasks:
```markdown
# Task: Create a new background job
1. Create a Celery task in `backend/app/tasks/`.
2. Define the schema for the job payload.
3. Add a trigger endpoint in the API.
4. Write tests with mocked task execution.
```

### Add Your Own Workflows
Create new workflows in `.agents/workflows/` for team processes:
```markdown
---
description: How to perform a database rollback in production
---
# Production DB Rollback
1. Notify the team in #incidents channel.
2. Run `alembic downgrade -1` on the production instance.
...
```

---

## The Persona System Explained

The core idea is **role-based AI behavior**. Instead of one generic AI, you get specialized personas:

```
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    React Dev    │  │   FastAPI Dev   │  │    Architect    │
│                 │  │                 │  │                 │
│ Tailwind        │  │ Pydantic v2     │  │ ADRs            │
│ Redux           │  │ SQLAlchemy      │  │ C4 Diagrams     │
│ Vitest          │  │ Alembic         │  │ Caching         │
└─────────────────┘  └─────────────────┘  └─────────────────┘

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    Security     │  │     DevOps      │  │       DBA       │
│                 │  │                 │  │                 │
│ OWASP           │  │ Docker          │  │ Naming          │
│ JWT/OAuth2      │  │ GitHub CI       │  │ Audit Cols      │
│ Rate Limit      │  │ Terraform       │  │ Soft Delete     │
└─────────────────┘  └─────────────────┘  └─────────────────┘

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│       QA        │  │       BA        │  │       PM        │
│                 │  │                 │  │                 │
│ pytest          │  │ User Stories    │  │ Sprints         │
│ Playwright      │  │ BDD Criteria    │  │ DoD             │
│ Coverage        │  │ NFR Checklist   │  │ Releases        │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

The persona routing and co-consultation rules are defined authoritatively in `agents.md` (see the **Persona Routing** table and **Mandatory Co-Consultation Matrix**). For example, an authentication task would automatically pull in `security.md`, `qa.md`, and `architect.md` — not just `fastapi-rules.md`.

The result is code that **multiple specialized engineers would have reviewed** — generated in seconds.

---

## Recommended Development Workflow

```
1. 📋 Define requirements    → Use BA persona to write user stories
2. 📐 Plan the architecture  → Use Architect persona for system design
3. 🗄️ Design the schema     → Use DBA persona for table design + migration
4. ⚙️ Build the backend      → Use scaffold-backend-endpoint
5. 🎨 Build the frontend     → Use scaffold-frontend-component
6. 🔒 Security review        → Use Security persona to audit the code
7. 🧪 Write tests            → Use QA persona for test strategy
8. 🚀 Deploy                 → Use DevOps persona for CI/CD + Docker
9. 📊 Track progress         → Use PM persona for sprint management
```

> **Pro tip:** For a new feature, tell your AI assistant: *"Follow the create-feature workflow in `.agents/workflows/create-feature.md`"* — it will execute all 10 steps in sequence.

---

## Available Workflows (10)

All workflows live in `.agents/workflows/`. Tell your AI assistant to follow any of them by name.

### 🔨 Development
| Workflow | Command | What It Does |
|---|---|---|
| Setup Local | `Follow setup-local.md` | Clone → install deps → Docker → migrations → dev servers |
| Create Feature | `Follow create-feature.md` | Branch → build backend + frontend → lint → test → PR |
| Fix Bug | `Follow fix-bug.md` | Reproduce → write failing test → fix → verify regression |
| Refactor | `Follow refactor.md` | Ensure test coverage → small steps → verify zero behavior change |
| Add Third-Party API | `Follow add-third-party-api.md` | Service wrapper → retry logic → circuit breaker → mocks → tests |
| Write ADR | `Follow write-adr.md` | Document architecture decisions with options, trade-offs, consequences |
| Run Tests | `Follow run-tests.md` | Full test suite: unit → integration → E2E → coverage → lint |

### 🚀 Operations
| Workflow | Command | What It Does |
|---|---|---|
| Deploy Production | `Follow deploy-production.md` | Freeze → regression → staging → smoke test → deploy → monitor |
| Hotfix | `Follow hotfix.md` | Branch from `main` → minimal fix → fast-track review → backport → post-mortem |

### 🧑‍💻 Team
| Workflow | Command | What It Does |
|---|---|---|
| Onboard Developer | `Follow onboard-developer.md` | Day 1 guide: setup → read architecture → run tests → first merged PR |

---

## Tech Stack (Defaults)

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite + TypeScript |
| Styling | Tailwind CSS |
| State | Redux Toolkit |
| Routing | React Router v6 |
| Backend | FastAPI (see `agents.md` version matrix) |
| Database | PostgreSQL + SQLAlchemy 2.0 (Async) |
| Migrations | Alembic |
| Auth | OAuth2 + JWT |
| Testing | pytest, Vitest, Playwright |
| CI/CD | GitHub Actions |
| Containers | Docker + Docker Compose |
| IaC | Terraform |

---

## Environment Variables

### Backend (`backend/.env`)
| Variable | Description | Example |
|---|---|---|
| `DATABASE_URL` | PostgreSQL async connection string | `postgresql+asyncpg://user:pass@localhost:5432/dbname` |
| `SECRET_KEY` | JWT signing key | `your-secret-key-here` |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Access token TTL | `30` |
| `CORS_ORIGINS` | Allowed origins (comma-separated) | `http://localhost:5173` |

### Frontend (`frontend/.env`)
| Variable | Description | Example |
|---|---|---|
| `VITE_API_BASE_URL` | Backend API URL | `http://localhost:8000/api/v1` |

---

## Contributing

1. Read [`agents.md`](agents.md) for code standards.
2. Follow the [`create-feature`](.agents/workflows/create-feature.md) workflow.
3. Use [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, `test:`).
4. All PRs require at least one approval.

---

## License

*(Add your license here)*

---

<p align="center">
  <i>Built for developers who ship faster with AI — without sacrificing quality.</i>
</p>
