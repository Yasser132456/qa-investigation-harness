---
description: Stage 3 — risk table, then a test plan for playwright-test-generator
agent: agent
---

Read qa-artifacts/${input:ticketId}/exploration.md and
qa-artifacts/${input:ticketId}/flow-model.md. Write no test code.

This prompt has two parts. Part B runs only after the human has edited risks.md.

--- PART A: risk table ---

For each state, each transition (especially unobserved ones), each item under
"Observed but not in the test case", and the flow as a whole, ask:
- What could fail here?
- What does the test case assume without verifying?
- What evidence would actually prove success?
- Is that evidence observable from the browser?

Write to qa-artifacts/${input:ticketId}/risks.md:

```markdown
# Risks: <flow name>
Ticket: ${input:ticketId}
Source: flow-model.md (<date from that file>)
Challenged: <ISO date>

| ID | Step/State | Risk | Proposed oracle | Observable | Status |
|----|------------|------|-----------------|------------|--------|
| R1 | ...        | ...  | ...             | yes/no/partial | PROPOSED |
```

Rules:
- Every row starts PROPOSED. Never write APPROVED or REJECTED.
- APPROVED will mean "this risk is real and should be covered." Write each row
  so that decision is answerable in one read.
- Describe oracles in prose. Never write them as code.
- Name the oracle that would ACTUALLY prove success, even when it requires an API
  or database check this project cannot yet perform. Mark those "no" or
  "partial". Never substitute a weaker browser-only check because it is
  reachable today.
- A rendered confirmation message is not evidence of a completed action. Flag any
  row whose only oracle is rendered text as an evidence gap in the Risk column.
- Cover every unobserved transition from flow-model.md with at least one row.
- Over-generate. The human's job is rejection, so give candidates to reject
  rather than a pre-filtered list.
- Include at least two rows the browser cannot fully verify.

Then stop and tell the human to review risks.md before running Part B.

--- PART B: test plan for playwright-test-generator ---

Run only when asked, and only after checking:
1. risks.md contains rows with status APPROVED. If zero, stop and say the review
   is still pending.
2. The Source date chain is intact: risks.md → flow-model.md → exploration.md.
   If any link is stale, say which stage needs re-running.

This writes specs/${input:ticketId}.plan.md — the SAME artifact
playwright-test-planner would normally produce, in the same structure, at the
same path, so playwright-test-generator can read it exactly as it always does.
playwright-test-planner is not invoked; see copilot-instructions.md for why.

If specs/${input:ticketId}.plan.md already exists:
- Read it first. Never renumber or rewrite existing TC-<NN> entries.
- Append new TC-<NN> entries for newly-approved risks, continuing the numbering
  from the highest existing TC number in the file.
- If the file has no existing entries for this exact flow, start at TC-01.

Write (or append to) specs/${input:ticketId}.plan.md in this exact structure —
match it precisely, it is read by playwright-test-generator:

```markdown
# ${input:ticketId} - <flow title>

## Application Overview

<one paragraph, written from exploration.md: what application/flow this covers,
its URL, and the screens or steps involved. Prose, third person, same style as
a human-written plan — do not mention risks, oracles, or this harness.>

## Test Scenarios

### 1. ${input:ticketId} - <flow title>

**Seed:** `<seed file path from exploration.md's Candidate seed files, or, if
none was found, the literal text "NONE FOUND — a seed file must exist before
this can be generated">`

#### 1.<n>. TC-<NN>: <short title derived from the risk>

**File:** `tests/<domain-slug>/tc-<NN>-<case-slug>.spec.ts`

**Steps:**
  1. <verified step from exploration.md, in the plan's plain action style>
    - expect: <the approved oracle, in prose, as the reader would expect a
      real behavioral assertion — this is what the generator turns into code>
  2. <next step>
    - expect: <...>
```

One `#### 1.<n>. TC-<NN>` block per APPROVED risk row, numbered sequentially.
`<domain-slug>` is a short kebab-case name for the feature area (derived from
the flow title, matching the style of existing files under tests/). Steps come
from exploration.md's verified path, not invented.

When a risk's Observable is "no" or "partial", still write a real `- expect:`
bullet stating the approved oracle as-is (do not weaken it), then add one more
bullet immediately after it:
    - expect: GAP — browser cannot verify this. Correct oracle requires
      <what the approved oracle actually needs, e.g. an API/DB check>. Not
      wired into this project yet.
This keeps the gap visible to whoever reads the plan or the generated test,
without pretending the browser-level check is sufficient.

Then, in the SAME response, append a new section to the BOTTOM of
qa-artifacts/${input:ticketId}/risks.md — after the existing risk table, which
must not otherwise be touched — recording the mapping just created:

```markdown

## Generated mapping (<ISO date>)
| Risk ID | TC ID | File |
|---------|-------|------|
| R1      | TC-04 | tests/<domain-slug>/tc-04-<case-slug>.spec.ts |
```

This mapping is what qa-validate uses later to connect an approved risk to the
test that's supposed to cover it. If Part B is run more than once for this
ticket, append a new dated "## Generated mapping" section each time rather than
overwriting the previous one.

Rules:
- Include APPROVED rows only. Ignore PROPOSED and REJECTED entirely, however
  important they appear.
- Write no code — steps and expectations are prose, in the plan's own
  documentation style, not assertions in TypeScript.
- specs/${input:ticketId}.plan.md must read as a normal test plan. Do not
  mention risks.md, APPROVED status, or this harness inside it — that context
  lives in qa-artifacts/${input:ticketId}/, referenced only from the mapping
  section in risks.md.
- If the seed file is "NONE FOUND", still write the plan, but say plainly to
  the human that generation cannot proceed until a seed file exists, and that
  creating one is outside this harness's authority.
- Do not claim the flow is adequately covered.
