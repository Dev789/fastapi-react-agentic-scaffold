# .agents — AI Agent Suite

This directory contains the AI persona rules, reusable prompts, and workflows that govern how AI assistants interact with this codebase.

> **Start here:** Read `../agents.md` for the global routing table that determines which persona to use for each task.

## Personas (`rules/`)

| File | Role | When to Use |
|---|---|---|
| `react-rules.md` | Frontend Developer | Writing React components, hooks, pages, styling, or frontend tests |
| `fastapi-rules.md` | Backend Developer | Writing FastAPI endpoints, models, schemas, CRUD, or backend tests |
| `architect.md` | Systems Architect | Making architectural decisions, designing system boundaries, ADRs |
| `security.md` | Security Engineer | Auth implementation, vulnerability fixes, security reviews, CORS/CSRF |
| `devops.md` | DevOps / SRE | Docker, CI/CD pipelines, infrastructure, monitoring, deployments |
| `dba.md` | Database Administrator | Schema design, migrations, query optimization, indexing |
| `qa.md` | QA Engineer | Writing tests, defining coverage strategy, E2E test planning |
| `ba.md` | Business Analyst | Writing user stories, acceptance criteria, requirements documents |
| `pm.md` | Project Manager | Task breakdown, sprint planning, ticket management, releases |

## Prompts (`prompts/`)

| File | Purpose |
|---|---|
| `create-endpoint.md` | Generate a full backend vertical slice (model → schema → CRUD → router → migration → tests) |
| `create-component.md` | Generate a React component with hook, types, Redux integration, and tests |
| `create-migration.md` | Generate and validate an Alembic database migration |

## Workflows (`workflows/`)

### Development
| File | Purpose |
|---|---|
| `setup-local.md` | Bootstrap the local dev environment from a fresh clone |
| `create-feature.md` | End-to-end feature development from branch creation to PR merge |
| `fix-bug.md` | Structured debugging with reproduce-first approach and regression tests |
| `refactor.md` | Safe refactoring with test-guarded verification |
| `code-review.md` | AI-assisted code review checklist for pull requests |
| `add-third-party-api.md` | Integrate external APIs with retry logic, circuit breakers, and mocks |
| `write-adr.md` | Document Architecture Decision Records for significant technical choices |
| `run-tests.md` | Run the full test suite (unit, integration, E2E) with coverage reporting |

### Operations
| File | Purpose |
|---|---|
| `deploy-production.md` | Step-by-step production deployment with staging validation and rollback |
| `hotfix.md` | Emergency production hotfix with fast-track review process |
| `performance-audit.md` | Profile backend and frontend performance with actionable findings |
| `security-audit.md` | Comprehensive security audit across dependencies, auth, CORS, and logging |

### Team
| File | Purpose |
|---|---|
| `onboard-developer.md` | New team member onboarding from clone to first merged PR |

## How to Use

1. **AI assistants** automatically consult the routing table in `agents.md` to select the right persona.
2. **Developers** can reference prompt templates when asking an AI to generate code (e.g., "Use the `create-endpoint` prompt for a `products` resource").
3. **Workflows** provide step-by-step guides for common processes — useful for onboarding and consistency.
