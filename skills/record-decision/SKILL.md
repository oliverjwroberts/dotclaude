---
name: record-decision
description: Write an architecture decision record for one contested decision, capturing the alternative that lost and why. Use when a decision has just been made that was genuinely contested, when the user says to record or write up a decision, when someone asks why the code is the way it is, or when a decision needs superseding.
---

Write one ADR for one decision. It records the choice made, the alternatives that lost, and
what that choice costs. It is not a summary of the discussion, and it is not a plan.

An ADR earns its place only when the decision was **contested**. If there was one reasonable
option, there is nothing to record. Say so and stop rather than filing an ADR that says the
obvious thing was chosen.

## Where it lives

`docs/adr/NNNN-kebab-title.md`, unless `docs/agents/dotclaude.md` says
otherwise. Number sequentially from the highest existing file; check first rather than
assuming the directory is empty.

## The format

```markdown
# NNNN. <The decision, as a statement>

Status: Proposed | Accepted | Superseded by [NNNN](NNNN-other.md)
Date: YYYY-MM-DD

## Context

What forced a choice. The constraint, the pressure, or the problem. Enough that a reader in a
year understands the situation without asking anyone.

## Decision

What was decided, in the active voice. "We route all writes through the outbox table."

## Alternatives

Each option that was seriously considered, and the specific reason it lost. One or two lines
each. An ADR with no alternatives is a note, not a decision record.

## Consequences

What this makes easy, what it makes hard, and what it locks in. Include the costs you accepted
knowingly. The consequences section is what a future reader checks against reality.
```

## Rules

- **An accepted ADR is never edited.** A changed mind is a new ADR that supersedes it, and the
  old one gets its `Status` updated to point at the replacement. Editing in place destroys the
  only thing an ADR has that a wiki page does not.
- **Record the losing option honestly.** A strawman alternative makes the record worthless,
  because the reader cannot tell whether the real objection was ever considered.
- **Title it as a statement, not a topic.** "Use Postgres for the event log", not "Database
  choice".
- **One decision per file.** Two decisions bundled together cannot be superseded separately.
- **Do not restate the design.** Reference the spec, the code, or `CONTEXT.md` by path.
- **Fix the status honestly.** `Proposed` is correct when the user has not committed yet.
  Do not write `Accepted` on their behalf.

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". When
the draft is done, call the Skill tool with "oliverjwroberts-dotclaude:unslop".

Show the user the ADR and its path. If it supersedes an earlier one, say which and confirm
before changing that file's status.
