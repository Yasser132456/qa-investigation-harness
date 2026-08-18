# QA Investigation Harness

A quality gate that sits **around** your existing `playwright-test-generator`
agent — not a replacement for it. It produces the exact same artifact
`playwright-test-planner` normally would (`specs/<ticket>.plan.md`), but built
from a human-reviewed risk table instead of autonomous exploration.
`playwright-test-planner` is intentionally not part of this flow — see
**Why no playwright-test-planner?** below.

This harness never writes test code — not even the plan's own seed file.

```
[Ticket ID + test case] → qa-explore → qa-model → qa-challenge (Part A)
                                            ↓
                              [HUMAN edits risks.md — APPROVE/REJECT]
                                            ↓
                     qa-challenge (Part B) → specs/<ticket>.plan.md
                          (+ Generated mapping appended to risks.md)
                                            ↓
                [HUMAN selects playwright-test-generator from the
             Copilot Chat agents dropdown, points it at the plan file]
                                            ↓
                                      qa-validate
```

## Before you start

- **Playwright MCP is installed**: registered in `.vscode/mcp.json`, launched via `npx @playwright/mcp@latest` — this is separate from the `playwright-test` MCP server bundled inside `playwright-test-generator`'s own agent definition
- **The ticket already exists**: this harness never invents a ticket ID (e.g. `CMDBTEST-1069`) — you supply one that already exists in your test management system
- **Target environment**: non-production only (you'll be asked to confirm before `qa-explore` navigates)
- **Credentials**: never written to any file — you'll be asked for them interactively at the start of `qa-explore`. The confirmed non-production URL itself *is* recorded, since the generated plan needs it, matching how your existing plans already work

## Stage 1: qa-explore

```
/qa-explore
```

**Input**: the ticket ID, and a test case (title + steps, however rough)

**Output**: `qa-artifacts/<ticket>/exploration.md`

Walks the flow the test case describes, once, using Playwright MCP in snapshot
mode. Records what it actually saw — including anything the test case didn't
mention, and anything it claimed that wasn't observed. Lists what can't be seen
from the browser at all (persisted state, emails, background jobs). Also
searches `tests/` for an existing seed file whose starting state matches this
flow, and records the best candidate (or says plainly that none was found — it
never creates one).

Nothing here is a test, a risk, or a recommendation. It's a factual record.

## Stage 2: qa-model

```
/qa-model
```

**Input**: the ticket ID

**Output**: `qa-artifacts/<ticket>/flow-model.md`

Turns the raw observations into states and transitions. The most useful part of
this output is the **Unobserved transitions** list — every transition that
plausibly exists but wasn't walked in Stage 1. That list feeds directly into the
risk table next.

## Stage 3: qa-challenge (Human Approval Required)

```
/qa-challenge
```

Runs in two parts.

**Part A — risk table.** Reads exploration.md and flow-model.md, produces:

| ID | Step/State | Risk | Proposed oracle | Observable | Status |
|----|------------|------|-----------------|------------|--------|
| R1 | ...        | ...  | ...             | yes/no/partial | PROPOSED |

Every row starts `PROPOSED`. **You edit `risks.md` by hand** and set each row to
`APPROVED` or `REJECTED`. Nothing in this harness ever does that for you.

- Approve a row if the risk is real and worth covering — even if `Observable` is
  `no`. The gap becomes documented, not silently dropped.
- Reject rows that don't matter, or that duplicate another row.
- Don't touch `Proposed oracle` unless the oracle itself is wrong — it's meant to
  state what would *actually* prove success, not what's convenient to check.

**Stop here and review `risks.md` before continuing.**

**Part B — test plan.** Once you've approved rows, run the same prompt again and
tell it to continue to Part B:

```
/qa-challenge run Part B
```

**Output**: `specs/<ticket>.plan.md` — written (or appended to) in exactly the
structure `playwright-test-planner` produces:

```markdown
# CMDBTEST-1069 - CMDB Reconciliation Workflow

## Application Overview
<prose, written from what qa-explore actually observed>

## Test Scenarios

### 1. CMDBTEST-1069 - CMDB Reconciliation Workflow

**Seed:** `tests/seed.spec.ts`

#### 1.1. TC-01: <title from an approved risk>

**File:** `tests/cmdb/tc-01-....spec.ts`

**Steps:**
  1. <verified step>
    - expect: <the approved oracle, in prose>
```

One `TC-<NN>` block per `APPROVED` risk row. If a risk's evidence isn't fully
observable from the browser, its `expect:` still states the real oracle, plus a
second `expect:` bullet flagging it as a GAP — never a weaker substitute
assertion. If `specs/<ticket>.plan.md` already exists, existing `TC-<NN>`
entries are never touched — new ones are appended, numbered onward from the
highest one present.

It also appends a **Generated mapping** section to the bottom of `risks.md`
(never touching the risk table above it), recording which `TC-<NN>` and file
each `Risk ID` produced. This is how `qa-validate` later checks the right test
against the right approved risk.

If `qa-explore` found no matching seed file, the plan still gets written, but
you're told plainly that generation can't proceed until one exists — creating
one isn't this harness's job.

## Hand-off: playwright-test-generator

This is the step the harness deliberately does not automate — and it needs no
translation step, because `specs/<ticket>.plan.md` is already the artifact your
generator expects.

`playwright-test-generator` is a **custom agent** (`.agent.md`, bundled with its
own MCP server), not a prompt file — so you don't invoke it with `/`. Instead:

1. Open Copilot Chat, click the **agents dropdown** at the bottom of the panel
2. Select **playwright-test-generator**
3. Point it at `specs/<ticket>.plan.md`, exactly as you would with a
   planner-produced plan

The generator drives the browser itself to execute each step in the plan
(that's how it verifies its own code before writing it), then saves one spec
file per `TC-<NN>` entry.

**Why no playwright-test-planner?** Its own instructions have it always explore
the flow from scratch and invent its own scenarios — it has no mechanism to
accept an external document as ground truth. Running it after `qa-challenge`
risks it silently discarding your entire risk review and testing whatever it
independently decided mattered, and would break `qa-validate`'s ability to
trace a generated test back to a specific approved risk. So this harness routes
straight to the generator: `qa-explore` / `qa-model` / `qa-challenge` are this
harness's own stand-in for what the planner would otherwise do — with a human
approval gate the planner doesn't have.

## Stage 5: qa-validate

Run this after `playwright-test-generator` has produced spec files from the plan.

```
/qa-validate
```

**Part A — oracle conformance.** Reads `risks.md`'s **Generated mapping**
section to find, for each approved risk, which `TC-<NN>` and file it produced.
Cross-references `specs/<ticket>.plan.md`'s `expect:` bullets for that `TC-<NN>`
against the actual generated assertion, and checks it isn't weaker than what
was approved. Writes findings to `qa-artifacts/<ticket>/validation.md`. Fixes
nothing.

**Part B — mutation check.** Run explicitly:

```
/qa-validate run Part B
```

Runs the specs and reports raw output. Then, for each passing test, temporarily
mutates one expected value, re-runs it alone, and restores the original
immediately. Any test that still passes after mutation goes under
`ASSERTION TOO WEAK` in `validation.md` — meaning the assertion isn't actually
checking what it claims to.

Nothing here is described as "passing," "healthy," or "adequate." You decide
what the raw output and the mutation results mean.

---

## File structure

```
.github/
  copilot-instructions.md         ← Harness rules (read once)
  prompts/
    qa-explore.prompt.md          ← Stage 1: walk the flow, find a matching seed
    qa-model.prompt.md            ← Stage 2: state/transition model
    qa-challenge.prompt.md        ← Stage 3: risk table + specs/<ticket>.plan.md
    qa-validate.prompt.md         ← Stage 5: post-generation audit
  agents/                         ← (already exists in your project — verify the
                                     exact path) playwright-test-planner.agent.md,
                                     playwright-test-generator.agent.md. Not part
                                     of this harness; invoked via the agents
                                     dropdown, not a slash command.
.vscode/
  mcp.json                        ← General-purpose Playwright MCP config, used
                                     only by qa-explore/qa-validate — separate
                                     from the generator's own bundled MCP server
specs/
  <ticket>.plan.md                ← Stage 3B output — the real artifact handed
                                     to playwright-test-generator
qa-artifacts/                     ← One folder per ticket, harness-internal
  <ticket>/
    exploration.md                ← Stage 1 output
    flow-model.md                 ← Stage 2 output
    risks.md                      ← Stage 3A output (+ Generated mapping,
                                     appended by 3B) — YOU EDIT THE TABLE
    validation.md                 ← Stage 5 output
```

## Ground rules

- **One flow per session.** Reset the Copilot session before starting a second flow.
- **This harness never writes test code**, before or after generation — including seed files. That stays with `playwright-test-generator`.
- **`playwright-test-planner` is bypassed by design**, not by oversight — see above.
- **`risks.md`'s Status column is a human-only field.** No prompt in this harness ever sets `APPROVED` or `REJECTED`. Its appended **Generated mapping** section is machine-written, but the table above it isn't.
- **Existing `TC-<NN>` entries in a plan file are never rewritten**, only appended to.
- **Snapshot mode only** — the accessibility tree, not screenshots or coordinates.
- **Non-production only.** `qa-explore` stops and asks if the target URL looks like production. Once confirmed, the URL is recorded (it belongs in the plan) — credentials never are, anywhere.
- **Staleness is tracked automatically** within `qa-artifacts/<ticket>/`. Each file records the date of the artifact it was built from. Re-running `qa-explore` will cause later stages to flag downstream files as stale.

## Environment variables

```bash
export BASE_URL="https://staging.example.com"
export TEST_ACCOUNT_EMAIL="user@example.com"
export TEST_ACCOUNT_PASSWORD="<password>"
```

`qa-explore` asks for these interactively rather than reading them from env.
Credentials are never written to disk; the URL is, once confirmed non-production.

## Workflow summary

```
1. /qa-explore                   Input: ticket ID + test case → exploration.md
                                  (also looks for a matching seed file)
2. /qa-model                     Input: ticket ID              → flow-model.md
3. /qa-challenge                 Input: ticket ID               → risks.md (all PROPOSED)
   ↓ YOU HAND-EDIT risks.md: set each row to APPROVED / REJECTED ↓
4. /qa-challenge run Part B                    → specs/<ticket>.plan.md
                                  → risks.md gets a Generated mapping section
5. Agents dropdown → playwright-test-generator → point it at specs/<ticket>.plan.md
   (playwright-test-planner is intentionally skipped — see above)
6. /qa-validate                  Input: ticket ID               → validation.md
7. /qa-validate run Part B       — runs specs, mutation-checks assertions
```

---

**Questions?** Refer to `.github/copilot-instructions.md` for the full rule set.
