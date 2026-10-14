---
name: ship
description: Turn completed work, including mixed uncommitted changes, into coherent commits and one or more GitHub pull requests, independent or stacked according to their dependencies. Monitor checks and reviews, fix issues, and merge when requested. Use when asked to open a PR, watch for feedback, ship, land, or handle any part of the PR lifecycle.
---

# Ship

## Objective

Carry intentional code changes to the endpoint the user asks for, and no further:

- **Open:** create the PRs and leave them open.
- **Watch:** open or continue PRs, then address checks and reviews during the session; the PRs stay open.
- **Ship / land:** merge and verify the result.

Do the prerequisite branching, committing, and corrective pushes within that scope. Never report an outcome you have not verified.

## 1. Inspect and partition

Read the repository's instructions. Inspect the current and base branches, the remote, existing PRs, the full `base...HEAD` diff, staged and unstaged changes, and untracked files. Before staging, check for credentials, generated files, and other unintended content.

Include all intentional work unless the user named a subset. Preserve unrelated or uncertain changes, and ask about ownership only when the answer changes what can safely ship.

Group changes into units of behavior, not files. Keep a unit's code, tests, and docs together even across files; separate unrelated behavior even within one file, using selective hunk staging. Each commit should be reviewable and valid in the order it will land.

Choose the smallest PR graph that keeps boundaries honest:

- **One PR** for related commits that share a purpose and can be reviewed and reverted together.
- **Independent PRs** from the same base for changes that build and test without each other.
- **Stacked PRs** only when one change needs another to compile, test, or make sense. Put foundations below consumers; each PR shows only its own layer against its parent.

Don't split a single dependent change just to make more PRs. If an apparently independent change turns out to rely on another, revise the graph rather than publish a misleading diff. Continue an existing PR instead of duplicating it. Share the proposed grouping in a brief progress update and proceed once the boundaries are clear.

## 2. Prepare commits and branches

Run relevant local checks for each unit, and for the integrated result where units depend on each other.

Create branches from the intended base, and child branches from their parent in a stack. When the working tree holds several groups, isolate them with a temporary index, staging branch, or worktrees. Record any pre-existing staged changes and keep their grouping; avoid indiscriminate `git add -A`. A new branch name does not shed unrelated commits inherited from the current branch, so check ancestry.

Before pushing, inspect every staged diff, every commit, and each PR's eventual `base...head` diff, and confirm that local changes outside the task are still intact.

Push only branches this task owns. No destructive resets, ordinary force pushes, or direct pushes to the base branch. If a lower-layer fix must be propagated through a published stack, confirm ownership and remote state, then use the repository's stack tooling or `--force-with-lease` on the affected task branches only. Stop if anyone else's work could be overwritten, and rerun affected checks after the rewrite.

## 3. Open the PRs

Open independent PRs against the intended base. Open a stack bottom-up, each child targeting the branch directly below it; use GitHub's stack features when available, or ordinary PRs with explicit parent bases. Confirm each diff contains only its intended unit before publishing. Use a concise title that describes the outcome.

Write the shortest description that tells a reviewer what changed, why, and how it was checked. A small PR needs a short paragraph and a verification line:

```markdown
Fix the reminder using the previous day's date after midnight. The date now comes from the user's timezone.

Verification: The midnight-boundary test and relevant checks pass.
```

For larger changes, use `## Summary` and `## Verification` when headings help readers scan. Add design decisions, before/after evidence, screenshots, rollout steps, or risks only when they affect review or merging. Name relevant checks that could not run. For a stack, link the parent PR, state the review base and merge order, and update links once the stack exists. No boilerplate sections, and no results you did not observe.

## 4. Watch and repair

Monitor PRs in scope during the session, normally polling every 30–60 seconds. Read live PR state, checks and failed-job logs, published reviews and comments, and all unresolved review threads, paginating as needed. Handle feedback already present on the first pass; ignore unpublished reviews. A pass on an old SHA does not validate a new push or a changed base.

- **Feedback:** Verify it against the current code. Fix valid issues in the unit that owns them, run focused checks, commit, push, and recheck that PR and any descendants. Resolve your own or automated threads only after verifying the fix; leave other people's threads to them unless the user authorized resolving them. Surface requests that need a human answer or renewed approval.
- **Failed checks:** Diagnose before changing code. Fix branch-caused failures at their source. Retry likely transient failures up to three times; stop with a precise blocker when a failure recurs with no new diagnosis or an external service is down.
- **Moving bases and stacks:** When the base moves or a lower layer changes, is rebased, or is retargeted, recheck every affected diff, check, and review state. Each remaining child must still contain only its own change. Use the repository's stack workflow if it has one; otherwise update dependents carefully and verify their PR bases.

Keep watching pending checks and required or explicitly requested reviews until the requested endpoint is reached or a blocker remains. If no checks appear, give expected workflows time to register and inspect repository settings before concluding none apply. Don't invent an optional reviewer as a merge gate.

## 5. Merge and sync

Merge only when merging is in scope. Merge independent PRs once their gates pass, and stacks from the bottom up. A stack tool may land several layers together only if every included PR is ready.

Immediately before each merge, freshly confirm the latest head, passing checks for that revision, valid required approvals, no actionable feedback or unresolved blocking threads, and mergeability. Honor branch protections and the repository's merge strategy. Never self-approve or use an admin bypass. If a person must approve or a permission blocks progress, keep watching while the session can run, or report the blocker.

After each merge, verify the PR's merged state and the resulting commit on the intended base. In a stack, recheck the next child's base, diff, checks, and approvals before continuing.

Then sync local state: fetch, fast-forward local target branches, update dependent local branches and worktrees, and move off a merged task branch onto the updated base. Delete owned merged branches once they're no longer needed. Preserve unrelated staged and unstaged changes; if a branch can't be updated safely, leave it intact and report the pending sync.

## 6. Report

Lead with the verified outcome and link the PRs. Match length to what the user needs: a clean merge may take one sentence, while several PRs in different states may warrant compact bullets or a table. Mention checks, caveats, or remaining work only when they affect the outcome, and don't restate the PR description.

For example: `Merged [#42: Fix reminder timezone](PR URL). Tests and CI passed.`

If the endpoint leaves PRs open, report observed checks and feedback, any pending next action, and that monitoring ends with this session. If blocked, link the open PRs, name the exact gate and next action, and identify intentional local changes left untouched. Never report a PR as merged until its merged state is verified.

## Principle

Treat a failed check or review comment as evidence about intended behavior, not a reason to accumulate exceptions. Make the smallest coherent correction, keep commit and PR boundaries intact, and recheck every affected layer.
