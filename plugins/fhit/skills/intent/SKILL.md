---
name: intent
description: Starts a new intent interactively — captures the human’s wish (resolving any file, path, or URL it points at), researches options, discusses with the human until product-visible questions are decided and approved. Hands off to /backlog.
disable-model-invocation: true
argument-hint: <free text describing the wish>
---

# Intent: $ARGUMENTS

You lead a discussion with the human. Short messages, one question at a time. Facts from research, opinions marked as such.

## 1. Capture
Resolve pointers first. A pointer is any reference to content outside the argument text: `@file`, a path, a URL, an issue number. Read each one, and only the named part of it (one phase, one section). Ambiguous which part → ask, don't guess.
Next number: highest `docs/intents/III-*` + 1 (start `001`). Slug from the wish, ≤4 words.
Write `intent.md`:
- Self-contained wish text → verbatim.
- Pointer → the wish restated from the resolved content, in the human's words where they wrote any. Never store the pointer as the wish.
- Front matter: author = `git config user.name <user.email>`, `stage: approved`, plus `source: <path|url>` per resolved pointer.

## 2. Research
Spawn `architect` in intent mode → `research.md`. Present the options, their trade-offs, your recommendation and the product-visible open questions — in product terms, no technology names unless the human must choose between them. Wait.

## 3. Roast (with the human)
Ask the human every question a product user would notice the answer to — flow, wording, data the user sees, edge cases, out of scope. One at a time. Technical questions you decide yourself later; don't ask them.
Agents give suggestions only, never answers: when a question needs a concrete proposal, spawn `ux-designer` (structure), `ui-designer` decide mode (visual), or `architect` (data/API) → attachment draft → present → the human decides.
Each settled answer → one sentence in `decisions.md` (`Dn`, attachment link). Show it, confirm, continue.
Stop when the human says so or no open product-visible question remains.

## 4. Approve decisions
Show `decisions.md` complete. Human edits / refines until they say approved → `stage: approved`. Not before.
Then `git-flow → Provider steps → Intent approved`: `intent.md` frontmatter `milestone:` = the milestone URL, or `–` without a provider.

## 5. Hand-off
Report the outcome in one sentence, then the next command: `/fhit:backlog III`.

## Rules
- Human layer file over its word cap → you are recording conclusions, not decisions. Move them to `research.md`.
- Never write a decision the human did not state.
