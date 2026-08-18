---
description: Stage 2 — turn observations into a state and transition model
agent: agent
---

Read qa-artifacts/${input:ticketId}/exploration.md. Write no test code and no risks.

Build a model of the flow from the observations only.

Write to qa-artifacts/${input:ticketId}/flow-model.md:

```markdown
# Flow model: <flow name>
Source: exploration.md (<date from that file>)
Modelled: <ISO date>

## States
| State | How the user knows they are in it | Entered from |

## Transitions
| From | Action | To | Observed? |

## Inputs and outputs
| Input | Where it goes | Visible result | Persisted result |

## Dependencies
- <external systems, prior state, or data this flow relies on>

## Unobserved transitions
- <every transition that plausibly exists but was not walked>
```

Rules:
- Mark a transition "Observed: yes" only if exploration.md actually recorded it.
  Everything else is "no", including transitions that obviously must exist.
- The Unobserved transitions list is the most useful output of this stage. Do not
  leave it empty to make the model look complete.
- Under Persisted result, write "unknown — not observable from browser" wherever
  exploration.md flagged it. Do not guess.
- Do not propose risks. That is the next stage.
