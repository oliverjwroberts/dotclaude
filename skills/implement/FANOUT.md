# Dispatching several tickets

Read this when more than one ticket runs in a batch.

## Frame it

Write down, and show the user:

- **Done condition.** What has to be true for the batch to be worth running.
- **The graph.** Which tickets are blocked by which. Everything unblocked runs in parallel;
  everything else waits for its blocker to report.
- **N.** Only as many parallel workers as the graph allows. Beyond about six, aggregation
  quality drops faster than throughput improves.
- **Agent and model per ticket.** State them rather than inheriting. `implementer` for
  building, `scout` for pure investigation, `critic` for adversarial design work.
- **Where each worker writes its report.** A file per worker under
  `.scratch/implement/<run>/`, or under the scratch directory named in
  `docs/agents/dotclaude.md` if that file exists, so no worker's raw output lands in
  this conversation.

Show the frame and wait for approval. This is the gate, and it is not optional.

## Dispatch

Send each ticket verbatim; it is already self-contained. Launch parallel tickets in a single
message so they actually run concurrently. Track them with TodoWrite.

Do not start a sequenced ticket before its blocker has reported. If a blocker fails, the
tickets behind it do not run. Say so rather than dispatching them into a broken state.

## Collapse the reports

Read each worker's file. Do not paste them into the conversation.

Normalise every worker to one status:

| Status    | Meaning                                   |
| :-------- | :---------------------------------------- |
| `DONE`    | Built and verified.                       |
| `ISSUES`  | Built, but something needs attention.     |
| `BLOCKED` | Could not complete. The work is not done. |

Then report:

```markdown
## Result

<Two or three sentences answering the done condition. This is the part that gets read.>

## Tickets

| Ticket | Status | Headline |
| :----- | :----- | :------- |
| 001    | DONE   | ...      |
| 002    | ISSUES | ...      |

## Out of scope

<Everything the workers found and deliberately left alone, worst first. Each with the ticket
that found it.>

## Gaps

<Anything BLOCKED, and any spec requirement no ticket covered. Omit the heading if there are
none.>
```

One line per row. A headline is a claim, not a summary of what the worker did.

## Fidelity

- **A dropout is a finding.** A worker that fails, times out, or returns nothing gets its row
  and names the gap. Never silently report N-1 as N.
- **Confidence carries through unchanged.** Where a worker said "possibly", the report says
  "possibly".
- **A finding you do not understand** goes in as the worker stated it, marked unverified.

Before writing the final report, call the Skill tool with
"oliverjwroberts-dotclaude:unslop".
