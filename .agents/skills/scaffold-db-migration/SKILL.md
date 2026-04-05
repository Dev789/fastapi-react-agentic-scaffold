---
name: scaffold-db-migration
description: Create and validate an Alembic migration for schema changes, including model alignment, revision review, upgrade, downgrade, and verification steps. Use when adding or changing tables, columns, constraints, or indexes.
---

# Scaffold DB Migration

Follow these steps while consulting `.agents/rules/dba.md`, `.agents/rules/fastapi-rules.md`, and `.agents/rules/qa.md`.

## 1. Verify the Model
Ensure the SQLAlchemy model in `backend/app/models/` reflects the desired schema change:
- New table: create a new model file with audit columns.
- Column change: update the existing model with the correct type, constraints, and defaults.
- New index: add `Index()` or `index=True`.

## 2. Import the Model
Ensure the model is imported in `backend/app/db/base.py` so Alembic can detect it:

```python
from app.models.<resource> import <Resource>  # noqa: F401
```

## 3. Generate the Migration
```bash
cd backend
alembic revision --autogenerate -m "<descriptive_message>"
```

Use descriptive messages such as:
- `add_<table>_table`
- `add_<column>_to_<table>`
- `create_index_on_<table>_<column>`

## 4. Review the Generated Migration
Open the generated file in `backend/alembic/versions/` and verify:
- [ ] `upgrade()` contains the expected DDL operations
- [ ] `downgrade()` fully reverts `upgrade()`
- [ ] Column types, defaults, and nullability match the model
- [ ] Foreign keys reference the correct tables and columns
- [ ] Index names follow `ix_<table>_<column>`

## 5. Apply the Migration
```bash
alembic upgrade head
```

## 6. Verify
- Check the database schema matches expectations.
- Run the test suite:

```bash
pytest tests/ -v
```

## 7. Rollback Test
```bash
alembic downgrade -1
alembic upgrade head
```
