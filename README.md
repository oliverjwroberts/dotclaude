# dotclaude

My Claude Code skills and subagents, packaged as a private plugin.

These are building blocks, not a workflow. Each one does a single job well: settling a
decision, mapping a domain, writing a spec, splitting it, building it, reviewing it. You chain
them yourself, and the chains are documented below.

Inspired by [mattpocock/skills](https://github.com/mattpocock/skills),
[pstack](https://github.com/cursor/plugins/tree/main/pstack), and
[ai-protocol](https://github.com/dnlbox/ai-protocol). Self-contained: nothing here depends on
those being installed.

## Install

```
/plugin marketplace add oliverjwroberts/dotclaude
/plugin install oliverjwroberts-dotclaude@oliverjwroberts-dotclaude
```

The `owner/repo` shorthand clones over SSH, which is what makes a private repo work with no
extra setup. To use HTTPS instead, run `gh auth setup-git` and set
`CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1`.

Background auto-updates disable git credential helpers by default. For unattended refreshes
of this private repo, either run `gh auth setup-git`, or embed a token:

```bash
git config --global \
  url."https://x-access-token:<TOKEN>@github.com/oliverjwroberts/dotclaude".insteadOf \
  "https://github.com/oliverjwroberts/dotclaude"
```

### Load it in every session

In `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "oliverjwroberts-dotclaude": {
      "source": { "source": "github", "repo": "oliverjwroberts/dotclaude" }
    }
  },
  "enabledPlugins": {
    "oliverjwroberts-dotclaude@oliverjwroberts-dotclaude": true
  }
}
```

### Working on the plugin itself

Install from the local checkout so edits are testable before pushing:

```
/plugin marketplace add ./
/plugin install oliverjwroberts-dotclaude@oliverjwroberts-dotclaude
```

`SKILL.md` edits apply live. Changes under `agents/` need `/reload-plugins`, and a new skill
directory is safest with a restart. Check the manifests with `claude plugin validate .`.

## Skills

Skills are namespaced `/oliverjwroberts-dotclaude:<name>`, but the bare name works too unless
something else has claimed it, so in practice you type `/settle`.

### The blocks

Model-invoked: you can type them, another skill can call them, or the model reaches for one
when a task fits.

| Skill              | What it does                                                                                |
| :----------------- | :------------------------------------------------------------------------------------------ |
| `/settle`          | Interviews you in rounds until every branch of the decision is resolved.                    |
| `/map-domain`      | Harvests the vocabulary actually in the code, finds the collisions, writes `CONTEXT.md`.    |
| `/record-decision` | One ADR for one contested decision, including the alternative that lost.                    |
| `/write-spec`      | Turns this conversation into a spec. Does not interview; synthesises what is already known. |
| `/split-tickets`   | Cuts a spec into bounded tickets, with the dependency graph between them.                   |
| `/implement`       | Builds one ticket, a batch, or a whole spec. Dispatches subagents when the work is wide.    |
| `/diagnose`        | Red feedback loop first, then root cause, then the smallest fix at the cause.               |
| `/review-code`     | Three parallel axes over a diff: Correctness, Standards, Spec. Reported side by side.       |
| `/commit`          | Discovers the repo's commit convention, splits the work, writes the messages.               |

### The writing standards

Most of the blocks above call these; you rarely invoke them directly.

| Skill                | What it does                                                                     |
| :------------------- | :------------------------------------------------------------------------------- |
| `/technical-writing` | Diátaxis modes plus sentence-level rules for docs, specs, tickets, PRs, commits. |
| `/unslop`            | Cuts AI tells from prose.                                                        |
| `/authoring-skill`   | House rules for skills, agents, and `CLAUDE.md`. Auto-activates on `SKILL.md`.   |

### User-invoked

The model never fires these; you type them.

| Skill            | What it does                                                                                    |
| :--------------- | :---------------------------------------------------------------------------------------------- |
| `/write-handoff` | Compacts the session into a document a fresh agent can pick up.                                 |
| `/setup`         | Configures a repo's artifact locations, tracker, and model policy. Everything works without it. |

Anything user-invoked cannot be called by another skill, which is why the list is short.

## Chaining them

### Building a feature

```
/settle  →  /write-spec  →  /split-tickets  →  /implement  →  /review-code  →  /commit
```

`/settle` is optional but usual, and it is the step people skip. Skipping it means the spec
records an idea that was never stress-tested. Use it whenever the request has more than one
reasonable reading.

For a small change, collapse the middle: `/settle` then `/implement` directly. `/write-spec`
and `/split-tickets` earn their place once the work outlives one session or gets handed to
someone else.

### Fixing a bug

```
/diagnose  →  /implement (or a direct fix)  →  /review-code  →  /commit
```

`/diagnose` stops at a proven root cause. If the fix is bigger than the diagnosis or touches
several places, it hands off to `/implement`; otherwise fix it in place and review.

`/commit` stops at the commit in both chains. Pushing and opening a PR stay yours.

### Understanding an unfamiliar codebase

```
/map-domain  →  CONTEXT.md
```

Run it when you land in a repo whose terms you do not trust yet. Where a naming collision
turns out to be a real decision rather than an accident, `/record-decision` catches it.

### Recording a decision

```
/settle  →  /record-decision
```

`/settle` calls this itself when a branch was genuinely contested. That moment, right as the
frontier empties, is the only time the losing option is still cheap to write down.

### Running out of context

```
/write-handoff
```

Any time. It is user-invoked on purpose: only you know when the session is nearly spent.

## Subagents

Addressable directly as `@agent-<name>`, or dispatched by the skills.

| Agent         | Model / effort | Role                                                                        |
| :------------ | :------------- | :-------------------------------------------------------------------------- |
| `implementer` | sonnet / high  | Builds one ticket. Reports scope it deliberately left alone.                |
| `reviewer`    | opus / xhigh   | Reviews one axis of a diff. Dispatched three times per review. Read-only.   |
| `scout`       | haiku          | Read-only locator. Returns `file:line` and a conclusion, never a file dump. |
| `critic`      | fable / high   | Attacks a plan for assumptions and failure modes. Proposes no fixes.        |

The discipline lives in the skills, not in these files. An agent definition holds only what a
skill cannot set: identity, tool grant, model pin, and report format.

### Notes on the model pins

The orchestrating session's model is **not** set by this plugin. Switch it with `/model`.

`reviewer` is dispatched once per axis, so a single `/review-code` run costs three Opus
reviews, not one.

`scout` carries no `effort` field on purpose. Haiku 4.5 errors on it, and Claude Code catches
the rejection, warns, and retries without it. Nothing crashes; every dispatch just pays for a
round-trip it did not need.

Two things to keep in mind when tuning: prompt caches are model-scoped, so switching the lead
mid-session throws the cache away; and cost is per completed task, not per request, so a
cheaper worker that needs three attempts is not cheaper.

## Conventions

Defaults, all overridable with `/setup`:

| Artifact   | Default              |
| :--------- | :------------------- |
| Domain map | `CONTEXT.md` at root |
| ADRs       | `docs/adr/`          |
| Specs      | `docs/specs/`        |
| Tickets    | `docs/tickets/`      |
| Handoffs   | `.scratch/handoffs/` |

`docs/` is committed, `.scratch/` is transient and gitignored. A ticket is committed because an
agent on another machine has to read it. A handoff is not, because it dies the moment it is
picked up.

Skills are stack-agnostic: they discover build and test commands from the repo rather than
assuming a toolchain. They reuse the bundled `/run` and `/verify`. Reviewing is deliberately
not reused; `/review-code` owns that path so the Standards and Spec axes exist alongside
Correctness.
