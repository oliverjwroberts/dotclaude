---
name: unslop
description: Cut AI tells from prose so it reads like a person wrote it. Use when writing or editing any prose surface - a README, doc, ADR, PR description, commit message, report, or handoff - and when the user says the writing sounds like AI, is too wordy, or asks to tighten it.
---

Rewrite to remove the patterns below. Preserve meaning, technical accuracy, and every
concrete detail. Shorter is usually better, but never at the cost of a fact.

## Content

- **Puffery.** "Pivotal", "seamless", "robust", "powerful", "game-changing", "cutting-edge".
  Delete, or replace with the concrete property you meant.
- **Hollow openers.** Sentences that open with "-ing" and say nothing: "Leveraging X to
  achieve Y". Lead with the subject and a real verb.
- **Vague attribution.** "Experts say", "it is widely considered", "studies show". Cite the
  source or drop the claim.
- **The challenge narrative.** "While X presents challenges, careful Y ensures Z." Say what
  is hard and what you did about it.
- **Weak framing verbs.** "Serves as", "acts as", "functions as", "plays a role in". Use the
  verb: "X is the router", "X routes requests".

## Structure

- **"Not just X, but Y."** Also "It's not about X, it's about Y." State Y.
- **Forced rules of three.** Three items because three sounds good. Use the number of items
  there are.
- **Synonym cycling.** Rotating "the system", "the platform", "the solution" for one thing.
  Pick one term and repeat it. Consistency beats variety in technical prose.
- **Generic conclusions.** "In conclusion", "Overall, X represents a significant step." End
  on the last real point.

## Style

- **Em-dash overuse** and mid-sentence colons used as dramatic pauses. One per paragraph at
  most; a full stop usually works better.
- **Bold spam.** Bolding a phrase in most sentences. Bold a label or a term of art, not
  emphasis you felt.
- **Decorative emoji** and title-case headings.
- **Curly quotes** in anything that might be pasted into a terminal or a code block.

## Voice

- **Chatbot pleasantries.** "Great question", "Certainly", "I hope this helps", "Let me know
  if you'd like me to". Delete.
- **Sycophancy.** "You're absolutely right", "Excellent point". Delete.
- **Hedging chains.** "It might potentially be somewhat possible that". Commit, or say
  plainly that you do not know.
- **Filler.** "In order to" is "to". "It is important to note that" is nothing. "Due to the
  fact that" is "because". "At this point in time" is "now".

## Plain speech

- Prefer the mechanism to the feeling: "the cache is invalidated on write", not "this
  ensures data stays fresh and reliable".
- Prefer active voice. Name the actor.
- Prefer a strong verb to a verb propped up by an adverb.
- Prefer a concrete noun to an abstract metaphor. "Landscape", "interplay", "tapestry",
  "testament" are almost always hiding something specific.
- Vary sentence length. Uniform medium-length sentences are the strongest AI tell there is.

## Where these rules yield

This is prose guidance, not a house style for everything.

- Terminal output is scanned, not read. A bold label or a table is helping the reader there.
- Quoted material, log lines, error text, and code stay exactly as they are.
- A user's own voice in a document you are editing is theirs. Cut the tells; do not
  overwrite their register with yours.
