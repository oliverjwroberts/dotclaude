---
name: write-handoff
description: Compact this session into a document a fresh agent can pick up from.
disable-model-invocation: true
argument-hint: "[what the next session should focus on]"
---

Write a handoff so a fresh agent, with none of this conversation, can continue the work.

A handoff is not a spec. A spec says what should be true when the work is done and outlives
the work. A handoff says where things stand right now and dies the moment it is picked up. If
what you actually need is the former, call the Skill tool with
"oliverjwroberts-dotclaude:write-spec" instead.

Save it to `.scratch/handoffs/<short-slug>.md`, or to the scratch directory named in
`docs/agents/dotclaude.md` if that file exists.

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". When
the draft is done, call the Skill tool with "oliverjwroberts-dotclaude:unslop".

## Contents

```markdown
# Handoff: <what this work is>

## Where things stand

Two or three sentences. The state of the work right now, and whether the repo is in a working
state or mid-change.

## Done

What has been completed and verified. Not what was attempted.

## Next

The immediate next actions, in order, specific enough to start on.

## Open questions

Decisions that are not settled, and who or what settles them.

## Watch out for

The things that would waste the next agent's time: a test that was already failing before
this work, a misleading name, an approach tried and abandoned and why.

## Suggested skills

Which skills the next agent should call, and at which point.
```

## Rules

- **Record what was tried and failed.** A dead end nobody wrote down gets rediscovered at full
  price. This is usually the most valuable section.
- **Reference, do not restate.** Specs, tickets, ADRs, commits, and diffs get cited by path or
  URL. The handoff is the connective tissue between them.
- **Distinguish verified from assumed.**
- **Redact secrets.** API keys, tokens, passwords, personal data. Never in a file.
- If the user passed an argument, treat it as the next session's focus and weight the document
  towards it.

Tell the user the path when you are done.
