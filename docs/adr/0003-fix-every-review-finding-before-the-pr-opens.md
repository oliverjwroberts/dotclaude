# 0003. Fix every review finding before the PR opens

Status: Accepted
Date: 2026-10-08

## Context

At the end of a run, `implement` calls `review-code` on the combined change. The question is
what happens to the findings when the user may not be in the session. pstack's `interrogate`
says "Do NOT auto-apply changes." The first plan in this session had the user approve
findings before any fix. In practice the user asked for every finding to be fixed in nearly
every run, and filed the occasional large one as a ticket.

## Decision

One implementer fixes every finding from `review-code` before `raise-pr` runs. A finding too
large for that pass becomes a ticket. The PR body links that ticket under "Not in this PR".

## Alternatives

- **Show the findings and fix only the approved ones.** Rejected because it stalls an
  unattended run before the PR opens, and the user approved all of them nearly every time.
- **Open the PR with the findings listed and fix nothing**, the `interrogate` stance.
  Rejected because it leaves the user a round of fixes that they would have approved anyway.

## Consequences

- An unattended run reaches a draft PR without waiting on the user.
- False positives get fixed along with real findings. The user reviews the fix commit in the
  PR rather than the finding.
- Standards findings the user would have rejected land in the diff and need reverting by
  hand.
- "Too large" is the implementer's judgment. A wrong call leaves a big change in the PR, or
  files a ticket for something that fit.
