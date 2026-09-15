---
name: docs-lookup
description: How to obtain current, version-correct API documentation — context7 first, then local sources, then web search, always pinned to the installed version. Preloaded into researching and implementing agents; not invoked by users.
user-invocable: false
---

# Docs lookup

Your training data is older than the installed packages. Look up any framework, library, SDK, CLI or cloud-service API before you use it, unless this repository already demonstrates that exact usage (then copy the repository).

## 1. Pin the version — before reading any doc

Read the installed version, never guess it:

| Ecosystem | Command |
|---|---|
| PHP | `composer show <vendor/pkg>` (falls back to `composer.lock`) |
| Python | `pip show <pkg>` / `uv pip show <pkg>`, else `uv.lock`, `poetry.lock` |
| JS/TS | `npm ls <pkg>` / `pnpm why <pkg>`, else the lockfile |

Manifest ranges (`composer.json`, `pyproject.toml`, `package.json`) are constraints, not installed versions — use them only when no lockfile and no installed package exist, and mark the result `ASSUMPTION`.

## 2. Ask context7

context7 is the default documentation source. Its MCP tools are exposed under different server names depending on how it was installed — use whichever context7 variant this session offers.

1. Resolve the library id. Pick the entry whose version matches step 1; ask for the versioned id (`/org/project/<version>`) when context7 lists versions.
2. Query one concept per call, phrased as the task: "Laravel 12 form request validation rules", not "validation".

## 3. Fallbacks — only when context7 returns no usable answer

In this order:

1. **Local**: installed source and its bundled docs (`vendor/`, `node_modules/`, site-packages), the package `README`/`CHANGELOG`, project `docs/`. This is authoritative — it is the code that will run.
2. **Web search**: the vendor's official documentation domain only. No blog posts, no Stack Overflow answers, no AI-generated tutorial sites.

## 4. Verify the version before you use what you found

Every doc page states or implies a version. Compare it with step 1:

- Match → use it.
- Doc newer than installed → use the installed version's doc instead; a feature that does not exist in the installed version is unusable. Never upgrade a dependency to make a doc fit.
- Doc older, or version unknowable → treat the API as unverified.

Unverified after all three sources → do not invent the call. Implementer: return `blocked` naming the API. Researcher: list it under open questions.

## Cite what you used

One line where your output already reports facts: `<pkg> <version> — <source: context7 | local | <domain>>`. Research facts and interface contracts that rest on an external API carry this line.
