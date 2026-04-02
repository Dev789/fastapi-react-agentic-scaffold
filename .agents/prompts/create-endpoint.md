# Task: Create a new FastAPI + PostgreSQL Endpoint

Please generate a new vertical slice for a feature named: **[INSERT FEATURE NAME HERE]**

Follow these exact steps, adhering to `.agents/rules/fastapi-rules.md` and `.agents/rules/dba.md`:

## 1. Model
Create a SQLAlchemy 2.0 async model in `backend/app/models/<resource>.py`:
- Inherit from the shared `Base` and audit mixin (includes `id`, `created_at`, `updated_at`).
- Define all columns with appropriate types and constraints.
- Add indices for columns that will be queried frequently.

## 2. Schema
Create Pydantic v2 schemas in `backend/app/schemas/<resource>.py`:
- `<Resource>Create` — fields required to create the resource.
- `<Resource>Update` — all fields optional (partial update).
- `<Resource>Out` — response schema with `model_config = ConfigDict(from_attributes=True)`. Exclude internal fields.
- `<Resource>List` — paginated response: `{ items: list[<Resource>Out], total: int }`.

## 3. CRUD
Create async DB queries in `backend/app/crud/<resource>.py`:
- `create_<resource>(db, schema) -> model`
- `get_<resource>(db, id) -> model | None`
- `get_<resources>(db, skip, limit) -> (list[model], total)`
- `update_<resource>(db, id, schema) -> model`
- `delete_<resource>(db, id) -> None` (soft delete if applicable)

## 4. Router
Create a FastAPI `APIRouter` in `backend/app/api/v1/endpoints/<resource>.py`:
- Prefix: `/api/v1/<resources>`
- Inject DB session via `Depends(get_db)`.
- Inject current user via `Depends(get_current_user)` for protected routes.
- Endpoints: `POST /`, `GET /`, `GET /{id}`, `PATCH /{id}`, `DELETE /{id}`.
- Return appropriate status codes: `201` for create, `200` for read/update, `204` for delete.

## 5. Register Router
Add the new router to `backend/app/api/v1/router.py`:
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
- `unit/test_<resource>_crud.py` — test CRUD functions with a test DB session.
- `integration/test_<resource>_endpoints.py` — test all endpoints via `httpx.AsyncClient`.
- Cover: happy path, not found (404), duplicate (409), validation error (422), unauthorized (401).

## 8. Checklist
- [ ] Model created with audit columns
- [ ] Schemas created (Create, Update, Out, List)
- [ ] CRUD functions implemented
- [ ] Router created and registered
- [ ] Migration generated and applied
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
