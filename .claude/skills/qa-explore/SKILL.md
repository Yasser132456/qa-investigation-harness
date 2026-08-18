---
name: qa-explore
description: Stage 1 of the QA investigation harness — walk a flow via Playwright MCP and record observations only. Invoke manually, do not trigger automatically.
argument-hint: <ticket-id> <test case description>
arguments: [ticketId, testcase]
disable-model-invocation: true
---

Read qa-harness/RULES.md and qa-harness/stages/01-explore.md, then follow the
explore stage exactly.

Ticket: $ticketId
Test case: $testcase
