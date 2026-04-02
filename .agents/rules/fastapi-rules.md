# FastAPI + PostgreSQL Backend Rules

## 1. Stack & Tooling
- **Framework:** FastAPI (latest stable).
- **Database:** PostgreSQL via `asyncpg`. SQLAlchemy 2.0 async ORM.
- **Schemas:** Pydantic v2 with `model_config = ConfigDict(from_attributes=True)`.
- **Migrations:** Alembic (async driver). Every schema change = a migration.
- **Config:** `pydantic-settings` for environment variable management. Settings class in `app/core/config.py`.
- **Formatting:** `ruff` for linting and formatting. Zero warnings policy.

## 2. Project Structure
```
backend/
├── app/
│   ├── api/
│   │   └── v1/
│   │       ├── endpoints/      # One file per resource (users.py, orders.py)
│   │       └── router.py       # Aggregates all endpoint routers
│   ├── core/
│   │   ├── config.py           # Settings (pydantic-settings)
│   │   ├── security.py         # JWT creation, password hashing
│   │   └── dependencies.py     # Reusable Depends() (get_db, get_current_user)
│   ├── crud/                   # Thin DB query functions (one file per model)
│   ├── db/
│   │   ├── base.py             # Declarative base + import all models
│   │   ├── session.py          # AsyncSession factory + engine
│   │   └── init_db.py          # Seed / initialization logic
│   ├── models/                 # SQLAlchemy ORM models (one file per table)
│   ├── schemas/                # Pydantic schemas (one file per resource)
│   ├── services/               # Business logic (complex operations, orchestration)
│   └── main.py                 # App factory, middleware, router inclusion
├── alembic/
├── tests/
│   ├── conftest.py             # Fixtures: async client, test DB session
│   ├── unit/
│   └── integration/
├── alembic.ini
├── pyproject.toml
└── requirements.txt
```

## 3. Routing & Dependency Injection
- Use `APIRouter` per resource. Prefix with `/api/v1/<resource>`.
- Register all routers in `app/api/v1/router.py`, then include in `main.py`.
- Use `Depends()` for:
  - Async DB sessions (`get_db`)
  - Current authenticated user (`get_current_user`)
  - Pagination parameters (`get_pagination`)
- Never access the database directly in route handlers — delegate to `crud/` or `services/`.

## 4. API Documentation (OpenAPI)
FastAPI auto-generates OpenAPI docs — but **you must enrich them**. Bare endpoints with no descriptions are unacceptable.

- Every endpoint must include:
  - `summary`: short one-line description (shown in Swagger sidebar).
  - `description`: detailed explanation with usage notes (Markdown supported).
  - `response_model`: explicit Pydantic schema for the response.
  - `status_code`: explicit expected status code.
  - `responses`: document error responses (400, 401, 404, 409, 422).

- Example:
  ```python
  @router.post(
      "/",
      response_model=ProductOut,
      status_code=status.HTTP_201_CREATED,
      summary="Create a new product",
      description="Creates a new product in the catalog. Requires authentication. "
                  "Returns the created product with generated ID and timestamps.",
      responses={
          409: {"description": "Product with this SKU already exists"},
          422: {"description": "Validation error in request body"},
      },
  )
  async def create_product(
      product_in: ProductCreate,
      db: AsyncSession = Depends(get_db),
      current_user: User = Depends(get_current_user),
  ) -> ProductOut:
      """Create a new product.

      Args:
          product_in: Validated product creation data.
          db: Async database session.
          current_user: Authenticated user making the request.

      Returns:
          The newly created product.

      Raises:
          DuplicateResourceError: If a product with the same SKU exists.
      """
  ```

- **Tags:** Group endpoints by resource using `tags=["Products"]` on the router.
- **Schema examples:** Add `json_schema_extra` with realistic examples to Pydantic schemas:
  ```python
  class ProductCreate(BaseModel):
      name: str
      price: Decimal
      sku: str

      model_config = ConfigDict(
          json_schema_extra={
              "example": {
                  "name": "Wireless Keyboard",
                  "price": 49.99,
                  "sku": "KB-WL-001",
              }
          }
      )
  ```
- **Review before merging:** Open `/docs` locally and verify the auto-generated documentation is clear, complete, and has working examples.

## 5. Database Models
- All models inherit from a shared `Base` in `app/db/base.py`.
- Every model **must** include:
  - `id`: UUID primary key (use `uuid7` for sortable IDs).
  - `created_at`: `DateTime` with server default `now()`.
  - `updated_at`: `DateTime` with `onupdate=now()`.
- Use a mixin or abstract base to enforce the audit columns.
- Relationships: use `relationship()` with `lazy="selectin"` as default to avoid N+1.

## 6. Pydantic Schemas
- Separate schemas per operation: `<Resource>Create`, `<Resource>Update`, `<Resource>Out`.
- `Out` schemas map from ORM models via `from_attributes=True`.
- Never expose internal fields (`hashed_password`, `is_deleted`) in `Out` schemas.
- Use `<Resource>List` schema for paginated responses: `{ items: list[ResourceOut], total: int }`.

## 7. Error Handling
- Raise `HTTPException` with appropriate status codes. Never return raw 500s.
- Use a global exception handler for unhandled exceptions — log the traceback, return a generic error to the client.
- Define custom exception classes in `app/core/exceptions.py` for domain-specific errors (e.g., `ResourceNotFoundError`, `DuplicateResourceError`).
- Return consistent error response shape: `{ "detail": "message" }`.

## 8. Authentication & Authorization
- **Pattern:** OAuth2 with JWT bearer tokens.
- Access tokens: short-lived (15–30 min). Refresh tokens: long-lived (7 days), stored in DB.
- Hash passwords with `bcrypt` via `passlib`.
- Protect routes with `Depends(get_current_user)`. Use role-based guards for admin endpoints.
- Never log or expose tokens in error responses.

## 9. Pagination
- Standardize on offset-based pagination: `?skip=0&limit=20`.
- Max limit = 100. Default limit = 20.
- Return total count in response for frontend pagination controls.

## 10. CORS & Middleware
- Configure CORS in `main.py` with explicit allowed origins from settings. Never use `allow_origins=["*"]` in production.
- Add `RequestID` middleware for tracing.
- Add timing middleware to log slow requests (> 500ms).

## 11. Logging
- Use `structlog` or Python's `logging` with JSON formatter.
- Log at boundaries: incoming requests, outgoing responses, DB queries (in debug), external API calls.
- Never log sensitive data (passwords, tokens, PII).

## 12. Health Checks
- `GET /health` — returns `200` if app is running.
- `GET /health/ready` — checks DB connectivity and returns `200` only if all dependencies are reachable.
