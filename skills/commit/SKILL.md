---
name: commit
description: Split the current changes into logical commits and write their messages in the repo's own convention. Use when the user says to commit, stage, or check something in, or when a piece of work is finished and needs recording in git.
---

Commit the current work in the repo's own convention, split so each commit is one logical
change.

**This skill stops at the commit.** It never pushes and never opens a pull request. Pushing is
outward-facing and hard to unwind, so it stays the user's call.

## 1. Discover the convention

Do this before writing anything. The repo's own convention always wins, including one you
would not have chosen.

| Look at | For |
| :-- | :-- |
| `git log -n 30 --format=%s` | The shape actually in use, and the scopes already taken |
| `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md` | A stated rule |
| `commitlint.config.*`, `.czrc`, `.gitmessage` | An enforced rule. This one is binding |
| `CHANGELOG.md` | Whether messages feed a release tool |

Read the scopes out of the existing log rather than inventing new ones. A repo using `api`,
`web`, and `db` does not want a fourth scope named after the directory you happened to edit.

Say which convention you found and what you inferred it from. Where the log is inconsistent,
follow the most recent twenty commits rather than the oldest.

## 2. Fall back to Conventional Commits

Only when discovery finds nothing. Then: `type(scope): subject`.

| Type | For |
| :-- | :-- |
| `feat` | A capability the user can now use |
| `fix` | A defect repaired |
| `refactor` | Structure changed, behaviour unchanged |
| `perf` | Faster or lighter, behaviour unchanged |
| `docs` | Documentation only |
| `test` | Tests only |
| `build` | Build system, dependencies, packaging |
| `ci` | Pipeline configuration |
| `chore` | Anything the above do not cover |

Scope is optional and names the area, not the file. A breaking change takes `!` before the
colon and a `BREAKING CHANGE:` footer saying what breaks and what replaces it.

## 3. Split the work

One logical change per commit. **Every commit leaves the repo working**, so a split that
produces a broken intermediate is not a split; either reorder it or keep it as one commit.

- A behaviour change never rides in a structural commit. Where you find both, separate them.
- An unrelated fix you made along the way gets its own commit, with its own type.
- Documentation that describes the change belongs with the change. Documentation that stands
  alone does not.
- One commit is a valid answer. Do not manufacture seams to look tidy.

## 4. Stage precisely

Never run `git add -A` without reading what it picks up first. Run `git status --short` and
`git diff --cached --stat`, and check for:

- Secrets: keys, tokens, `.env` files, credentials in a config.
- Generated output, build artifacts, dependency directories.
- Anything the repo should ignore but does not. A file that should have been gitignored is a
  finding worth raising, not something to quietly commit.
- Unrelated work in progress that belongs in a different commit.

## 5. Write the messages

Call the Skill tool with "oliverjwroberts-dotclaude:technical-writing". Then call the Skill
tool with "oliverjwroberts-dotclaude:unslop" on the draft.

Those skills own the prose rules. The format rules are here. A commit body outlives every
other document in the repo and nobody edits it afterwards, which is why the prose standards
apply to it more strictly, not less.

- **Subject.** Imperative mood, under 72 characters, says what changes. "Add retry to the
  upload path", not "Added retries" or "Fixes". Where the convention you found in step 1
  differs, that convention wins.
- **Body.** Says why, not what. The diff already says what.

**Keep it short.** Most commits need a subject and two or three sentences. Write a body only
where the reason is not already in the subject. Past roughly eight lines you are restating
the diff, which the reader can already see.

## 6. Show, then commit

Present the split before running anything: one line per commit, the subject, and the files in
it. Then commit.

When you are done, show `git log --oneline` for what you added, and say plainly that nothing
has been pushed.
