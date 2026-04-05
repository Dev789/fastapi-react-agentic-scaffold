# Global AI Agent Directives

## 0. Instruction Hierarchy

When instruction sources conflict, the following precedence order applies (highest -> lowest):

1. **`agents.md`** - Global directives. Always wins.
2. **Persona rules** (`.agents/rules/*`) - Domain-specific constraints.
3. **Skills & workflows** (`.agents/skills/*/SKILL.md`, `.agents/workflows/*`) - Reusable execution guides.
4. **README examples** (`README.md`, `.agents/README.md`) - Illustrative only; never normative.

If a workflow or skill contradicts `agents.md` or a persona rule, follow the higher-authority source and flag the inconsistency to the developer.

## 1. Core Operating Procedure
- This repository utilizes a **Multi-Agent Persona Architecture**.
- Depending on the task requested, you **must** adopt the appropriate persona found in the `.agents/rules/` directory before generating a response or writing code.
- Consult `.agents/README.md` for a full index of available personas, skills, and workflows.

### Persona Routing
| Task Domain | Persona File |
|---|---|
| React / Frontend code | `.agents/rules/react-rules.md` |
| FastAPI / Backend code | `.agents/rules/fastapi-rules.md` |
| System design / Architecture decisions | `.agents/rules/architect.md` |
| Security review / Auth / Vulnerability fixes | `.agents/rules/security.md` |
| Docker / CI-CD / Infrastructure | `.agents/rules/devops.md` |
| Database schema / Queries / Migrations | `.agents/rules/dba.md` |
| Writing or reviewing tests | `.agents/rules/qa.md` |
| Requirements / User stories | `.agents/rules/ba.md` |
| Task breakdown / Sprint planning | `.agents/rules/pm.md` |

> When a task spans multiple domains (e.g., "add a new feature end-to-end"), consult **all** relevant personas and reconcile their guidance.

### Mandatory Co-Consultation Matrix

The routing table above selects the **primary** persona. The matrix below lists personas that **must also** be consulted for specific change types, regardless of how the task is phrased:

| Change Type | Always Co-Consult |
|---|---|
| Auth / permissions / login | `security.md` + `qa.md` + `architect.md` |
| Schema / migrations / DB changes | `dba.md` + `fastapi-rules.md` + `qa.md` |
| External / third-party API integration | `security.md` + `devops.md` + `architect.md` |
| New API endpoint | `fastapi-rules.md` + `qa.md` + `security.md` |
| New UI page or route | `react-rules.md` + `qa.md` |
| Infrastructure / CI-CD / Docker | `devops.md` + `security.md` + `architect.md` |
| Dependency add / upgrade | `security.md` + `devops.md` |

### Supported Version Matrix

Do **not** use phrases like "latest stable" or "v6+" when referring to dependencies. Use the pinned versions below. Upgrades require an ADR (see `architect.md` §3).

| Dependency | Supported Version | Upgrade Policy |
|---|---|---|
| Python | 3.11.x, 3.12.x | Adopt new minor within 90 days of release |
| FastAPI | 0.115.x | Pin minor; test before bump |
| Pydantic | 2.x | Pin major |
| SQLAlchemy | 2.0.x | Pin major |
| Alembic | 1.13.x | Pin minor |
| React | 18.x | Pin major |
| React Router | 6.x | Pin major; v7 requires ADR |
| Redux Toolkit | 2.x | Pin major |
| Vite | 5.x | Pin major |
| TypeScript | 5.x | Pin major |
| Tailwind CSS | 3.x | Pin major |
| Node.js | 20.x LTS | Use active LTS only |

> Update this matrix in `agents.md` whenever a version is upgraded. The matrix is the single source of truth for supported versions.

## 2. General Code Guidelines
- **No Dead Code:** Never leave commented-out code blocks or `print()`/`console.log()` statements in committed code.
- **Environment Variables:** Never hardcode secrets, API keys, or connection strings. Use `.env` files locally and a secrets manager in production.
- **Root Cleanliness:** Do not generate scripts or configs in the root directory unless explicitly instructed. Keep frontend logic in `/frontend` and backend logic in `/backend`.
- **Formatting:** All Python code must pass `ruff` linting. All TypeScript/JSX code must pass `eslint` + `prettier`.
- **Naming Conventions:**
  - Python: `snake_case` for variables, functions, modules. `PascalCase` for classes.
  - TypeScript/React: `camelCase` for variables/functions. `PascalCase` for components and types.
  - Database: `snake_case` for tables and columns. Plural table names (e.g., `users`, `orders`).

