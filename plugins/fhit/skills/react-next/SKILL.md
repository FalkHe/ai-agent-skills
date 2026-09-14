---
name: react-next
description: React and Next.js conventions — App Router, server vs. client components, data fetching, state, testing with Vitest/Testing Library/Playwright. Preloaded into frontend; not invoked by users.
user-invocable: false
---

# React / Next.js

Detect: `next.config.*` → App Router (`app/`) or Pages; package manager from lockfile; test runner from `package.json`. Follow what exists.

## Components
Server components by default; `'use client'` only for state, effects, browser APIs. One component per file, named export matching the file. Props typed; no `any`.
Colocate: `feature/Component.tsx`, `Component.test.tsx`.

## Data & state
Server: fetch in server components / route handlers. Client: existing data library (TanStack Query, SWR) if present; do not add one. Forms: server actions or existing form library. Global state only if already established.

## Tests
Unit/component: Vitest + Testing Library, query by role/label, never by class. Acceptance (qa): Playwright if configured. Name: `it('AC1: …')`.
Run: `npm test -- --run` / `pnpm test`; single: `vitest run path`.

## Lint / format
`npm run lint`; `prettier --write` if configured; `tsc --noEmit`.

## Never
`useEffect` for derived state. Index as key on mutable lists. `console.log` committed. Inline styles where a token/class exists.
