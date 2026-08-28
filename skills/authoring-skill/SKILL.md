---
name: authoring-skill
description: House rules for writing skills, subagent definitions, and agent-facing instruction files in this repo. Use when creating or editing a SKILL.md, a file under agents/, CLAUDE.md, or AGENTS.md.
paths:
  - "**/SKILL.md"
  - "**/CLAUDE.md"
  - "**/AGENTS.md"
  - "**/agents/*.md"
---

You are writing instructions another agent will follow with no chance to ask a question.

Before drafting, call the Skill tool with
"oliverjwroberts-dotclaude:technical-writing". When the draft is done, call the Skill tool
with "oliverjwroberts-dotclaude:unslop". This applies to every file written under this
skill. A `SKILL.md`, an agent definition, and `CLAUDE.md` are prose, and they are read more
often than anything else in the repo.

## The invocation axis

Every skill is one of two kinds, and the choice drives the frontmatter and the description.

|               | User-invoked                                | Model-invoked                         |
| :------------ | :------------------------------------------ | :------------------------------------ |
| Frontmatter   | `disable-model-invocation: true`            | omit it                               |
| Reached by    | The human typing `/name`                    | The human, or any skill, or the model |
| `description` | Human-facing one-liner, triggers stripped   | Trigger-rich: "Use when the user..."  |
| Holds         | Orchestration and timing the human controls | Reusable discipline                   |

Default to model-invoked. A skill you make user-invoked is a skill no other skill can ever
call, so reserve it for acts whose timing only the human knows.

**The invariant**: a user-invoked skill may call model-invoked skills. It can never call
another user-invoked skill, and nothing can call it. When a step depends on a user-invoked
skill, tell the human to run it; do not write it as a Skill tool call.

## Writing the description

This string is the routing signal, and for a model-invoked skill it is the whole reason the
skill ever fires. Lead with the use case. Then the triggers, in the words a user would
actually type. `description` plus `when_to_use` is truncated at 1,536 characters.

A description that describes the skill's internals instead of its trigger is the most common
reason a skill never fires.

## Writing the body

- **Every line is a recurring cost.** Invoked content stays in context for the rest of the
  session. Keep `SKILL.md` well under 500 lines, and aim far lower.
- **Standing instructions, not one-time steps.** The file is not re-read on later turns, so
  anything that should hold for the whole task must be phrased as a rule, not a step.
- **State what to do.** Cut the rationale to the one line that changes a decision. An agent
  does not need to be persuaded.
- **Progressive disclosure.** Heavy reference, templates, long rule sets, and formats go in
  a sibling file, linked from `SKILL.md` with a line saying what is in it and when to read
  it. Unread files cost nothing.
- **Show the format.** Where output shape matters, give a fenced block. It transfers faster
  than a paragraph describing it.

## Calling another skill

Write it as an explicit tool call with the namespaced name:

> Call the Skill tool with "oliverjwroberts-dotclaude:settle".

Not a bare `/settle`, which the model may read as prose. One skill per call: two skills is
two calls, and say so.

## Frontmatter worth knowing

| Field                      | Use it for                                             |
| :------------------------- | :----------------------------------------------------- |
| `disable-model-invocation` | Making a skill user-invoked                            |
| `user-invocable: false`    | Background knowledge that is not an action             |
| `argument-hint`            | Showing expected arguments in autocomplete             |
| `paths`                    | Auto-activating only when working on matching files    |
| `allowed-tools`            | Pre-approving tools for the invoking turn              |
| `model`, `effort`          | Overriding for the invoking turn only, not the session |
| `context: fork`, `agent`   | Running the skill's body as a subagent task            |

`context: fork` only makes sense when the body is an actual task. A skill of guidelines
forked into a subagent gives it rules and nothing to do.

## Writing a subagent

An agent file holds what a skill cannot set: identity, tool grant, model pin, and report
format. The discipline lives in the skill the agent lists under `skills:`. Do not restate it
in the body; two copies drift, and the skill is the one that gets maintained.

- The `description` is what the delegating model routes on. Say what the agent does and when
  to send work to it.
- Grant the narrowest `tools` that let it finish. Read-only agents get no `Edit` or `Write`.
- Pin `model` and `effort` to the role. **Haiku 4.5 rejects `effort`**, so an agent on Haiku
  must not carry that field.
- The body is a system prompt. Write what the agent _is_ and how it reports, not a procedure
  for one task.
- State the report format. A subagent's only output is its final message, and a dump is a
  failed report.

## Writing CLAUDE.md and AGENTS.md

These load into every session in the project, so every line is paid for on every run.

**One testable obligation per bullet.** Its scope is fixed by the heading above it. An
obligation is testable when you could look at a change and say yes or no.

- Good: "Every exported function has a doc comment."
- Good: "Database access goes through `store/`; no other package imports the driver."
- Bad: "Write clean, maintainable code." Nothing to check.
- Bad: "Follow good practices and keep functions small and well named." Three obligations in
  one bullet, none of them checkable.

Belongs in it:

- Commands to build, test, typecheck, and run. The real ones, verified.
- Structural rules: where things live, what may import what.
- Conventions a reader could not infer from the code in five minutes.
- Anything that has already been got wrong once.

Does not belong in it:

- Rationale. That goes in an ADR or `CONTEXT.md`.
- Anything a linter or formatter enforces. Tooling reports it better.
- General programming advice. The model already knows it.
- Anything not yet true. It states what holds, not what is hoped for.

## Done when

- The invocation axis is chosen, and the frontmatter and description match it.
- A model-invoked description names the triggers a user would actually type.
- Nothing in the body would be better as a sibling file.
- No agent body restates discipline that lives in a skill.
- Every cross-skill dependency is an explicit Skill tool call with the namespaced name.
- Every path, command, and skill name in it exists. Check them.
