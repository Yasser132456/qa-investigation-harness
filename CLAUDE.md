# QA Investigation Harness

This repo has a risk-driven QA investigation harness. Full rules live in
[qa-harness/RULES.md](qa-harness/RULES.md) — read it before running any stage
below, and follow it exactly.

Never write Playwright test code yourself. Test generation belongs to this
project's test-generation agent (see qa-harness/RULES.md for how to get one
if it doesn't exist yet) — this harness only investigates, produces a
human-reviewed test plan, and audits the result.

## Stages

Invoke these as skills, in order:

- `/qa-explore` — walk a flow, record observations (Stage 1)
- `/qa-model` — build a state/transition model (Stage 2)
- `/qa-challenge` — risk table, then (after human approval) a test plan (Stage 3)
- `/qa-validate` — audit generated tests against approved risks (Stage 5)

Stage 4 — actually generating tests — is not a skill here. Point your
project's test-generation agent at the `specs/<ticket>.plan.md` file Stage 3
produces, the same way you'd hand it any other plan.

See [qa-harness/README.md](qa-harness/README.md) for the full walkthrough.
