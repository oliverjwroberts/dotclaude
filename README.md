# dotclaude

My Claude Code skills and subagents, packaged as a plugin. The repo is public so I can install
it from any machine. It is built for me, but you are welcome to it.

Each skill does one job. Settle a decision, map a domain, write a spec, split it, build it,
review it. You chain them yourself.

## What you reach for, when

### Building a feature

```
/settle  →  /write-spec  →  /split-tickets  →  /implement  →  /review-code  →  /commit  →  /draft-pr
```

`/settle` interviews you in rounds until every branch of the decision is resolved. It is
optional, and it is the step I skip when I am in a hurry. That is the wrong instinct. Skipping
it means the spec records an idea that was never stress-tested, so use it whenever the request
has more than one reasonable reading.

`/write-spec` turns the conversation into a spec without interviewing you again.
`/split-tickets` cuts that spec into bounded tickets and the dependency graph between them.
`/implement` builds one ticket, a batch, or the whole spec, and dispatches subagents when the
work is wide. `/review-code` runs three parallel axes over the diff and reports them side by
side. `/commit` discovers the repo's commit convention and splits the work into messages.
`/draft-pr` writes the PR title and body.

For a small change, collapse the middle: `/settle` then `/implement`. `/write-spec` and
`/split-tickets` earn their place once the work outlives one session or gets handed to someone
else.

### Fixing a bug

```
/diagnose  →  /implement (or a direct fix)  →  /review-code  →  /commit  →  /draft-pr
```

`/diagnose` gets to a red feedback loop first, then the root cause, then the smallest fix at
the cause. It stops at a proven cause. If the fix is bigger than the diagnosis or touches
several places, hand it to `/implement`; otherwise fix it in place and review.

`/commit` stops at the commit in both chains, and `/draft-pr` stops at the text. Pushing and
opening the pull request stay yours.

### Landing in an unfamiliar repo

```
/map-domain  →  CONTEXT.md
```

`/map-domain` harvests the vocabulary actually in the code, finds the collisions, and writes
`CONTEXT.md`. Run it when you land in a repo whose terms you do not trust yet. Where a naming
collision turns out to be a real decision rather than an accident, `/record-decision` catches
it.

### Recording a decision

```
/settle  →  /record-decision
```

`/record-decision` writes one ADR for one contested decision, including the alternative that
lost. `/settle` calls it itself when a branch was genuinely contested. That moment, right as
the frontier empties, is the only time the losing option is still cheap to write down.

### Running out of context

```
/write-handoff
```

Compacts the session into a document a fresh agent can pick up. Any time. It is user-invoked on
purpose, because only you know when the session is nearly spent.

## Skills

Skills are namespaced `/oliverjwroberts-dotclaude:<name>`, but the bare name works too unless
something else has claimed it, so in practice you type `/settle`.

### The blocks

Model-invoked. You can type them, another skill can call them, or the model reaches for one
when a task fits.

| Skill              | Does                                          |
| :----------------- | :-------------------------------------------- |
| `/settle`          | Interviews you until the decision is resolved |
| `/map-domain`      | Writes `CONTEXT.md` from the code's own terms |
| `/record-decision` | One ADR for one contested decision            |
| `/write-spec`      | Synthesises a spec from this conversation     |
| `/split-tickets`   | Cuts a spec into bounded tickets              |
| `/implement`       | Builds a ticket, a batch, or a whole spec     |
| `/diagnose`        | Root cause, then the smallest fix at it       |
| `/review-code`     | Three parallel axes over a diff               |
| `/commit`          | Splits the work and writes the messages       |
| `/draft-pr`        | The PR title and body, and nothing past it    |

### The writing standards

Most of the blocks above call these; you rarely invoke them yourself. `/authoring-skill` is not
invoked at all. It carries `paths:` frontmatter, so it fires on its own whenever a `SKILL.md`
is edited.

| Skill                | Does                                             |
| :------------------- | :----------------------------------------------- |
| `/technical-writing` | Diátaxis modes plus sentence-level rules         |
| `/unslop`            | Cuts AI tells from prose                         |
| `/authoring-skill`   | House rules for skills, agents, and `CLAUDE.md`  |

### User-invoked

The model never fires these; you type them.

| Skill            | Does                                          |
| :--------------- | :-------------------------------------------- |
| `/write-handoff` | Compacts the session for a fresh agent        |
| `/setup`         | Sets a repo's artifact locations and tracker  |

Anything user-invoked cannot be called by another skill, which is why the list is short.

## Subagents

Addressable directly as `@agent-<name>`, or dispatched by the skills.

| Agent         | Model / effort | Role                                                                        |
| :------------ | :------------- | :-------------------------------------------------------------------------- |
| `implementer` | sonnet / high  | Builds one ticket. Reports scope it deliberately left alone.                |
| `reviewer`    | opus / xhigh   | Reviews one axis of a diff. Dispatched three times per review. Read-only.   |
| `scout`       | haiku          | Read-only locator. Returns `file:line` and a conclusion, never a file dump. |
| `critic`      | opus / xhigh   | Attacks a plan for assumptions and failure modes. Proposes no fixes.        |

The discipline lives in the skills, not in these files. An agent definition holds only what a
skill cannot set: identity, tool grant, model and effort pins, and report format.

These files are the only source of truth for model and effort. No skill and no config file
overrides them. Change a pin by editing the agent here, or set effort for one session with
`/effort`. The orchestrating session's model is not set by this plugin at all. Switch it with
`/model`.

`reviewer` is dispatched once per axis, so a single `/review-code` run costs three Opus
reviews, not one. Cost is per completed task rather than per request, which is why
`implementer` is pinned to sonnet at high rather than something cheaper. A worker that needs
three attempts is not cheap.

`scout` carries no `effort` field on purpose. Haiku 4.5 errors on it, and Claude Code catches
the rejection, warns, and retries without it. Nothing crashes; every dispatch just pays for a
round-trip it did not need.

## Where things land

Defaults, all overridable with `/setup`:

| Setting | Default              |
| :------ | :------------------- |
| Context | `CONTEXT.md` at root |
| ADRs    | `docs/adr/`          |
| Specs   | `docs/specs/`        |
| Tickets | `docs/tickets/`      |
| Scratch | `.scratch/`          |
| Docs    | `docs/`              |

Handoffs land in `<scratch>/handoffs/`, and subagent reports in `<scratch>/` itself.

`docs/` is committed, `.scratch/` is transient and gitignored. A ticket is committed because an
agent on another machine has to read it. A handoff is not, because it dies the moment it is
picked up.

Skills are stack-agnostic. They discover build and test commands from the repo rather than
assuming a toolchain.

## Install

```
/plugin marketplace add oliverjwroberts/dotclaude
/plugin install oliverjwroberts-dotclaude@oliverjwroberts-dotclaude
```

To load it in every session, in `~/.claude/settings.json`:

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

Working on the plugin itself is in `CLAUDE.md`. Be aware that installing from a local checkout
replaces this registration rather than sitting alongside it.

## Inspired by

[mattpocock/skills](https://github.com/mattpocock/skills),
[pstack](https://github.com/cursor/plugins/tree/main/pstack), and
[ai-protocol](https://github.com/dnlbox/ai-protocol). Nothing here depends on those being
installed.
