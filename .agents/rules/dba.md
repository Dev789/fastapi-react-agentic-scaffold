# Database Administrator (DBA) Rules

## 1. Migrations
- All schema changes **must** use Alembic. No manual DDL in production.
- Each migration must have a descriptive message: `alembic revision --autogenerate -m "add_users_table"`.
- Every `upgrade()` must have a corresponding `downgrade()` that fully reverts the change.
- Migrations are immutable once merged to `main`. Fix forward, never edit a released migration.

## 2. Naming Conventions
| Object | Convention | Example |
|---|---|---|
| Tables | `snake_case`, plural | `users`, `order_items` |
| Columns | `snake_case` | `first_name`, `created_at` |
| Primary Keys | `id` | `id UUID` |
| Foreign Keys | `<singular_table>_id` | `user_id`, `order_id` |
| Indices | `ix_<table>_<column(s)>` | `ix_users_email` |
| Unique Constraints | `uq_<table>_<column(s)>` | `uq_users_email` |
| Check Constraints | `ck_<table>_<description>` | `ck_orders_positive_total` |

## 3. Audit Columns
Every table **must** include:
- `id` — UUID v7 primary key (sortable, time-ordered).
- `created_at` — `TIMESTAMP WITH TIME ZONE`, server default `now()`, not nullable.
- `updated_at` — `TIMESTAMP WITH TIME ZONE`, auto-updated on row modification.

Define these in a shared SQLAlchemy mixin (`app/models/mixins.py`) so they are never forgotten.

## 4. Soft Deletes
- Prefer soft deletes over hard deletes for user-facing resources.
- Add `is_deleted: bool = False` and `deleted_at: Optional[datetime]` columns.
- All queries must filter `WHERE is_deleted = FALSE` by default. Use a query mixin or default scope.
- Provide an admin-only endpoint for hard purges with a retention policy.

## 5. Query Optimization
- Watch for **N+1 queries**. Use `selectin` or `joined` eager loading for known relationships.
- Add **B-Tree indices** on columns used in `WHERE`, `ORDER BY`, and `JOIN` clauses.
- Add **GIN indices** for `JSONB` columns and full-text search (`tsvector`).
- Use `EXPLAIN ANALYZE` to validate query plans for any query touching > 10k rows.

## 6. Connection Pooling
- Use SQLAlchemy's async pool with `asyncpg`. Configure:
  - `pool_size`: 10 (per instance).
  - `max_overflow`: 20.
  - `pool_timeout`: 30 seconds.
- In production, consider PgBouncer for connection multiplexing.

## 7. Data Seeding
- Maintain seed data scripts in `backend/app/db/init_db.py`.
- Seed data must be idempotent (safe to run multiple times).
- Separate seed data from migrations — seeds are not schema changes.

## 8. Backups & Safety
- Production databases must have automated daily backups with 30-day retention.
- Test restore procedures quarterly.
- Never run destructive queries (`DROP`, `TRUNCATE`, `DELETE` without `WHERE`) without a transaction and peer review.
