# The default PR layout

Use this when the repo has no PR template. Drop any section with nothing in it rather than
padding it.

````markdown
## Summary

<one or two sentences, then the smallest visual that makes the change clear>

## Evidence

- **Before:** <failing test, output, or screenshot>
  **After:** <passing test, output, or screenshot>

## Merge danger

**Door:** <one-way or two-way>. <what that rests on>

**Blast radius:** <one word>. <who or what the change touches, and what that rests on>

## Not in this PR

- <blocked ticket, filed ticket, or deliberate omission, each with its link>

<Closes #N, Refs #N, or links to the spec and tickets>
````

The title follows the repo's commit convention: imperative, under 72 characters, saying what
changes.

## Summary

Skip the preamble. Pick the one view that makes the key point clear, and place it next to the
sentence it supports. Keep only the calls, files, and boundaries the reviewer needs.

| The change is mostly | Show it as |
| :-- | :-- |
| Logic or an algorithm | Pseudocode |
| Runtime control flow | A call tree |
| UI structure | A component tree, with the state and module boundaries that matter |
| File responsibility or a broad refactor | A shallow file tree with a comment per entry |
| Interaction between components | A Mermaid sequence diagram |
| A change to a shape that already exists | A `diff` block of that shape: the tree, the call stack, or the control flow |

A diff-sketch of a call tree looks like this:

```diff
 submitForm
   createSession
     persistPrompt
+    expandSkillMention
     launchAgent
```

Show the whole block only when most of it is new or when the reviewer needs the exact target
shape. One visual is usual and two is plenty. Use the vocabulary in `CONTEXT.md` if the repo
has one.

## Evidence

Show that the change works, as a before and an after.

- A screenshot is the strongest evidence for a visual change, when the environment can take
  one.
- Otherwise use execution: the test that failed and now passes, named or sketched in
  pseudocode, or the command and its output.
- Say plainly what you did not run. Where `/verify` was not run, say so. Never claim a check
  you did not run.

## Merge danger

A two-way door is cheap to walk back: revert the PR and nothing is lost. A one-way door is a
destructive action or a decision that is hard to reverse, such as a data migration, a
deleted public API, or a sent message.

The blast radius is everything the change can break: consumers of an API, layout, mobile
views, other services, data. Consider them all, then name the one that matters.

Each claim says what it rests on. In order of strength:

1. You said so. This is worthless on its own.
2. You pointed at the line that makes it true.
3. You showed that the bad case cannot happen.
4. You ran it.

Name the one or two facts the claim depends on, and prove those. A blast-radius claim that
only sounds right is worthless.

## Not in this PR

List what a reviewer might otherwise report as a miss:

- Tickets that ended `BLOCKED`, with the reason.
- Review findings too large to fix in this PR, linked to the ticket that now holds each one.
- Anything found and deliberately left alone.
