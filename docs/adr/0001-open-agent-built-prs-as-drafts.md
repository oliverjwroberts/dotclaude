# 0001. Open agent-built pull requests as drafts

Status: Accepted
Date: 2026-10-08

## Context

`implement` now ends every run that started from a spec or a ticket by calling `raise-pr`,
which pushes the branch and opens a pull request. These runs often happen unattended, so
nobody has read the diff when the PR appears. pstack's `opening-a-pr` playbook takes the
opposite line: "Open every PR ready, never as a draft."

## Decision

`raise-pr` opens every pull request with `gh pr create --draft`. The user marks it ready
after reading it.

## Alternatives

- **Open it ready, as pstack does.** Rejected because a ready PR notifies CODEOWNERS and can
  start auto-merge rules before anyone has read the change. pstack's playbook assumes someone
  is in the session watching. These runs often have nobody watching.
- **Stop at the text and let the user open the PR**, as `draft-pr` did. Rejected because the
  user wants to come back to a PR ready for review. A block of text in a finished session
  does not meet that.

## Consequences

- The user wakes up to a PR that nobody else has been notified about.
- Every PR needs one manual step, marking it ready, before review requests go out.
- Repos whose CI runs only on ready PRs give no CI signal until that step. The Evidence
  section has to carry the local checks on its own.
