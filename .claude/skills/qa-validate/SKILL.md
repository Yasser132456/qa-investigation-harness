---
name: qa-validate
description: Stage 5 of the QA investigation harness — audit generated tests against approved risks, then mutation-check assertions on request. Invoke manually, do not trigger automatically.
argument-hint: <ticket-id> [run Part B]
arguments: [ticketId, part]
disable-model-invocation: true
---

Read qa-harness/RULES.md and qa-harness/stages/05-validate.md, then follow
the validate stage exactly.

Ticket: $ticketId

If the argument "run Part B" was given below, run Part B (mutation check).
Otherwise run Part A only (oracle conformance).

$ARGUMENTS
