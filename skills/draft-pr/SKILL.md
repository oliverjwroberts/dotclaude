---
name: draft-pr
description: Write the title and description for a pull request in the repo's own PR convention. Use when the user asks to draft a PR, write a PR description, fill in a PR template, or asks what to put in the pull request body.
---

Draft the title and body for a pull request covering the work on this branch.

**This skill stops at the text.** It never pushes, never runs `gh pr create`, and never edits
an open PR. Those are outward-facing and hard to unwind, so they stay the user's call.

## 1. Discover the convention

Do this before writing anything. The repo's own convention always wins.

| Look at | For |
| :-- | :-- |
| `.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/` | The template to fill in. This one is binding |
| `CONTRIBUTING.md` | A stated rule about what a PR must say |
| `gh pr list --state merged --limit 20 --json title,body` | The shape actually in use |
| `git log -n 30 --format=%s` | The title convention, usually the commit convention |

Fill a template heading for heading. Do not add headings of your own to it, and do not delete
a heading you have nothing to say under; write "None" and move on.

Say which convention you found and what you inferred it from.

## 2. Read the change

Find the base branch rather than assuming `main`. Try `git symbolic-ref --short
refs/remotes/origin/HEAD`, and fall back to what the repo's open PRs target. Then read
`git log <base>..HEAD` and `git diff <base>...HEAD --stat`.

Where the commits or the branch name point at a spec, a ticket, an ADR, or an issue, read it.
Cite it by path or URL in the description. Do not restate it; a restated copy goes stale the
moment the original changes, and the reader cannot tell which one is true.

## 3. The shape, when there is no template

Five parts, in this order:

- **Title.** The repo's commit convention, imperative, under 72 characters. One line that
  says what changes.
- **What changed and why.** The reason the diff exists. The diff already says what, so spend
  the words on why this approach and not another.
- **How it was verified.** The exact commands you ran and their outcome. Say plainly what
  failed, what you skipped, and what you could not run. Never claim a check you did not run.
- **Out of scope.** What you found and deliberately left alone, so a reviewer does not report
  it as a miss.
- **Links.** The spec, tickets, ADR, or issue, by path or URL.

Keep it short. A reviewer reads the diff, not an essay about the diff. Where a section has
nothing in it, drop the section rather than padding it.

## 4. Write it

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". When
the draft is done, call the Skill tool with "oliverjwroberts-dotclaude:unslop".

## 5. Hand it back

Show the title and the body in one fenced block, ready to paste. Then say plainly that nothing
has been pushed and no pull request has been opened.
