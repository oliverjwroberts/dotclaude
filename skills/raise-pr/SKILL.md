---
name: raise-pr
description: Write a pull request's title and body in the repo's own PR convention, and open it as a draft when asked to raise it. Use when the user asks to raise, open, or create a PR, draft a PR description, fill in a PR template, or asks what to put in the pull request body.
argument-hint: "[open] [branch]"
---

Write the title and body for a pull request covering the work on a branch. Open it as a draft
only when the mode below says to.

## Pick the mode

| Caller                                                               | Mode |
| :------------------------------------------------------------------- | :--- |
| The argument is `open`                                               | Open |
| The user typed `/raise-pr`, or asked in this message to raise, open, or create a PR | Open |
| Anything else, such as "write the PR description"                    | Text |

Decide from the argument and the current message only. An earlier run of `implement` in this
session does not make this call Open.

The branch is the one named in the argument, or the current branch when none is named.

**Text mode never pushes and never runs `gh pr create`.** **Open mode stops at a draft PR.**
It never marks the PR ready, requests reviewers, or merges. Those stay the user's call.

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
`git log <base>..<branch>` and `git diff <base>...<branch> --stat`.

Where the commits or the branch name point at a spec, a ticket, an ADR, or an issue, read it.
Cite it by path or URL. Do not restate it; a restated copy goes stale the moment the original
changes.

When `implement` called you, take the run's result from it: which tickets merged, which are
blocked, which review findings became tickets, and which checks ran.

## 3. Write the body

With no template, read [LAYOUT.md](LAYOUT.md) and follow it. It holds the default layout and
the rules for each section.

Link the work, whatever the layout:

- When `docs/agents/dotclaude.md` says `Tracker: github`, write `Closes #N` for each ticket
  that merged and `Refs #N` for each ticket that did not. Write `Closes #N` for the spec only
  when every one of its tickets merged.
- Otherwise link the spec and tickets by path. Nothing closes automatically.

A PR body is a briefing a reviewer reads in under a minute. Keep it under about 40 lines.
Link logs, SHA lists, and metric tables rather than pasting them.

Before drafting, call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". When
the draft is done, call the Skill tool with "oliverjwroberts-dotclaude:unslop".

## 4. Hand it back

In Text mode, show the title and the body in one fenced block, ready to paste. Say plainly
that nothing has been pushed and no pull request has been opened.

In Open mode:

1. Check that `git remote get-url origin` points at GitHub and that `gh auth status`
   succeeds. If either fails, fall back to Text mode and say which check failed.
2. Run `gh pr view <branch> --json url,state`. If an open PR already exists for the branch,
   do not create another and do not edit it. Show the text and the existing URL.
3. Write the body to a file in the scratch directory, `.scratch/` unless
   `docs/agents/dotclaude.md` names another.
4. Run `git push -u origin <branch>`, then
   `gh pr create --draft --head <branch> --title "<title>" --body-file <file>`.
5. Show the PR URL, and say it is a draft that nobody has reviewed.
