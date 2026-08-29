---
name: diagnose
description: Find the root cause of something broken, failing, flaky, or slow, and fix the cause rather than the symptom. Use when the user reports a bug, a crash, a failing test, a regression, or a performance problem, or asks why something is not working.
---

Find the cause before changing anything. A fix applied to a symptom you did not understand is
a second bug wearing the first one's clothes.

## The loop

1. **Get a red feedback loop first.** Find or write the fastest check that fails _because of
   this bug_: a failing test, a script, a curl, one command. Nothing below is reliable until
   this exists, so do not skip it, and do not proceed on a check that fails for a different
   reason.

2. **Minimise.** Cut the reproduction down until every remaining part is load-bearing. The
   smallest reproduction usually names the cause.

3. **Hypothesise before instrumenting.** Write down what you think is happening and what you
   expect to observe if you are right. A hypothesis you can disconfirm beats scattering print
   statements.

4. **Instrument and observe.** Add logging, run under a debugger, check the actual values.
   Confirm or kill the hypothesis. If it is dead, go back to step 3. Two dead hypotheses in a
   row means widen the search. Dispatch `scout` over the call path.

5. **Fix the cause, not the symptom.** Adding a guard, a retry, or a special case means saying
   explicitly why the underlying cause cannot be fixed instead.

6. **Prove it.** The check from step 1 goes green. Run the surrounding tests to confirm you
   broke nothing else.

7. **Leave a regression test.** Something that would have caught this. If the bug was not
   testable, say so and say what would make it testable.

For a performance regression the same loop holds with one change. Step 1 is a measurement
rather than a pass or fail, and step 6 compares against the baseline you recorded before
touching anything.

## Reporting

- **Separate what you verified from what you inferred.** An inferred cause presented as
  confirmed sends the next person down the wrong path.
- **Say when you have not found it.** "The symptom goes away when X, and I do not know why" is
  a useful report. A confident wrong cause is not.
- **Name what you ruled out**, and how. That is what stops the next person repeating the search.

## Where it goes next

- A fix bigger than the diagnosis, or one that touches several places: call the Skill tool
  with "oliverjwroberts-dotclaude:implement".
- A root cause that forces a design choice rather than a repair: call the Skill tool with
  "oliverjwroberts-dotclaude:record-decision".
- Otherwise fix it here, then call the Skill tool with
  "oliverjwroberts-dotclaude:review-code".

To record the fix in git, call the Skill tool with
"oliverjwroberts-dotclaude:commit". The body says what was actually wrong, not what you
changed.
