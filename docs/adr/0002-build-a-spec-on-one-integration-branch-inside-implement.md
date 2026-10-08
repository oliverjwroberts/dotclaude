# 0002. Build a whole spec on one integration branch inside `implement`

Status: Accepted
Date: 2026-10-08

## Context

The fan-out in `skills/implement/FANOUT.md` dispatched parallel implementers into one shared
working tree and index. Nobody committed and nothing collected the finished tickets. Matt
Pocock's `implement-spec` (mattpocock/skills v1.3) solves this with a separate skill, an
integration branch, a worktree per ticket, and a merger subagent.

Claude Code's Agent tool offers `isolation: "worktree"`. Its base comes from the
`worktree.baseRef` setting, which defaults to `origin/<default-branch>`. A plugin cannot set
the base per call. A worktree from that base lacks every ticket merged so far, and anything
not yet pushed.

## Decision

Whole-spec fan-out lives in `implement`. Each run builds on one integration branch. The
session creates each ticket's worktree itself with `git worktree add`, branched from the
integration branch tip when that ticket is dispatched. Tickets run in parallel when the
blocking graph allows it, after the user approves the frame. The session merges each finished
ticket into the integration branch in a worktree of its own, then dispatches the tickets that
merge unblocked. The run ends with one draft PR.

## Alternatives

- **A separate `implement-spec` skill**, as Matt ships it. Rejected because `implement`
  already routes by the size of the work. A second skill with an overlapping description
  competes with `implement` for the same triggers.
- **Agent `isolation: "worktree"`.** Rejected because a ticket dispatched after its blocker
  merges would start without the blocker's code. The plugin cannot change the base.
- **A stack of narrow PRs, as pstack's `orchestrate` prefers.** Rejected because the user
  wants one PR to review per spec when they return.
- **Sequential tickets in one worktree, parallel only on request.** This was the cheaper
  option and removes merges entirely. Rejected because the user wants independent tickets to
  run in parallel when the graph allows.

## Consequences

- A dependent ticket starts from code that already includes its blockers.
- The session owns worktree creation, merging, and cleanup. That logic is more than the old
  FANOUT protocol had, and it lives in the skill text with no code to test it.
- Each worktree needs the repo's own install step, because gitignored directories such as
  `node_modules` are absent.
- Parallel workers can collide on ports and databases. They run narrow checks only, and
  app-level checks run once on the integration branch.
- A merge conflict marks that ticket and its dependents `BLOCKED`. Independent tickets carry
  on, and the PR lists what is blocked.
