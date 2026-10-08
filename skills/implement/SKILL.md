---
name: implement
description: Build the work described by a ticket, a batch of tickets, or a spec, in this session or across dispatched subagents, and end with a draft PR. Use when a plan is agreed and it is time to write the code, when the user says to build it, implement it, ship it, or hand it to agents, or when tickets exist and need executing.
---

Build exactly what the ticket or spec describes, and nothing else.

The same process holds whether there is one piece of work or ten. What changes is who does
it: you, or subagents you dispatch.

## If you are a subagent

You were dispatched with one ticket. Build it in the worktree and on the branch your brief
names, and work only inside that worktree. Commit with the Skill tool call
"oliverjwroberts-dotclaude:commit", then report.

**Never push, merge, call `review-code`, or call `raise-pr`.** The session that dispatched you
owns those. Never dispatch a subagent of your own. Where this skill says to dispatch `scout`
or `critic`, do that search or that critique yourself. Skip section 4.

## 1. Read the work

Read the ticket or spec in full before touching anything. Read `CONTEXT.md` and `CLAUDE.md`
if the repo has them. Find the repo's own commands for installing, building, testing, and
typechecking from `package.json`, `Makefile`, `pyproject.toml`, `go.mod`, CI config, or the
README. Never assume a toolchain.

Given a spec with no tickets and more than one piece of work in it, call the Skill tool with
"oliverjwroberts-dotclaude:split-tickets" first.

## 2. Decide who builds

| Situation                              | Do this                                  |
| :------------------------------------- | :--------------------------------------- |
| One ticket, small                      | Build it here                            |
| One ticket, large or well isolated     | Dispatch one `implementer`               |
| A spec with tickets, or several tickets | Read [FANOUT.md](FANOUT.md) and follow it |

**Gate before dispatching more than one subagent.** Show the frame in FANOUT.md and wait for
approval. A parallel fan-out is expensive and cannot be un-spent. One subagent on an approved
ticket is a normal step and needs no gate.

**Work on a branch, every run.** When the current branch is the repo's default branch,
create one named after the ticket or the spec, following the branch names already in
`git branch -a`. When the current branch is not the default, build on it. Fan-out creates its
own integration branch from here.

## 3. Build

- **Work in small, verifiable steps.** Each one leaves the repo working. Run the narrow check
  often; run the full suite once at the end.
- **Find the seam before writing.** Dispatch `scout` to locate the modules this touches and
  anything already doing something similar. Reuse beats new code. Say what you reused.
- **Design the interface first.** Name the data shape, the signatures, and the module boundary
  before writing a body. Where the design is contested or expensive to undo, send it to
  `critic` before you build.
- **Match the surrounding code.** Its naming, comment density, error handling, and idioms are
  the spec for style.
- **Test-first where the repo has a suite and the change is testable.** Write the failing test,
  then the code. Where there is no harness, or the change is not unit-testable, say so and
  state how you verified it instead. Do not claim a discipline you did not follow.
- **Verify before reporting.** A change you have not run is not done.
- **Tests passing is not the same as the change working.** Where the change has something you
  can run, call the Skill tool with "run". Where the change deserves a harder look and the
  user is watching, ask them to run `/verify`; it is user-invoked, so you cannot fire it
  yourself. In a fan-out run, or when the user said they would be away, do not wait for it.
  Record that `/verify` was not run. An inconclusive check is a failure, not a pass.

## Behaviour-preserving changes

When the work is restructuring rather than new behaviour, four extra rules bind:

- **Get the safety net green first.** Existing tests, a characterisation test you write now, or
  a recorded before-and-after comparison. Record it passing _before_ touching anything. A
  restructure without this is a rewrite.
- **Map the blast radius.** Dispatch `scout` for every caller, every test, and every document
  that names what you are moving.
- **Never mix a behaviour change into a structural commit.** Find a bug mid-restructure, stop,
  note it, and decide separately whether to fix it.
- **Migrate callers, then delete.** Add the new shape, move the callers, remove the old one.
  Leaving both is how a codebase ends up with two of everything.

## Scope discipline

The ticket's non-scope is binding. When you find something outside it that matters, a bug, a
missing test, a bad abstraction, **report it, do not fix it**. Widening scope is the most
expensive thing you can do here, because whoever wrote the ticket planned around its edges.

Stop and report rather than guessing when the ticket contradicts the code, a named file does
not exist, or verification cannot pass for a reason outside your scope.

## 4. Close out

- **Relay what each subagent built and verified.** The user does not see subagent transcripts.
- **Never report a task done because an agent said so.** Check what it actually changed.
- **Surface everything flagged as out of scope.** That list is the most valuable thing to come
  out of a dispatched run, and it is the thing most often dropped.
- **Say plainly if something failed, was blocked, or could not be verified.** An inconclusive
  check is a failure, not a pass.
- **Record decisions the tickets did not settle.** Where an agent had to make a contested call,
  call the Skill tool with "oliverjwroberts-dotclaude:record-decision" rather than letting it
  evaporate into a report nobody rereads.

Then, in order:

1. **Commit** anything still uncommitted. Call the Skill tool with
   "oliverjwroberts-dotclaude:commit".
2. **Review the combined change.** Call the Skill tool with
   "oliverjwroberts-dotclaude:review-code" on the branch.
3. **Fix every finding** in this run. Do not call `implement` again for it. Fix them here, or
   in one `implementer` when there are many. A finding too large for one fix pass becomes a
   ticket in the format of [TICKET.md](../split-tickets/TICKET.md), filed in the tracker.
   Commit the fixes.
4. **Raise the PR**, only when the work came from a spec or a ticket and the branch has no
   open PR. Call the Skill tool with "oliverjwroberts-dotclaude:raise-pr" and the argument
   `open <branch>`. Pass it which tickets merged, which are blocked, which findings became
   tickets, and which checks ran.

Some runs stop early. When the work is a fix that `review-code` asked for, stop after step 1,
because that review already ran. When the work came from `diagnose`, or the branch already has
an open PR, stop after step 3. In each case, say that nothing has been pushed, and that
`git push` updates an existing PR.
