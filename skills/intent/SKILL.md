---
name: intent
description: Starts a new intent interactively — captures the human's wish verbatim, researches options, discusses with the human until product-visible questions are decided and approved. Hands off to /backlog.
disable-model-invocation: true
argument-hint: <free text describing the wish>
---

# Intent: $ARGUMENTS

You lead a discussion with the human. Short messages, one question at a time. Facts from research, opinions marked as such.

## 1. Capture
Next number: highest `docs/intents/III-*` + 1 (start `001`). Slug from the wish, ≤4 words.
Write `intent.md`: `$ARGUMENTS` verbatim, author = `git config user.name <user.email>`, `stage: approved`.

## 2. Research
Spawn `architect` in intent mode → `research.md`. Present ≤20 lines: options, trade-offs, recommendation, product-visible open questions. Wait.

## 3. Roast (with the human)
Ask the human every question a product user would notice the answer to — flow, wording, data the user sees, edge cases, out of scope. One at a time. Technical questions you decide yourself later; don't ask them.
Agents give suggestions only, never answers: when a question needs a concrete proposal, spawn `ux-designer` (structure), `ui-designer` decide mode (visual), or `architect` (data/API) → attachment draft → present → the human decides.
Each settled answer → one line in `decisions.md` (`Dn`, attachment link). Show the line, confirm, continue.
Stop when the human says so or no open product-visible question remains.

## 4. Approve decisions
Show `decisions.md` complete. Human edits / refines until they say approved → `stage: approved`. Not before.

## 5. Hand-off
Report ≤5 lines: intent path, Dn count, next command: `/backlog III`.

## Rules
- Human layer files ≤50 lines. Over → you are recording conclusions, not decisions. Move them to `research.md`.
- Never write a decision the human did not state.
