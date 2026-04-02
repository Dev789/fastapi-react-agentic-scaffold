# Global AI Agent Directives

## 1. Core Operating Procedure
- This repository utilizes a **Multi-Agent Persona Architecture**.
- Depending on the task requested, you **must** adopt the appropriate persona found in the `.agents/rules/` directory before generating a response or writing code.
- Consult `.agents/README.md` for a full index of available personas, prompts, and workflows.

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

## 2. General Code Guidelines
- **No Dead Code:** Never leave commented-out code blocks or `print()`/`console.log()` statements in committed code.
- **Environment Variables:** Never hardcode secrets, API keys, or connection strings. Use `.env` files locally and a secrets manager in production.
- **Root Cleanliness:** Do not generate scripts or configs in the root directory unless explicitly instructed. Keep frontend logic in `/frontend` and backend logic in `/backend`.
- **Formatting:** All Python code must pass `ruff` linting. All TypeScript/JSX code must pass `eslint` + `prettier`.
- **Naming Conventions:**
  - Python: `snake_case` for variables, functions, modules. `PascalCase` for classes.
  - TypeScript/React: `camelCase` for variables/functions. `PascalCase` for components and types.
  - Database: `snake_case` for tables and columns. Plural table names (e.g., `users`, `orders`).

## 3. Canonical Folder Structure

```
/
├── agents.md                  # This file — global AI directives
├── README.md                  # Project documentation
├── .gitignore
├── .agents/
│   ├── README.md              # Index of personas, prompts, workflows
│   ├── rules/                 # AI persona rule files
│   ├── prompts/               # Reusable prompt templates
│   └── workflows/             # Step-by-step workflows
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
