---
name: fastapi
description: FastAPI conventions and commands for implementing and testing endpoints — routers, Pydantic models, dependencies, pytest, ruff. Preloaded into backend-python; not invoked by users.
user-invocable: false
---

# FastAPI

Detect tooling: `pyproject.toml` → package manager (uv/poetry/pip), ruff/mypy config, pytest config. Use what is there.

## Structure
One `APIRouter` per resource, mounted in `main.py`/`app.py`. Pydantic models: separate `*In`/`*Out`; never return ORM objects. Dependencies (`Depends`) for DB session, current user, settings. Business logic in a service module, not in the route function.

## Data
SQLAlchemy 2.x style (`select()`), sessions via dependency. Alembic migration per schema change; never edit an applied one.

## Tests
`pytest` + `httpx.AsyncClient`/`TestClient` against the app; override dependencies with `app.dependency_overrides`. Fixtures in `conftest.py`. Name: `test_ac1_rejects_wrong_password`.
Run: `pytest -q` (single: `pytest tests/test_x.py -q`).

## Lint / format
`ruff check --fix . && ruff format .`; `mypy` if configured.

## Never
Sync blocking calls in `async def` routes. `print()` committed. Secrets outside settings (`pydantic-settings`). Bare `except:`.
