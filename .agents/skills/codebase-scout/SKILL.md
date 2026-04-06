---
name: codebase-scout
description: Deeply map and understand a new codebase from scratch. Identify tech stack, core architecture flows, key entry points, and deployment processes. Use when first entering a repository or exploring an unfamiliar feature area.
---

# Codebase Scout Skill

Use this skill to build a "zero-to-one" mental model of any repository. Always consult `.agents/rules/orchestrator.md` and `.agents/rules/architect.md` during discovery.

## 1. Technology Stack Audit
Identify the core technologies and pinned versions. For this repository:

| Layer | Technology | Version | Key Configuration |
|---|---|---|---|
| **Backend** | FastAPI | 0.115.x | `backend/pyproject.toml` |
| **Frontend** | React / Vite | 18.x / 5.x | `frontend/package.json` |
| **Language** | Python / TypeScript | 3.12.x / 5.x | `tsconfig.json` |
| **Database** | PostgreSQL / SQLAlchemy | 2.0.x | `backend/app/db/` |
| **Styling** | Tailwind CSS | 3.x | `tailwind.config.js` |
| **Container** | Docker / Compose | N/A | `docker-compose.yml` |

## 2. Core Architecture Flow
Trace a request from the user's click to the database.

### 2.1 The "Request Life Cycle"
1.  **Browser Interface:** React component in `frontend/src/components/`.
2.  **State Management:** Redux Toolkit slice in `frontend/src/features/`.
3.  **API Client:** Axios instance in `frontend/src/api/`.
4.  **Network Boundary:** FastAPI Router in `backend/app/api/v1/endpoints/`.
5.  **Service/CRUD:** Business logic and DB queries in `backend/app/crud/`.
6.  **Persistence:** SQLAlchemy models in `backend/app/models/`.

## 3. Key Files & Directories
| Path | Purpose |
|---|---|
| `backend/app/main.py` | Backend entry point & app factory. |
| `frontend/src/main.tsx` | Frontend entry point & React mounting. |
| `agents.md` | Global AI directive and persona routing. |
| `.agents/rules/` | Persona-specific rule files. |
| `alembic/` | Database migration history. |

## 4. Local Development Guide
Follow these unified steps to get productive:
1.  **Infrastructure:** `docker-compose up -d db redis`
2.  **Backend:** `pip install -r requirements.txt` -> `alembic upgrade head` -> `uvicorn app.main:app`
3.  **Frontend:** `npm install` -> `npm run dev`
4.  **Testing:** `pytest` (Backend) and `npm run test` (Frontend).

## 5. Deployment & Release Process
1.  **Release Strategy:** Feature branches -> `develop` -> `release/vX.Y.Z` -> `main`.
2.  **Staging:** Automatic deployment on `release/*` branch creation.
3.  **Production:** Tagged releases on `main`.
4.  **Rollback:** Docker image revert or `alembic downgrade -1`.

## 6. Discovery Checklist
When scouting a new module or feature:
- [ ] List all entry points (Routes/Pages).
- [ ] Identify the data model (Schemas/Models).
- [ ] Locate the business logic (Services/CRUD).
- [ ] Check for existing tests (Unit/Integration).
- [ ] Audit for security/auth requirements.

> [!NOTE]
> For deep technical decisions, always check the `documents/adrs/` folder for Architecture Decision Records.
