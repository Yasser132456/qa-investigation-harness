---
name: qa-challenge
description: Stage 3 of the QA investigation harness — produce a risk table (Part A), then after human approval a test plan (Part B). Invoke manually, do not trigger automatically.
argument-hint: <ticket-id> [run Part B]
arguments: [ticketId, part]
disable-model-invocation: true
---

Read qa-harness/RULES.md and qa-harness/stages/03-challenge.md, then follow
the challenge stage exactly.

Ticket: $ticketId

If the argument "run Part B" was given below, run Part B — after first
checking risks.md actually has APPROVED rows, per the stage file. Otherwise
run Part A only, and stop for human review as the stage file instructs.

$ARGUMENTS
