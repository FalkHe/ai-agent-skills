---
name: typer
description: Typer CLI conventions — command structure, options, output, exit codes, testing with CliRunner. Preloaded into backend-python; not invoked by users.
user-invocable: false
---

# Typer

- One `typer.Typer()` app per module, sub-apps via `app.add_typer()`. Commands are thin; logic in importable functions so tests and the API can reuse them.
- Options with `typer.Option(..., help=)`, required arguments with `typer.Argument`. Enums for fixed choices.
- Output: `rich` if already a dependency, else plain `typer.echo`. Errors to stderr (`err=True`) + `raise typer.Exit(code=1)`. Never exit 0 on failure.
- `--json` flag for anything scripts might consume.
- Tests: `typer.testing.CliRunner().invoke(app, [...])`; assert `exit_code` and output. Name: `test_ac1_…`.
- Never: `sys.exit()` inside logic functions, interactive prompts without a `--yes` bypass.
