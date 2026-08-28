---
name: technical-writing
description: Standard for technical documents - pick a Diataxis mode, then write for a tired reader to understand on the first pass. Use when writing or editing a README, doc, ADR, RFC, spec, task brief, PR description, or commit message.
---

Write so a tired engineer gets it on the first read. Three rules override everything below:
delete words that do no work, prefer the everyday word, and if a rule makes the prose worse,
fix the sentence another way.

## 1. Pick the mode

A document that mixes modes serves none of them. Pick one, and split the document if you
find yourself needing two.

| Mode        | Job                   | Reader is                     |
| :---------- | :-------------------- | :---------------------------- |
| Tutorial    | Learning by doing     | Following along, new          |
| How-to      | Getting one task done | Working, in a hurry           |
| Reference   | Looking a fact up     | Working, knows what they want |
| Explanation | Understanding why     | Reading, building a model     |

Do not explain in a how-to. Do not instruct in a reference. Send the reader to the other
document instead.

Commit messages, PR descriptions, and task briefs skip mode selection. Everything below
still applies to them.

## 2. Address the reader

- "You", present tense, active voice. "The compiler checks", not "is checked".
- Command first, then the detail: "Run `x`, which does y."
- Condition before instruction: "If the build fails, run `x`." Not the reverse; the reader
  should know whether the sentence applies before they read the action.
- Common case first, exceptions after.
- Say what a thing does, not that it is easy. "Simply", "just", "quickly", and "easy" tell a
  stuck reader they are stupid.

## 3. Keep the sentence load down

- Around 20 words for an instruction, 25 for anything else. Split at the conjunction.
- One idea per sentence, one topic per paragraph.
- Keep the articles. "Set flag value" has three readings; "set the value of the flag" has
  one.
- Put "only", "always", and "first" next to the word they modify.
- Break noun stacks with a verb or a preposition. "Request validation error handler" becomes
  "the handler for errors raised while validating a request".

## 4. Remove the ambiguity

- **Ambiguous pronouns.** "It", "this", "they", "that" opening a sentence. Name the noun.
- **One term per concept.** Do not alternate between "job", "task", and "run" for the same
  thing. Pick one, define it once, repeat it.
- **No idioms, no figurative language.** They fail for non-native readers and for search.
- **No slashes** as a substitute for a decision: "and/or", "user/admin". Say which.
- **No Latin.** "e.g." is "for example", "i.e." is "that is", "etc." is usually a list you
  did not finish.
- **Real symbols only.** Every path, command, flag, and function name must be accurate at
  the time you write it. Go and check. A plausible invented path is worse than no example.

## 5. Structure

- Lead with the answer. The reader may stop after one paragraph, so put the conclusion
  there, not at the end.
- A multi-way branch goes in a table or a list, never in a paragraph. The reader is scanning
  for their row.
- Headings are noun phrases that say what is under them, sentence case.
- Every code block is runnable as written, or clearly marked as a fragment.

## 6. Documents that report on work

These rules hold for anything that describes work in progress or work to be done: a spec, a
handoff, a task brief, a review, an investigation note.

- **Reference, do not restate.** Specs, plans, ADRs, issues, commits, and diffs get cited by
  path or URL. A restated copy goes stale the moment the original changes, and the reader
  cannot tell which one is true.
- **Distinguish verified from assumed.** Mark every claim as one or the other. An inferred
  claim presented as fact is the main way a document like this does damage.
- **Record what was tried and failed.** A dead end nobody wrote down gets rediscovered at
  full price.
- **Redact secrets.** API keys, tokens, passwords, personal data. Never in a file.

## Commit messages and PR descriptions

- Subject line: imperative mood, under 72 characters, says what changes. "Add retry to the
  upload path", not "Added retries" or "Fixes".
- Body: why this change, not what the diff already shows. Name the behaviour the reader
  would otherwise have to reverse-engineer.
- A PR description also states how it was verified and what is deliberately not in scope.
