---
name: map-domain
description: Build and sharpen a project's domain model in CONTEXT.md: the concepts, the vocabulary, and the boundaries between them. Use when discussing what things in the codebase should be called, when a term seems to mean two things, when onboarding onto an unfamiliar repo, or when writing or editing a CONTEXT.md.
---

Describe what this project is _about_: the concepts it deals in, what each one is called, and
where the boundaries between them fall. The output is `CONTEXT.md`.

`CONTEXT.md` is not `CLAUDE.md`. `CLAUDE.md` states obligations an agent must obey.
`CONTEXT.md` describes a domain so a reader can think in it. Keep them apart; when you find
yourself writing a rule, it belongs in the other file.

## Where it lives

Repo root, beside `CLAUDE.md`, unless
`docs/agents/dotclaude.md` says otherwise. Add a nested `CONTEXT.md` in a
subdirectory only when that subtree genuinely has its own vocabulary. One file with something
to say beats five with nothing.

## Building the map

1. **Harvest the terms actually in use.** Read the code, not your impression of it. Dispatch
   `scout` for the type names, the module names, the table names, and the words that recur in
   comments and commit messages. The vocabulary the code already uses is the starting point,
   even where it is wrong.
2. **Find the collisions.** Two names for one concept, or one name for two. This is the
   finding that makes the exercise worth doing, and it is the thing a fresh reader cannot see.
3. **Draw the boundaries.** Which concepts own which data, and which one changes when a
   requirement changes.
4. **Propose the fixes to the user.** Renaming a concept is their call, not yours. Show the
   collision, recommend a winner, say what it costs to adopt.

## What goes in it

```markdown
# Context: <project>

## What this is

Two or three sentences. What problem this system deals with, in the domain's own terms.

## Concepts

### <Term>

What it is, in one or two sentences. What it owns. What it is often confused with.

## Boundaries

Where one concept ends and the next begins, and which module owns each. Name the seams that
are load-bearing, not every module.

## Not this

Terms that sound like they belong here and do not, with what they actually mean. This
section stops the same wrong assumption being made twice.
```

## Rules

- **One concept, one term.** Fix the vocabulary here and use it everywhere afterwards: in
  code, in specs, in tickets, in commits. Synonym cycling is the thing this file exists to
  kill.
- **A term earns its entry by being ambiguous.** A concept nobody could misread does not need
  a paragraph. A short file gets read.
- **Describe, do not prescribe.** No implementation detail, no obligations, no rationale for
  decisions. A decision belongs in an ADR; call the Skill tool with
  "oliverjwroberts-dotclaude:record-decision".
- **Say what is settled and what is open.** An open naming question written down is cheap; an
  open one mistaken for settled is expensive.
- **Update on contradiction, not on schedule.** When the code and the map disagree, one of
  them is wrong. Say which, and fix that one.

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". This
is an explanation document. When the draft is done, call the Skill tool with
"oliverjwroberts-dotclaude:unslop".

Tell the user the path when you are done.
