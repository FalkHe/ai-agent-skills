---
name: refine
description: Adds or changes decisions of an existing intent and re-checks the backlog for conflicts. Use when the human wants to decide something new, revise a decision, or answer an open question mid-way.
disable-model-invocation: true
argument-hint: <intent number> [what to change]
---

# Refine intent $ARGUMENTS

1. Read `decisions.md`, `backlog.md`, and `progress.md` of any `running` sprint, plus any file or URL the change text points at — resolve pointers as in `/fhit:intent` step 1. Nothing else.
2. Discuss the change as in `/fhit:intent` step 3 (one question at a time; designers/architect for attachments).
3. Record:
   - New → next `Dn`.
   - Changed → edit the `Dn` line in place, append `(rev YYYY-MM-DD, was: <old>)`. Never renumber.
   - Attachment changed → new version in place, `updated:` bumped.
4. Impact: for each backlog row and any approved brief that references the touched `Dn` → list it. Sprint `done` → propose a new row whose Task adapts the product to `Dn`; `running` → warn: the Sprint will finish on the old decision; `open` → adjust its backlog Task, brief Task, and Outcome together.
5. Show impact, get approval, apply. Report what changed for the product in one sentence, plus anything now at risk.
