---
name: split-tickets
description: Break an agreed spec into bounded tickets that an agent can finish without asking a question. Use once a spec or plan exists and the work needs decomposing, when the user says to split it up, break it down, ticket it, or make tasks out of it.
---

Cut a spec into tickets. A ticket is the unit of dispatch: one agent, one bounded change, one
verification.

The reader of a ticket inherits none of this conversation and cannot ask a follow-up. That
single fact drives every rule here.

## 1. Cut along the seams

Cut where the interface between pieces is small and already decided. A good ticket is one an
agent can finish and verify without a question.

For each ticket, decide what **blocks** it. Tickets touching the same files, or where one
defines an interface another consumes, run in sequence. Everything else can run in parallel.

Two tickets writing the same file in parallel is a merge conflict you chose to create. Do not.

If the spec does not decompose cleanly, say so and go back to the user rather than inventing
seams that are not there. One ticket is a valid answer.

## 2. Write them

Read [TICKET.md](TICKET.md) for the format and fill it for each one.

Before writing, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing".
Tickets are instructions read once by someone who cannot ask a follow-up, so the instruction
rules matter more here than anywhere else: command first, condition before instruction, no
ambiguous pronouns, one term per concept. Use the vocabulary in `CONTEXT.md` if the repo has
one.

## 3. Store them

Default `docs/tickets/NNN-kebab-slug.md`. Read
`docs/agents/dotclaude.md` if it exists; when `Tracker: github`, open one
issue per ticket instead, and confirm `gh auth status` succeeds first. Link every ticket back
to its spec by path or URL.

Tickets are committed rather than kept in scratch. An agent on another machine has to be able
to read them.

## 4. Show the split

Present it compactly before anyone starts work:

- One line per ticket.
- Which run in parallel, which are sequenced, and why.
- Anything in the spec that no ticket covers, and whether that is deliberate.

That last line is the one worth checking. A spec requirement that fell between two tickets is
the most common way delegated work comes back incomplete.

Dispatching is not this skill's job. Call the Skill tool with
"oliverjwroberts-dotclaude:implement" when the split is agreed.
