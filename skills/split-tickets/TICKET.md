# Ticket format

One file per ticket. The reader is a fresh agent with no memory of the conversation, no
access to the discussion, and no way to ask you a question.

```markdown
# NNN. <Goal, as a statement>

Spec: <path or URL>
Blocked by: <ticket numbers, or "nothing">

## Goal

One sentence: what is true after this ticket that is not true now.

## Context

The three or four things the agent needs that it cannot get from reading the files. Why this
change, what it is part of, which decision already settled the approach. Reference the spec
or an ADR by path rather than restating it.

## Scope

- The specific change to make, as a short list.
- Named files or directories where the work belongs, when they are known.

## Not in scope

The things nearby this ticket must not touch. Be explicit. This list is what keeps a parallel
ticket from colliding, and what stops a helpful agent from doubling the diff.

## Interfaces

Any function signature, type, schema, or API shape another ticket depends on. Write it out
exactly. If this ticket defines something a later one consumes, that is the contract, and it
is not the agent's to change.

## How to verify

The exact commands, discovered from the repo, not assumed. What passing looks like. If the
change cannot be verified automatically, say what to check by hand.

## Report back

Files changed, commands run and their outcome, anything found and deliberately left alone,
and decisions this ticket did not settle.
```

## Checks before you file it

- Could an agent that has never seen this conversation act on it? Read it back as that agent.
- Is every path, command, and symbol real? Check, do not assume.
- Does the non-scope section actually prevent collision with the other tickets in this batch?
- Is it under a page? A ticket longer than the work is a spec that has not been decomposed.
