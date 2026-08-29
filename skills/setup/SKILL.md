---
name: setup
description: Configure this repo's artifact locations and tracker. Run once per repo; the skills work without it.
disable-model-invocation: true
---

Write `docs/agents/dotclaude.md` for this repo. Everything in it is an override. Every
skill in this plugin works correctly without the file, using the defaults shown below.

**That path is fixed.** Every other location here is configurable, but the config itself
cannot be, because a skill has to know where to look before it has read anything. Create
`docs/agents/` if it does not exist.

Ask only about what this repo would actually change. Do not walk the user through settings
they have no reason to touch.

## 1. Artifacts

| Setting | Default              | Ask about it when                                           |
| :------ | :------------------- | :---------------------------------------------------------- |
| Context | `CONTEXT.md` at root | The repo puts domain documentation elsewhere                |
| ADRs    | `docs/adr/`          | The repo already has decision records somewhere else        |
| Specs   | `docs/specs/`        | The repo has a different home for specs                     |
| Tickets | `docs/tickets/`      | The tracker is `github`, or the repo has its own convention |
| Scratch | `.scratch/`          | The repo has a convention, or `.scratch/` is not gitignored |
| Docs    | `docs/`              | The repo puts documentation somewhere else                  |

## 2. Tracker

Where `write-spec` and `split-tickets` put their output.

| Value    | Behaviour                                                            |
| :------- | :------------------------------------------------------------------- |
| `local`  | Markdown files under the Specs and Tickets directories. **Default.** |
| `github` | One GitHub issue per spec and per ticket.                            |

`local` is the default because it works in any repo with no authentication, which is what the
zero-setup rule requires.

If they pick `github`, confirm `gh auth status` succeeds and the repo has a GitHub remote. If
either is missing, say so and leave the tracker as `local` rather than writing a setting that
fails later.

## 3. Agent models and effort

**This file does not configure either.** Each agent's frontmatter in `agents/` is the only
source of truth, and nothing reads a model or effort setting from the config. Never write one.

Raise this section only if the user asks what the agents run on.

| Agent         | Model  | Effort                                 |
| :------------ | :----- | :------------------------------------- |
| `implementer` | sonnet | high                                   |
| `reviewer`    | opus   | xhigh                                  |
| `scout`       | haiku  | _(none: Haiku 4.5 rejects this field)_ |
| `critic`      | opus   | xhigh                                  |

To change a pin, edit `agents/<name>.md` in the dotclaude repo and push. That changes it
everywhere, so it is a plugin decision rather than a per-repo one. For one session only, the
user runs `/effort`.

`reviewer` is dispatched three times per review, once per axis, so its pin costs triple what
the table suggests. `implementer` is the pin that moves the bill on parallel work.

## 4. The lead model

The orchestrating session's model is **not** something this plugin can set. Mention it only if
the user asks.

- Change it for now with `/model`.
- Change it for this project by setting `"model"` in `.claude/settings.json`.
- `CLAUDE_CODE_SUBAGENT_MODEL` sets a default for subagents that do not pin their own; the
  four here all do.

Offer to write the `.claude/settings.json` entry. Do not write it unasked.

## 5. Write the file

```markdown
# oliverjwroberts-dotclaude config

Context: CONTEXT.md
ADRs: docs/adr/
Specs: docs/specs/
Tickets: docs/tickets/
Scratch: .scratch/
Docs: docs/
Tracker: local
```

Omit any line the user did not change from the defaults. A config file that only restates the
defaults is a file that goes stale silently.

The file is committed, so it is visible to everyone working in the repo. Where `docs/` is the
build root for a published documentation site, say so: `docs/agents/` will appear in the
output unless the site config excludes it, and that is worth a one-line exclusion rather than
a surprise on the next deploy.

Then tell them what you wrote and where.
