# fhit

Intent → decisions → backlog → autonomous sprints. See repository root README for install; `docs/workflow.md` there for the process.

- `agents/` — architect, ux-designer, ui-designer, backend-php, backend-python, frontend, qa, verifier
- `skills/` — commands `init intent issue backlog refine sprint status`; knowledge `workflow git-flow docs-lookup browser-check laravel fastapi typer react-next vue tailwind mui docker-dev`

Recommended MCP servers: **context7** (agents look up every unfamiliar API at the installed version) and **playwright** (frontend acceptance tests and visual checks). Either install form works; without them agents fall back to local sources and web search, and skip live browser checks.
