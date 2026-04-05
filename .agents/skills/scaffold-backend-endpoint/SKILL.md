---
name: scaffold-backend-endpoint
description: Create a FastAPI backend vertical slice for a new resource, including model, schemas, CRUD, router, migration, and tests. Use when adding a new API endpoint or bootstrapping a backend feature from the repository conventions.
---

# Scaffold Backend Endpoint

Follow these steps while consulting `.agents/rules/fastapi-rules.md`, `.agents/rules/dba.md`, `.agents/rules/security.md`, and `.agents/rules/qa.md`.

## 1. Model
Create a SQLAlchemy 2.0 async model in `backend/app/models/<resource>.py`:
- Inherit from the shared `Base` and the audit mixin.
- Define columns with appropriate types, defaults, and constraints.
- Add indexes for columns that will be queried frequently.

## 2. Schema
Create Pydantic v2 schemas in `backend/app/schemas/<resource>.py`:
- `<Resource>Create` for required creation fields.
- `<Resource>Update` for partial updates.
- `<Resource>Out` with `model_config = ConfigDict(from_attributes=True)`.
- `<Resource>List` for paginated responses: `{ items: list[<Resource>Out], total: int }`.

## 3. CRUD
Create async DB queries in `backend/app/crud/<resource>.py`:
- `create_<resource>(db, schema) -> model`
- `get_<resource>(db, id) -> model | None`
- `get_<resources>(db, skip, limit) -> (list[model], total)`
- `update_<resource>(db, id, schema) -> model`
- `delete_<resource>(db, id) -> None`

Use soft delete when the resource should not be physically removed.

## 4. Router
Create an `APIRouter` in `backend/app/api/v1/endpoints/<resource>.py`:
- Prefix: `/api/v1/<resources>`
- Inject the DB session with `Depends(get_db)`.
- Inject the current user for protected routes with `Depends(get_current_user)`.
- Add `POST /`, `GET /`, `GET /{id}`, `PATCH /{id}`, and `DELETE /{id}`.
- Use appropriate status codes: `201`, `200`, and `204`.
- Enrich the OpenAPI metadata with `summary`, `description`, and `responses`.

## 5. Register Router
Update `backend/app/api/v1/router.py`:

```python
from app.api.v1.endpoints import <resource>

api_router.include_router(<resource>.router, prefix="/<resources>", tags=["<Resources>"])
```

## 6. Migration
Generate and review the Alembic migration:

```bash
cd backend
alembic revision --autogenerate -m "add_<resources>_table"
alembic upgrade head
```

## 7. Tests
Create tests in `backend/tests/`:
- `unit/test_<resource>_crud.py`
- `integration/test_<resource>_endpoints.py`

Cover:
- Happy path
- Not found (`404`)
- Duplicate (`409`)
- Validation error (`422`)
- Unauthorized (`401`) when auth applies

## 8. Checklist
- [ ] Model created with audit columns
- [ ] Schemas created (`Create`, `Update`, `Out`, `List`)
- [ ] CRUD functions implemented
- [ ] Router created and registered
- [ ] Migration generated and applied
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
