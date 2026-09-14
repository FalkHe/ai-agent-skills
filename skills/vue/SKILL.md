---
name: vue
description: Vue 3 conventions — Composition API, SFC structure, Pinia, vue-router, vue-i18n, testing with Vitest/Vue Test Utils. Preloaded into frontend; not invoked by users.
user-invocable: false
---

# Vue 3

Detect: Vite/Nuxt, Pinia, router, i18n from `package.json`. Follow what exists.

## SFC
`<script setup lang="ts">`, `defineProps`/`defineEmits` typed. Composables `useX()` in `composables/` for shared logic. One component per file, PascalCase.

## State & routing
Pinia store per domain, setup-style. Route-level data in the page component or a composable. No prop drilling past two levels → provide/inject or store.

## i18n
All user-facing strings via `t('key')`; keys in the existing locale files. No hardcoded text.

## Tests
Vitest + `@vue/test-utils` (`mount`, find by role/text). Pinia: `createTestingPinia`. Name: `it('AC1: …')`.
Run: `npm test -- --run`; single: `vitest run path`.

## Lint / format
`npm run lint`; `vue-tsc --noEmit` if configured.

## Never
Options API in new code. Mutating props. `watch` where `computed` works. `console.log` committed.
