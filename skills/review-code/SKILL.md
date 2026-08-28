---
name: review-code
description: Review changes since a fixed point along three parallel axes - Correctness, Standards, and Spec - and report them side by side. Use when the user asks to review a branch, a PR, work in progress, or "review since X", or after a change is built and before it is merged.
---

Review a diff along three axes, run in parallel, reported separately. Findings, not fixes:
this skill never edits code.

## 1. Fix the range

Establish exactly what is under review before dispatching anything, and say what you chose.

| The user said  | Range                                                                            |
| :------------- | :------------------------------------------------------------------------------- |
| Nothing        | Working tree plus staged: `git diff HEAD`                                        |
| A branch or PR | Merge-base with the default branch: `git diff $(git merge-base main HEAD)..HEAD` |
| "since X"      | `git diff X..HEAD`, where X is the commit, tag, or branch named                  |

An empty diff is a finding, not a silent pass. Say so and stop.

## 2. Find the spec

Look for what this change was supposed to do: a path the user gave, a ticket or issue
referenced in the commit messages, or a file under `docs/specs/`, `docs/tickets/`, or
`.scratch/`. If there is none, say so and run two axes rather than inventing one.

## 3. Dispatch

Read [AXES.md](AXES.md) and send **three** briefs to the `reviewer` agent in a single message
so they run concurrently. Each brief carries the range, the repo's documented standards where
relevant, the spec path where one exists, and exactly one axis definition from `AXES.md`.

One axis per agent. An agent given two axes will let the louder one mask the quieter one,
which is the whole reason they are separate.

## 4. Report

Three headings, in this order, each under 400 words:

```markdown
## Correctness

<Findings, worst first. Each anchored to file:line with the hunk quoted.>

## Standards

<Findings. Each marked as a hard violation of a documented standard, or a judgement call.>

## Spec

<Findings, each quoting the spec line it comes from. Or: "No spec found; axis skipped.">
```

End with one line per axis: how many findings, and the worst one.

## Rules

- **Never merge or rerank across axes.** A change can follow every convention while
  implementing the wrong thing, or do exactly what was asked while being broken. Keeping the
  axes apart is what stops one masking another.
- **Do not pick an overall winner.** Three verdicts, not one.
- **Anchor every finding to `file:line`** and quote the hunk. An unanchored finding cannot be
  acted on.
- **A dropped axis is reported, not hidden.** If an agent fails or returns nothing, say which
  axis has no coverage.
- **Report, do not fix.** The person who owns the change chooses what to do about each
  finding. Where they ask for fixes, call the Skill tool with
  "oliverjwroberts-dotclaude:implement" afterwards.

Before writing the final report, call the Skill tool with
"oliverjwroberts-dotclaude:unslop".
