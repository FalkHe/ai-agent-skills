---
name: browser-check
description: Using playwright for frontend work — committed end-to-end tests with the Playwright test runner, and live visual observation through the playwright browser tools. Preloaded into frontend, ui-designer, ux-designer, qa and verifier; not invoked by users.
user-invocable: false
---

# Browser check (playwright)

Two uses, never mixed: **committed tests** (the repository's, reproducible in CI) and **live observation** (yours, this run only, never committed).

## Prerequisite: a running app

Both uses need the app served locally. Start it the project's way (`docker-dev` skill, or the dev command in `AGENTS.md`), wait until it answers, and use only that local URL. Never drive a browser against a staging or production host.

## A. Committed tests — Playwright test runner

For acceptance criteria a user verifies in a browser.

- Spec files live where the project keeps them (`e2e/`, `tests/e2e/`); run `npx playwright test` (single: `npx playwright test <file> --project=chromium`).
- Select by role, label or text — `getByRole('button', { name: 'Sign in' })`. Never by CSS class or DOM position; both change with styling.
- Wait on state, not on time: `await expect(locator).toBeVisible()`. No `waitForTimeout`.
- Assert what the user observes (visible text, URL, disabled state), not internal calls.
- Playwright not installed → report it and use the project's configured browser runner. Do not add the dependency.

## B. Live observation — playwright browser tools

For seeing the real thing: verifying your own change, judging a rendered UI, reading existing screens, diagnosing a failure the test output does not explain. The MCP tools are exposed under different server names depending on how playwright was installed — use whichever playwright variant this session offers.

1. Navigate, then take the **accessibility snapshot** first — it is the cheap, structured view and answers most questions (present, labelled, reachable, in this order).
2. Screenshot only when the question is genuinely visual: layout, spacing, contrast, overflow, truncation, alignment.
3. Responsive: check 390×844 (mobile) and 1280×800 (desktop) when the work item touches layout.
4. After every interaction, read console messages and failed network requests. A clean-looking page with console errors is a `partial`, not a `done`.
5. Close the browser when finished.

## Evidence

Report an observation as one line: what you did → what you saw (`clicked Sign in at 390×844 → error text visible, no console errors`). Screenshots stay outside the repository (the runner's own output directory or a temp path) and are cited by path — never `git add` an image as proof.
