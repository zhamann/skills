---
name: ship
description: Turn completed work, including mixed uncommitted changes, into coherent commits and one or more GitHub pull requests; use independent or stacked PRs according to dependencies, monitor checks and reviews, fix issues, and merge. Use when asked to ship, land, or take code changes through the complete PR lifecycle.
---

# Ship

Take intentional code changes through a verified merge. Invoking this skill authorizes branching, commits, PR creation, corrective pushes, and merging once the gates below pass. Follow narrower instructions such as "open PRs but leave them unmerged."

## 1. Inventory and partition

Read repository instructions and inspect the current branch, base branch, remote, existing PRs, the full `base...HEAD` diff, staged and unstaged changes, and untracked files. Check for credentials, generated files, and other unintended content before staging. Include all intentional work in scope unless the user specified a subset; preserve unrelated or uncertain changes and ask about ownership only when the answer changes what can safely ship.

Identify coherent units of behavior before committing. Group the code, tests, and documentation for each unit together, even when they occupy different files; separate unrelated behavior even when it occupies one file. Use selective hunk staging or equivalent isolation for mixed files. Keep each commit reviewable and valid in the sequence in which it will land. Do not split a single dependent change merely to make more PRs.

Choose the smallest useful PR graph:

- Put closely related commits in one PR when they share one purpose and can be reviewed and reverted together.
- Use separate PRs from the same base for independent changes. Each must build and test without the other.
- Stack PRs only when one change needs another to compile, test, or make sense. Put shared foundations below consumers; each PR should show only its own layer against its parent branch.

Summarize the proposed grouping and dependencies in a brief progress update, then proceed when the boundaries are clear. If an apparently independent change actually relies on another, revise the graph instead of publishing a misleading diff. Check for an existing PR and continue it when appropriate rather than duplicating it.

## 2. Prepare commits and branches

Run relevant local checks for each unit and the integrated result where dependencies matter. Create descriptive branches from the intended base; create child branches from their parent branch for a stack. When the working tree contains several groups, use an isolated staging branch, temporary index, or worktrees as needed to construct the groups without mixing them. Record pre-existing staged changes and preserve their intended grouping; avoid indiscriminate `git add -A`. Verify that untouched edits and staging remain intact. Inspect every staged diff, commit, and eventual `base...head` PR diff before pushing. Never assume a new branch name removes unrelated commits inherited from the current branch.

Push only branches owned by this task. Avoid destructive resets, ordinary force pushes, and direct pushes to the base branch. If a published stack needs history rewritten to propagate a lower-layer fix, first confirm branch ownership and remote state, then use the repository's stack tooling or `--force-with-lease` on the affected task branches only; stop if another person's work could be overwritten. Keep dependencies correct and rerun affected checks after any rewrite.

## 3. Open the PRs

Open independent PRs against the intended base. Open a stack from bottom to top, with each child targeting the branch immediately below it. Use GitHub's stack features when available; ordinary PRs with explicit parent bases are a valid fallback. Before publishing each PR, confirm its diff contains only its intended unit. Select a concise title describing the outcome.

Write the shortest PR description that lets a reviewer understand what changed, why, and how it was checked. For a small PR, use a short paragraph and a verification line:

```markdown
Fix the reminder using the previous day's date after midnight. The date now comes from the user's timezone.

Verification: The midnight-boundary test and relevant checks pass.
```

For larger changes, use `## Summary` and `## Verification` when headings help readers scan. Add design decisions, before/after evidence, screenshots, rollout steps, or risks when they affect review or merging. State relevant checks that could not be run. For a stack, link the parent PR, identify the review base and merge order, and update links when the stack exists. Do not fill sections with boilerplate or claim unobserved results.

## 4. Watch, repair, and merge

Monitor every open PR in the same session, normally polling every 30–60 seconds. Use live PR state, checks and failed-job logs, published reviews and comments, and paginated unresolved review threads. Inspect feedback already present at the first pass. Ignore unpublished reviews. A passing result on an old SHA does not validate a new push or a changed base.

- Verify feedback against the latest code. Fix valid issues in the appropriate unit, run focused checks, commit, push, and recheck that PR and any descendants. Resolve your own or automated threads only after verifying the fix; let other humans review their threads unless the user authorized resolution. Surface requests that need a human response or renewed approval.
- Diagnose failed checks before acting. Fix branch-related failures at their source; retry likely transient failures up to three times. Stop with a precise blocker when a recurring failure has no new diagnosis or an external service is unavailable.
- When the base moves, a lower layer changes, or a stack is rebased or retargeted, inspect every affected diff and review state again. Ensure each remaining child still contains only its own change. Rerun checks required for the resulting heads. Use the repository's stack workflow if supported; otherwise update dependent branches carefully and verify the resulting PR bases.
- Continue watching pending checks and required or explicitly requested reviews. If no checks appear, allow expected workflows to register and inspect repository settings before concluding none apply. Do not invent an optional reviewer as a merge gate.

Merge each independent PR once its gates pass. Merge stacked PRs from the bottom up, verifying the next child's base, diff, checks, and approvals after each parent merge; a stack tool may land multiple layers together only if every included PR is ready. Honor branch protections and the repository's merge strategy. Never self-approve or use an admin bypass. Before each merge, freshly confirm the latest head, applicable checks, valid required approvals, absence of actionable feedback and unresolved threads, and mergeability. If a person must approve or a permission blocks progress, keep watching while the session can run or report the blocker; do not imply background monitoring continues after the session.

Verify each PR's merged state and resulting commit on the intended base. Leave unrelated local changes intact.

## 5. Report to the user

Lead with the verified outcome and link the PRs. Match the length to what the user needs: a straightforward merge may need only a sentence, while several PRs or different statuses may warrant compact bullets or a table. Mention checks, meaningful caveats, or remaining work when they affect the outcome; do not restate the PR description.

For example: `Merged [#42: Fix reminder timezone](PR URL). Tests and CI passed.`

If blocked, link the open PRs, name the exact gate and next action, and identify intentional local changes left untouched. Never report a PR as merged until its merged state is verified.

## Principle check

Treat a failed run or review comment as evidence about desired behavior, not a reason to accumulate exceptions. Make the smallest correction that preserves coherent commit and PR boundaries, then recheck every affected layer.
