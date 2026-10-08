# Building a spec across subagents

Read this when more than one ticket runs in a batch. The whole batch builds on one integration
branch. Each ticket builds in its own worktree, branched from the integration branch as it
stands when that ticket is dispatched. A ticket therefore starts with its blockers' code
already merged.

Do not use the Agent tool's `isolation: "worktree"`. It branches from `origin/<default>`, so
a ticket would start without its blockers' code and without anything not yet pushed. Create
the worktrees yourself.

## Frame it

Write down, and show the user:

- **Done condition.** What has to be true for the batch to be worth running.
- **The graph.** Which tickets are blocked by which. A ticket is ready when every blocker has
  merged into the integration branch.
- **N.** The most workers that run at once. Only as many as the graph allows, and no more
  than about six, where aggregation quality drops faster than throughput improves.
- **Agent and model per ticket.** State them rather than inheriting. `implementer` for
  building, `scout` for pure investigation, `critic` for adversarial design work.
- **Branches.** The starting branch, which is the current branch. The integration branch,
  named after the spec and created from the starting branch. One branch per ticket. Follow the branch names already in
  `git branch -a`. Where a name already exists from an earlier run, add a numeric suffix and
  say so.
- **Uncommitted changes.** Any that `git status` shows, other than the spec and ticket files.
  They do not reach any worktree, so ask whether the user wants them committed first.
- **The ending.** The run ends with `review-code`, a fix pass over every finding, and a draft
  PR from the integration branch into the starting branch, opened through `raise-pr`.

Show the frame and wait for approval. This is the gate, and it is not optional. Approval
covers the run up to the draft PR and nothing past it.

## Prepare

Put worktrees in `<scratch>/worktrees/` and worker reports in `<scratch>/implement/<run>/`.
The scratch directory is `.scratch/` unless `docs/agents/dotclaude.md` names another. Use
absolute paths in everything you hand a worker. Run every `git` command for the batch in the
integration worktree, with `git -C <integration worktree>`. The user's own checkout is never
touched.

1. Create the integration worktree:
   `git worktree add -b <integration-branch> <scratch>/worktrees/integration HEAD`.
2. When the tickets are files rather than GitHub issues, copy the spec and the ticket files
   from the user's checkout into the integration worktree, and commit them there. Every ticket
   worktree then has them.
3. Run the repo's own install step in the integration worktree. Gitignored directories such as
   `node_modules` and files such as `.env` are absent from a new worktree.

## Dispatch

Keep N workers running while tickets are ready. For each ticket you start:

1. Create its worktree from the integration tip:
   `git worktree add -b <ticket-branch> <scratch>/worktrees/<ticket> <integration-branch>`.
2. Write the brief: the ticket verbatim, with this header above it.

   ```markdown
   Worktree: <absolute path>. Work only inside it.
   Branch: <ticket-branch>
   Issue: #<N>, or none
   Report: <absolute path>/<ticket>.md
   Run the repo's install step before building. Run narrow checks only. Do not start the
   app, run end-to-end tests, or bind a port.
   ```

3. Dispatch an `implementer` with the brief and `run_in_background: true`.

Track the tickets with TodoWrite.

## Merge as each one reports

Each completion arrives on its own. Handle it, then fill the free slots with tickets that are
ready.

1. Read the worker's report file. Do not paste it into the conversation.
2. Check that the report names the branch and head SHA, and that
   `git -C <worktree> rev-parse HEAD` matches. When either check fails, respawn the worker
   once with the same brief. After a second failure, record the ticket as `BLOCKED` with no
   report. A gap never counts as a pass.
3. Merge only a ticket that built and verified, with at least one commit ahead of the
   integration branch: `git rev-list --count <integration-branch>..<ticket-branch>` is above
   zero. A worker that reports a failed check, a stop, or no commits is `BLOCKED`, and so is
   every ticket that depends on it.
4. Run `git merge --no-ff <ticket-branch>`.
5. When the merge conflicts, run `git merge --abort`. Mark the ticket `BLOCKED`, and every
   ticket that depends on it. Do not resolve the conflict. A conflict means the split was
   wrong, and that is the user's decision. Independent tickets carry on.
6. When the merge succeeds, run `git worktree remove <worktree>` and
   `git branch -d <ticket-branch>`. Keep a blocked ticket's branch and worktree so the user can
   inspect them.

## Finish

When nothing is running and nothing is ready:

1. When no ticket merged, stop. Open no PR, and report why each ticket is blocked.
2. In the integration worktree, run the full suite and the app-level checks the workers were
   told to skip. Where the change has something you can run, call the Skill tool with "run".
3. Return to section 4 of `SKILL.md` and work on the integration branch. The fix pass runs in
   the integration worktree. `raise-pr` gets the integration branch and the starting branch
   as its arguments.
4. When `raise-pr` returns, whether or not it opened a PR, remove the integration worktree and
   every merged ticket's worktree with `git worktree remove`, then run `git worktree prune`. List the worktrees left for blocked
   tickets in the final report.

## Collapse the reports

Normalise every worker to one status:

| Status    | Meaning                                   |
| :-------- | :---------------------------------------- |
| `DONE`    | Built, verified, and merged.              |
| `ISSUES`  | Merged, but something needs attention.    |
| `BLOCKED` | Not merged. The work is not done.         |

Then report:

```markdown
## Result

<Two or three sentences answering the done condition, and the PR URL. This is the part that
gets read.>

## Tickets

| Ticket | Status | Headline |
| :----- | :----- | :------- |
| 001    | DONE   | ...      |
| 002    | ISSUES | ...      |

## Out of scope

<Everything the workers found and deliberately left alone, worst first. Each with the ticket
that found it.>

## Gaps

<Anything BLOCKED and why, and any spec requirement no ticket covered. Omit the heading if
there are none.>
```

One line per row. A headline is a claim, not a summary of what the worker did.

## Fidelity

- **A dropout is a finding.** A worker that fails, times out, or returns nothing gets its row
  and names the gap. Never silently report N-1 as N.
- **Confidence carries through unchanged.** Where a worker said "possibly", the report says
  "possibly".
- **A finding you do not understand** goes in as the worker stated it, marked unverified.

Before writing the final report, call the Skill tool with
"oliverjwroberts-dotclaude:unslop".
