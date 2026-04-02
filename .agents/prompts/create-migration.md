# Task: Create an Alembic Database Migration

Please generate a migration for: **[DESCRIBE THE SCHEMA CHANGE HERE]**

Follow these exact steps, adhering to `.agents/rules/dba.md`:

## 1. Verify the Model
Ensure the SQLAlchemy model in `backend/app/models/` reflects the desired schema change:
- New table? Create a new model file with audit columns (`id`, `created_at`, `updated_at`).
- Column change? Update the existing model with the correct type, constraints, and defaults.
- New index? Add `Index()` or `index=True` on the column.

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
- Message format: `add_<table>_table`, `add_<column>_to_<table>`, `create_index_on_<table>_<column>`.

## 4. Review the Generated Migration
Open the generated file in `backend/alembic/versions/` and verify:
- [ ] `upgrade()` contains the expected DDL operations.
- [ ] `downgrade()` fully reverts the `upgrade()` — no orphaned tables, columns, or indices.
- [ ] Column types, defaults, and nullability match the model.
- [ ] Foreign key constraints reference the correct table and column.
- [ ] Index names follow the convention: `ix_<table>_<column>`.

## 5. Apply the Migration
```bash
alembic upgrade head
```

## 6. Verify
- Check the database schema matches expectations.
- Run the test suite to ensure nothing is broken:
```bash
pytest tests/ -v
```

## 7. Rollback Test
Test that the downgrade works:
```bash
alembic downgrade -1
alembic upgrade head
```
