---
name: docker-dev
description: Runs the project's local services (MariaDB, PostgreSQL, Redis, app containers) for tests and manual checks via docker compose. Use when a test suite or check needs running services.
user-invocable: false
---

# Docker for local dev

- `docker compose ps` first. Services already up → use them. Never restart what runs.
- Start: `docker compose up -d <service>`; wait for health: `docker compose ps --format json` until `healthy`, or retry the connection for ≤60 s.
- Run inside the app container if the project does (`docker compose exec app <cmd>`); the `AGENTS.md`/README says which. Otherwise run on host against exposed ports from `.env`.
- Test databases: use the compose-defined test DB or a separate database name; never the dev database.
- Never: `docker compose down -v` (destroys volumes), edit `docker-compose*.yml`, or push images. Missing service → report, don't invent one.
