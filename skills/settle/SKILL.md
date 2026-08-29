---
name: settle
description: Interview the user relentlessly about a plan, decision, problem, or idea until every branch is resolved. Use when the user says "let's settle this", "settle the plan", or "grill me", wants to think something through or stress-test an approach, asks what they are missing, or brings a problem that is not yet specified well enough to act on.
argument-hint: "[the subject, or a spec to resume]"
---

Interview the user until you reach a shared understanding. Do not start work until they
confirm you have it.

Map the subject as a **design tree**. Every decision branches into the decisions that hang
off it. The **frontier** is every decision whose prerequisites are already settled, the
questions you can ask now without guessing at answers you have not heard yet.

## Resume an earlier session

When the argument is a path, a URL, or an issue number, it names a spec to continue rather
than a subject to settle from scratch. Resume from it and skip the search below.

Otherwise a subject may still have a spec from a session that ran out of room, so look for one
before you compute the first frontier. Read `docs/agents/dotclaude.md` for the Specs location
and the tracker; the default is `docs/specs/`. When `Tracker: github`, run
`gh issue list --label spec --state open --json number,title` and pick by title. Read the
candidate titles and judge them. Do not guess at search terms.

Where a spec covers this subject, its `Open questions` section is the frontier. Start there.
Treat `Approach` and `Scope` as settled and do not re-ask what they already answer. Say which
spec you resumed from, so the user can correct you if it is the wrong one.

## The loop

Work in rounds. Each round:

1. Compute the frontier.
2. Ask the whole frontier at once. Number each question, and give your recommended answer to
   each one.
3. Wait. Do not answer for the user, and do not start work.
4. Their answers settle those decisions and push the frontier outward. Go again.

Format a round like this:

```
**Q1 - <short title>**: <the question, with the options if there are discrete ones>

Recommend: <your answer, and the one-line reason>

---

**Q2 - <short title>**: <question>

Recommend: <answer and reason>
```

A question whose answer depends on another question still open in this round belongs to a
later round, not this one.

## The division of labour

**Facts are your job. Decisions are the user's.**

When a frontier question needs a fact from the environment, the filesystem, the codebase, a
dependency's behaviour, what an API actually returns, go and find it. Do not ask the user
something you could look up. For anything more than a quick grep, dispatch the `scout`
subagent so the search does not fill this conversation.

Do not block on it. A running lookup is an unsettled prerequisite, so only the questions
downstream of it wait. Ask the rest of the frontier now.

Read `CONTEXT.md` if the repo has one, and use the vocabulary already in it. Inventing a
second name for a concept that already has one is how a design tree ends up with two
branches that are the same branch.

## Asking well

- Give a recommendation on every question. "What do you think?" makes the user do your work.
  A wrong recommendation is still useful, because it is faster to correct than to originate.
- Prefer a concrete choice to an open prompt. "A or B, and here is the tradeoff" beats "how
  should we handle X?"
- Ask about the thing that would be most expensive to get wrong, first.
- Name what you are assuming when you skip a question. A silent assumption is the failure
  this whole skill exists to prevent.
- Keep rounds small enough to answer in one sitting. If a round runs past six or seven
  questions, the scope is probably too big; say so.

## Finishing

When the frontier is nearly empty and the shape of the answer is settled, send it to the
`critic` subagent for one adversarial pass. That is the moment a fresh attack is worth most,
and it costs nothing when there is nothing left to attack. Relay what came back; the critic
proposes no fixes, so the choice of what to do about each objection is the user's.

The session is done when the frontier is empty. Every branch has been visited and nothing
silently assumed. Summarise what was decided.

Where a decision was genuinely contested, call the Skill tool with
"oliverjwroberts-dotclaude:record-decision" before the alternative is forgotten. This is the
only moment the losing option is still cheap to write down.

Then ask whether to proceed.

## Stop without losing the frontier

Sometimes the session ends with the frontier still open, because the context is filling or
the user stops. The design tree lives only in this conversation, so it dies with the session
unless you write it down.

Before you stop, call the Skill tool with "oliverjwroberts-dotclaude:write-spec" and give it
what the session settled. Put every unresolved frontier question in `Open questions`, one
line each, each with the recommendation you would have made. A question recorded without its
recommendation costs the next session a round of work to re-derive.

Tell the user where the spec landed and which round they stopped on.
