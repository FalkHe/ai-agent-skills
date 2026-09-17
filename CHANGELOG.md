# Changelog

## 2026-09-17 - Enhance Public README

Reworked the public README from a rough project note into an overview that
explains what the `fhit` plugin does, how the workflow runs, and how users can
install it. The goal was to make the repository understandable from the first
page without reading the internal workflow docs first.

- Replaced the short project description with a product-level summary of the
  FHIT agent stack.
- Added a work-in-progress status note so users know the workflow is still
  being proven.
- Documented the two workflow phases: human-guided intent work and autonomous
  sprint execution.
- Added command, agent, and skill tables that explain the plugin surface.
- Expanded install, MCP server, scope, and repository layout guidance.

Commit: `b3031bf`

## 2026-09-16 - Refine FHIT Agent Workflow Guidance

Refined the FHIT agent instructions so each agent has clearer professional
boundaries, stronger local-context expectations, and more consistent review
signals. The goal was to make the autonomous sprint flow safer and easier to
judge after implementation.

- Added stronger architecture guidance for professional technical decisions.
- Clarified the difference between architect, implementer, QA, and verifier
  responsibilities.
- Added expectations to use local project documentation and lookup resources
  before making technical claims.
- Standardized verifier status wording around `OK` and `FAIL`.
- Required GitLab merge request approval when verified changes are ready.
- Removed maximum-turn limits from agent definitions.

Commit: `f79861b`

## 2026-09-16 - Write For The Product Owner

Reworked workflow instructions so human-facing text is written for a product
owner instead of for another agent. The change replaces line-count driven
reports with word-count guidance and asks agents to report exceptions and
user-visible outcomes rather than process summaries.

- Added workflow rules for intended reader, word counts, exception reporting,
  and user-visible failure wording.
- Removed scattered report field lists and return-block caps that encouraged
  formulaic output.
- Changed small generated units to sentence limits so prose stays readable.
- Simplified PR/MR bodies into `What changed`, `How to check it`, and
  `Heads-up`.

Commit: `b6167ff`

## 2026-09-15 - Mirror Intents And Sprints Into The Tracker

Connected the FHIT planning model to GitHub and GitLab tracker objects. This
lets intent and sprint state live in the repository docs while still being
visible in the normal issue and milestone workflow.

- Created one milestone per approved intent.
- Created one tracker issue per approved backlog line.
- Attached sprint PRs/MRs to the matching milestone.
- Made sprint PRs/MRs close their sprint issue when merged.
- Documented API choices for GitHub and GitLab milestone and issue handling.

Commit: `c5f1b43`

## 2026-09-15 - Resolve Wish Pointers Before Recording Intents

Changed intent capture so references are followed before the wish is recorded.
This avoids storing vague pointers like "read this file" as the intent and
instead records the actual resolved user need with traceable sources.

- Updated `/fhit:intent` to resolve file, path, URL, and issue references.
- Updated issue and refine flows to use the same reference-resolution behavior.
- Added source tracking for resolved wishes.
- Restated wishes from resolved content before saving them.

Commit: `d2ce5eb`

## 2026-09-15 - Add Docs Lookup And Browser Checks

Added verification skills for framework documentation and rendered UI behavior.
The purpose was to stop agents from inventing APIs or judging frontend work
only from code when installed dependencies and live browser behavior can be
checked.

- Added `docs-lookup` to pin API research to installed dependency versions.
- Added `browser-check` for Playwright specs and live browser observation.
- Wired context7 and Playwright expectations into relevant agents.
- Changed agent tool configuration from allowlists to denylists so MCP tools
  remain available across install styles.
- Added browser acceptance checks to sprint gates and verifier evidence.
- Updated framework skills and documentation to reference the new lookup and
  browser-check flow.

Commit: `e24f8cc`

## 2026-09-14 - Move Plugin Into `plugins/fhit`

Reorganized the repository so the shipped plugin lives under `plugins/fhit/`.
This matches the expected plugin marketplace layout and keeps root-level
documentation out of the packaged plugin.

- Moved plugin metadata, agents, skills, and templates into `plugins/fhit/`.
- Updated the marketplace entry to point at the plugin subdirectory.
- Added a plugin-level README.
- Updated root documentation to describe the new repository layout.

Commit: `ff61f2a`

## 2026-09-14 - Package As The `fhit` Claude Code Plugin

Turned the agent stack into a Claude Code plugin with marketplace metadata and
namespaced commands. This made the workflow installable instead of just being a
collection of repository files.

- Added `.claude-plugin/plugin.json` and marketplace metadata.
- Added `/fhit:init` to scaffold workflow files into a project.
- Added templates for `AGENTS.md` and `docs/architecture.md`.
- Namespaced command references under `/fhit:*`.
- Documented the deployment approach and harness decision.

Commit: `cb47817`

## 2026-09-14 - Add The FHIT Agent Stack

Added the first complete version of the FHIT workflow: intent capture,
decision gates, backlog slicing, autonomous sprint execution, acceptance
testing, and verification. This established the repository's main product
shape.

- Added eight agents: architect, UX designer, UI designer, frontend,
  backend PHP, backend Python, QA, and verifier.
- Added command skills for intent, issue, backlog, refine, sprint, and status.
- Added knowledge skills for workflow, Git flow, Laravel, FastAPI, Typer,
  React/Next.js, Vue, Tailwind, MUI, and Docker development.
- Added workflow templates for intent docs, decisions, research, plans,
  backlogs, sprint briefs, and progress.
- Added initial workflow, stack, discussion roadmap, and instruction guideline
  documentation.

Commit: `84a1fc7`

## 2026-09-14 - Update Local Claude Instructions

Adjusted the repository-local Claude instructions after the initial project
setup. This kept the working guidance aligned with the evolving repository
conventions.

- Updated `.claude/CLAUDE.md`.

Commit: `409c614`

## 2026-09-14 - Add Basic Project Information

Added the first project-level information files so the repository described
what it is and how agents should work with it.

- Added `.claude/CLAUDE.md`.
- Updated `README.md` with basic project information.

Commit: `0c2fc73`

## 2026-09-03 - Initial Commit

Started the repository with the initial README. This created the project
baseline that later commits expanded into the FHIT plugin and workflow.

- Added `README.md`.

Commit: `0fa30d5`
