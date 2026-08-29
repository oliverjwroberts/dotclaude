# The three review axes

One axis per agent brief. Paste the relevant section verbatim into the brief, along with the
diff range and any spec path.

## Correctness

Does this code do what it appears to be trying to do, under inputs nobody tested?

- **Logic errors.** Off-by-one, inverted condition, wrong operator, a branch that cannot be
  reached.
- **Edge cases.** Empty input, a single element, null or absent, zero, negative, the maximum.
- **Error handling.** A failure swallowed, an error path that leaves state half-written, a
  retry that repeats a non-idempotent effect.
- **Concurrency.** Shared mutable state, a check followed by an act that is not atomic, an
  await that drops a lock, ordering assumed between independent operations.
- **Resource handling.** Something opened and not closed, an unbounded buffer, a query in a
  loop.
- **Data integrity.** An unvalidated boundary, a type coerced silently, a rounding or timezone
  assumption.

For each finding, give the concrete input or state that makes it bite. A finding with no
failure scenario is a guess; mark it as one or drop it.

## Standards

Does this follow what this repo documents, plus the baseline below?

Find what the repo documents: `CLAUDE.md`, `AGENTS.md`, `CONTEXT.md`, `CONTRIBUTING.md`,
`CODING_STANDARDS.md`, linter and formatter config. A documented repo standard always beats
the baseline. Skip anything tooling already enforces; a linter reports it better than you can.

A diff that introduces a competing term for a concept `CONTEXT.md` already names is a
standards finding. Vocabulary drift is the cheapest thing to catch here and the most expensive
to catch later.

On top of that, apply this baseline, which holds even in a repo that documents nothing. Each
is a labelled judgement call, never a hard violation.

- **Mysterious name.** A name that does not reveal what it does or holds. Rename it. If no
  honest name comes, the design is murky.
- **Duplicated code.** The same logic shape in more than one place in the diff. Extract it.
- **Feature envy.** A function reaching into another object's data more than its own. Move it
  to the data.
- **Data clumps.** The same few values always travelling together. Give them a type.
- **Primitive obsession.** A string or int standing in for a domain concept. Give the concept
  a type.
- **Repeated switches.** The same branch on the same type recurring. Replace with one map or
  polymorphism.
- **Shotgun surgery.** One logical change forcing scattered edits. Gather what changes
  together.
- **Divergent change.** One module edited for several unrelated reasons. Split it.
- **Speculative generality.** Abstraction or parameters for needs nothing has. Delete it.
- **Message chains.** Long `a.b().c().d()` navigation. Hide the walk behind one method.
- **Middle man.** A layer that mostly delegates onward. Cut it out.

Mark each finding as a hard violation of a documented standard or a judgement call.

## Spec

Does the diff do what was actually asked for?

Read the spec or ticket you were given. Report three things, quoting the line for each:

- Requirements missing, or only partly implemented.
- Behaviour in the diff nobody asked for.
- Requirements that look implemented but where the implementation looks wrong.

Check the spec's own Verification section. Was it run, and does the diff make it pass?

If no spec was supplied, report that and stop. Do not reconstruct one from the diff; a spec
inferred from the code always agrees with the code.
