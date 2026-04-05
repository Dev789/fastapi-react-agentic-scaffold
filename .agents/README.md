# .agents - AI Agent Suite

This directory contains the AI persona rules, specialized skills, and workflows that govern how AI assistants interact with this codebase.

> **Start here:** Read `../agents.md` for the global agent routing definitions that determine which Antigravity handle and rule file to use for each task.

## Personas (`rules/`)

| File | Role | When to Use |
|---|---|---|
| `orchestrator.md` | Lead Orchestrator | Triaging incoming work, selecting specialist agents, reconciling cross-domain guidance |
| `react-rules.md` | Frontend Developer | Writing React components, hooks, pages, styling, or frontend tests |
| `fastapi-rules.md` | Backend Developer | Writing FastAPI endpoints, models, schemas, CRUD, or backend tests |
| `architect.md` | Systems Architect | Making architectural decisions, designing system boundaries, ADRs |
| `security.md` | Security Engineer | Auth implementation, vulnerability fixes, security reviews, CORS/CSRF |
| `devops.md` | DevOps / SRE | Docker, CI/CD pipelines, infrastructure, monitoring, deployments |
| `dba.md` | Database Administrator | Schema design, migrations, query optimization, indexing |
| `qa.md` | QA Engineer | Writing tests, defining coverage strategy, E2E test planning |
| `ba.md` | Business Analyst | Writing user stories, acceptance criteria, requirements documents |
| `pm.md` | Project Manager | Task breakdown, sprint planning, ticket management, releases |

## Skills (`skills/`)

| Capability | Purpose |
|---|---|
| `security-scanner` | Comprehensive security audit across dependencies, auth, CORS, and logging |
| `performance-profiler` | Profile backend and frontend performance with actionable findings |
| `pr-reviewer` | AI-assisted code review checklist for pull requests |
| `scaffold-backend-endpoint` | Generate a full backend vertical slice (model → schema → CRUD → router → migration → tests) |
| `scaffold-frontend-component`| Generate a React component with hook, types, Redux integration, and tests |
| `scaffold-db-migration` | Generate and validate an Alembic database migration |
| `push-code-repo` | Push committed code to the correct remote branch with git safety checks |

Agents that do not auto-discover skills should read `.agents/skills/registry.yaml` to map tasks to the correct skill file.

## Workflows (`workflows/`)

### Development
| File | Purpose |
|---|---|
| `setup-local.md` | Bootstrap the local dev environment from a fresh clone |
| `create-feature.md` | End-to-end feature development from branch creation to PR merge |
| `fix-bug.md` | Structured debugging with reproduce-first approach and regression tests |
| `refactor.md` | Safe refactoring with test-guarded verification |
| `add-third-party-api.md` | Integrate external APIs with retry logic, circuit breakers, and mocks |
| `write-adr.md` | Document Architecture Decision Records for significant technical choices |
| `run-tests.md` | Run the full test suite (unit, integration, E2E) with coverage reporting |

### Operations
| File | Purpose |
|---|---|
| `deploy-production.md` | Step-by-step production deployment with staging validation and rollback |
| `hotfix.md` | Emergency production hotfix with fast-track review process |

### Team
| File | Purpose |
|---|---|
| `onboard-developer.md` | New team member onboarding from clone to first merged PR |

## Compatibility Shims (`prompts/`)

This folder contains legacy prompt templates retained for backward compatibility with older AI coding assistants. 

| File | Redirects To |
|---|---|
| `create-component.md` | `skills/scaffold-frontend-component/SKILL.md` |
| `create-endpoint.md` | `skills/scaffold-backend-endpoint/SKILL.md` |
| `create-migration.md` | `skills/scaffold-db-migration/SKILL.md` |

> [!NOTE]
> Modern agents should ignore this folder and use the canonical `skills/` directly.

## How to Use

1. **AI assistants** automatically consult the agent routing definitions in `agents.md` to select the right Antigravity handle and governing rule file.
2. **Skills** define self-contained capabilities for the AI (e.g., "Run the `security-scanner` skill" or "Use `scaffold-backend-endpoint`").
3. **Workflows** provide step-by-step guides for common processes - useful for onboarding and consistency.
