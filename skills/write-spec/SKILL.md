---
name: write-spec
description: Turn the current conversation and codebase understanding into a written spec, without interviewing the user. Use when a plan has been agreed and needs writing down, when the user says to write it up, spec it out, or file it, or when work is about to be handed to someone who was not in this conversation.
---

Synthesise what has already been decided into a spec. **Do not interview.** Everything you
need is in this conversation and in the code. If it is genuinely not, call the Skill tool with
"oliverjwroberts-dotclaude:settle" instead of guessing, and come back.

The reader is someone who was not here: a future session, another person, or a subagent. They
get this document and the repo, nothing else.

## Where it goes

Default `docs/specs/<kebab-slug>.md`. Read
`docs/agents/dotclaude.md` if it exists; when `Tracker: github`, open a
GitHub issue with the same body instead, and confirm `gh auth status` succeeds before trying.
If the tracker is unreachable, write the file and say so rather than losing the work.

Label the issue `spec`. Run `gh label create spec --description "A dotclaude spec" --color
0E8A16 --force` first, because `gh issue create --label spec` fails on a repo that does not
have the label yet. `--force` updates an existing label rather than failing, so it is safe to
run every time. The label is what lets a later session list the specs and read their titles
instead of guessing at search terms.

Read `CONTEXT.md` if the repo has one and use the vocabulary in it. A spec that renames the
domain's concepts forces every reader to translate.

## The format

```markdown
# <What this delivers, as a statement>

## Problem

What is wrong or missing now, and for whom. Concrete. A reader who disagrees with this
section should stop reading and argue here.

## Outcome

What is true when this is done, in one or two sentences. This is the acceptance test in
prose.

## Approach

The shape of the solution, and the decisions already settled. Reference an ADR by path where
one exists rather than re-arguing the decision.

## Scope

The specific changes, as a list. Name real files and modules where they are known.

## Not in scope

What this deliberately does not do, and why. This section prevents more wasted work than
any other.

## Constraints

What the solution must hold to: interfaces it cannot break, performance it must keep,
compatibility it must preserve.

## Open questions

What is genuinely unsettled, and who settles it. Empty is a valid answer and a good sign.

## Verification

How anyone knows this worked. The commands, discovered from the repo, and what passing looks
like. Where it cannot be checked automatically, what to check by hand.
```

## Rules

- **Record what was decided, not the discussion.** The conversation's back-and-forth is noise
  to the reader. Give them the conclusion and the reason it beat the alternative.
- **Every path, command, and symbol is real.** Check them. A plausible invented path costs
  the reader more than no example.
- **Distinguish verified from assumed.** Mark anything you inferred rather than confirmed.
- **Say what you are unsure of** in Open questions rather than smoothing it over. A spec that
  hides its soft spots gets built wrong confidently.
- **Under two pages.** A spec nobody finishes is a spec nobody follows.

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". When
the draft is done, call the Skill tool with "oliverjwroberts-dotclaude:unslop".

Show the user the path or the issue URL. Say plainly what you assumed where the conversation
did not settle something.
