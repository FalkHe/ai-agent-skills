---
name: status
description: Shows the state of an intent or sprint from backlog.md and progress.md, and the resume point after an interrupted sprint. Read-only.
disable-model-invocation: true
argument-hint: <intent number> [sprint number]
---

# Status $ARGUMENTS

Read only frontmatter + tables; never research or plan files.

**Intent**: `intent.md` title, milestone URL from frontmatter, decisions count and stage, backlog table with Task + status + issue columns, proposals pending.

**Sprint** (if given): brief stage, `progress.md` table, issues, verify rounds, branch + PR state (`gh pr view` / `glab mr view` if branch exists).

**Resume hint**, one sentence, derived from state:
- backlog not approved → `/fhit:backlog III`
- sprints `running` without a live agent → `/fhit:sprint III NN` re-spawns them (idempotent: done work items are skipped)
- PR open, verify pending → `/fhit:sprint III NN` continues at step 6
- proposals pending → `/fhit:refine III`

This command *is* the status report — the exceptions-only rule (`workflow`) does not apply. Everything else does: product owner's language, human-message word cap.