### 2.1 Documentation Standards
- **Python:** Every public function, class, and module must have a docstring (Google style).
  ```python
  async def create_user(db: AsyncSession, user_in: UserCreate) -> User:
      """Create a new user in the database.

      Args:
          db: Async database session.
          user_in: Validated user creation data.

      Returns:
          The newly created User ORM instance.

      Raises:
          DuplicateResourceError: If email already exists.
      """
  ```
- **TypeScript:** Use JSDoc for exported functions and complex logic.
  ```typescript
  /**
   * Fetches a paginated list of products from the API.
   * @param skip - Number of items to skip (default: 0)
   * @param limit - Max items to return (default: 20)
   * @returns Promise resolving to a ProductList response
   */
  ```
- **Comments:** Explain **why**, never **what**. The code shows what it does — comments explain non-obvious reasoning.
- **README updates:** When adding a new feature that changes setup, configuration, or environment variables, update the relevant section in `README.md` in the same PR.

## 3. Canonical Folder Structure

```
/
├── agents.md                  # This file — global AI directives
├── README.md                  # Project documentation
├── .gitignore
├── .agents/
│   ├── README.md              # Index of personas, skills, workflows
│   ├── rules/                 # AI persona rule files
│   ├── skills/                # Specialized agent capabilities
│   └── workflows/             # Step-by-step procedures
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── api/               # API client layer (axios instances, endpoints)
│   │   ├── app/               # Redux store configuration
│   │   ├── assets/            # Static assets (images, fonts)
│   │   ├── components/        # Reusable UI components
│   │   │   └── ui/            # Primitive/shared UI components
│   │   ├── features/          # Feature-based slices (Redux + components)
│   │   ├── hooks/             # Custom React hooks
│   │   ├── layouts/           # Page layout wrappers
│   │   ├── pages/             # Route-level page components
│   │   ├── types/             # Shared TypeScript types/interfaces
│   │   ├── utils/             # Pure utility functions
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── tailwind.config.js
│   ├── vite.config.ts
│   └── package.json
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   └── v1/            # Versioned API routers
│   │   │       ├── endpoints/ # Route handlers per resource
│   │   │       └── router.py  # Aggregated v1 router
│   │   ├── core/              # Config, security, dependencies
│   │   ├── crud/              # Database CRUD operations
│   │   ├── db/                # Engine, session, base model
│   │   ├── models/            # SQLAlchemy ORM models
│   │   ├── schemas/           # Pydantic request/response schemas
│   │   ├── services/          # Business logic layer
│   │   └── main.py            # FastAPI app factory
│   ├── alembic/               # Database migrations
│   ├── tests/
│   │   ├── conftest.py
│   │   ├── unit/
│   │   └── integration/
│   ├── alembic.ini
│   ├── pyproject.toml
│   └── requirements.txt
└── docker-compose.yml
```

## 4. Git Conventions
- **Branch naming:** `feature/<ticket-id>-short-description`, `fix/<ticket-id>-short-description`, `chore/<description>`.
- **Commit messages:** Use Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`, `test:`, `refactor:`).
- **Pull requests:** Must reference the originating ticket. Require at least one approval before merge.

### 4.1 Granular Atomic Commits
- Each commit must represent **one logical change**. Never bundle unrelated changes.
- Preferred commit sequence for a new feature:
  ```
  feat(models): add Product model with audit columns
  feat(schemas): add Product create/update/out schemas
  feat(crud): add Product CRUD operations
  feat(api): add Product endpoints with pagination
  feat(migration): add products table migration
  test: add Product endpoint unit and integration tests
  docs: update CHANGELOG for Product feature
  ```
- If a commit cannot be described without using the word "and", it should be split.
- Tests and code must be in the same PR but may be in separate commits.

### 4.2 Changelog
- Maintain a `CHANGELOG.md` in the project root following [Keep a Changelog](https://keepachangelog.com/) format.
- Every user-facing change **must** have a changelog entry in the same PR that introduces it.
- Group entries under: `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`.
- Format:
  ```markdown
  ## [Unreleased]
  ### Added
  - Product CRUD endpoints with pagination (#42)
  - Product search by category filter (#43)

  ### Fixed
  - Order total calculation with zero quantity (#38)
  ```
- On release, move `[Unreleased]` entries under the version heading with the release date.

### 4.3 Pull Request Description
- Every PR must include:
  ```markdown
  ## Summary
  Brief description of what this PR does and why.

  ## Changes
  - Bullet list of specific changes made.

  ## Related Ticket
  Closes #<ticket-id>

  ## Testing
  Describe how this was tested (automated + manual).

  ## Screenshots (if UI change)
  Before / After screenshots or recordings.

  ## Checklist
  - [ ] Tests pass locally
  - [ ] Linting passes
  - [ ] CHANGELOG updated
  - [ ] Documentation updated (if applicable)
  ```

