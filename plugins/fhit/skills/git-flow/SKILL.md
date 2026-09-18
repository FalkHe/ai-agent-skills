---
name: git-flow
description: Provider-agnostic branch, commit, review and merge rules, plus the provider steps (milestone, issue, PR/MR, verdict, status) with their plain-git defaults. The github and gitlab skills implement those steps. Preloaded into sprint, verifier and implementer agents; not invoked by users.
user-invocable: false
---

# Git flow

Plain git is the baseline and always sufficient. A repository provider¹ only mirrors what exists in files and git.

¹ Provider = the hosted service around the repository, GitHub or GitLab. Tracker = its milestones and issues.

## Setup

Three lines in `AGENTS.md → Workflow`, written by `/fhit:init`:

- `Provider: github | gitlab | none` — which provider skill to load. `none` = git only: no tracker, no PR/MR
- `Human: <account>` — the product owner on the provider. Reviewer of every PR/MR; the only account whose issue comments count as answers
- `Agent: <account>` — the account `gh` / `glab` is signed in as. Author of issues and PRs/MRs, assignee of sprint issues

Lines missing → act as `none`, tell the human to run `/fhit:init`.

## Branch
`sprint/<III>-<NN>-<slug>` from the default branch. Example: `sprint/001-02-sign-in-email-password`.

## Commits
Conventional: `<type>(<scope>): <summary>` — types `feat fix test refactor docs chore`. Scope = module or work item.
- One commit per work item minimum. Tests red before green: `test(auth): …` then `feat(auth): …`
- Never commit red. Lint + full test suite pass before every push
- Never `--force` on a shared branch, never `--no-verify`

## Review
Every sprint ends in a review: the sprint branch plus `review.md` in the sprint directory (template in `workflow`). The product owner reads it before merging; with a provider it is also the PR/MR body. Content: what changed, how to see it, what to watch out for — in the product's terms. No paths beyond the brief link, no symbols, no test names.

## Verdict
The verifier's judgement of the review, written for the product owner: `approve`, or `changes requested` with one sentence per failure as a user would notice it. Returned to the sprint lead, who appends it to `review.md → Verdict` with the round number.

## Merge
Human only, always. Agents never merge, never close an issue, never delete a branch.

## Provider steps
Every step has a plain-git default that always runs. `Provider` ≠ `none` → load the provider skill once per run (`Skill` tool: `fhit:github` or `fhit:gitlab`), run its `Account` check first, then after each default do what its section of the same name says.

| Step | Runs in | Plain-git default |
|---|---|---|
| Intent approved | `/fhit:intent` 4, `/fhit:issue` 1 | `intent.md` frontmatter `milestone: –` |
| Backlog approved | `/fhit:backlog` 4 | `Issue` column `–` |
| Sprint shipped | `/fhit:sprint` 5 | commit `review.md`; `git push -u origin <branch>` when `origin` exists |
| Review diff | `verifier` | `git diff <default>...<branch>` |
| Verdict | `verifier` → `/fhit:sprint` 6 | return value only; sprint lead writes `review.md → Verdict` and `progress.md → Verify`, commits, pushes |
| Status | `/fhit:status` | branch exists · commits ahead of default · listed in `git branch --merged <default>` |
| Merge | human | `git merge --no-ff <branch>` |

`/fhit:issue` needs a provider; its commands are the provider skill's `Issue thread` section.
