# Working in this repo

This repo is a Claude Code plugin marketplace containing one plugin,
`oliverjwroberts-dotclaude`. It ships skills (`skills/`) and subagents (`agents/`).
Everything here is markdown; there is no build step and no test suite.

When you create or edit a `SKILL.md` or a file under `agents/`, call the Skill tool with
`oliverjwroberts-dotclaude:authoring-skill` and follow it. That skill owns the writing rules:
body length, descriptions, the invocation axis, how one skill calls another, and how to write
`CLAUDE.md` itself. They are not repeated here.

## What everything in here has to hold to

- **Zero setup.** No skill may hard-require `docs/agents/dotclaude.md`.
  Read it if present, fall back to the documented defaults if not.
- **Stack-agnostic.** Never hardcode `pytest`, `go test`, `pnpm`, or any other toolchain.
  Discover the repo's commands from its own files.
- **The skill is canonical.** Discipline lives in a `SKILL.md`. An agent file holds only what
  a skill cannot set: identity, tool grant, model pin, and report format. Two copies of a rule
  drift, and the skill is the one that gets maintained.
- **Reuse `/run` and `/verify`.** Claude Code ships both. `/run` is model-invoked, so a skill
  calls it through the Skill tool; `/verify` is user-invoked, so a skill tells the human to run
  it. Reviewing is deliberately not reused: `review-code` owns the whole review path.

## Conventions

- Artifact defaults, all overridable in `setup`: `CONTEXT.md` at the repo root, ADRs in
  `docs/adr/`, specs in `docs/specs/`, tickets in `docs/tickets/`, handoffs in
  `.scratch/handoffs/`, subagent reports in `.scratch/`.
- `.scratch/` is transient and gitignored. `docs/` is durable and committed. A ticket is
  committed because an agent on another machine has to read it; a handoff is not because it
  dies when it is picked up.
- Subagents live in `agents/` and pin their own `model` and `effort`. Leave `effort` off
  `scout`: Haiku 4.5 errors on it, and Claude Code then warns and retries without it, so the
  field costs a round-trip and buys nothing.
- After editing anything under `agents/`, run `/reload-plugins`. `SKILL.md` edits are picked
  up live.
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org):
  `type(scope): subject`, imperative mood, under 72 characters.
