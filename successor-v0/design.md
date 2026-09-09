# Design family

## Lifecycle

```text
Design entry
  -> factual or candidate work by the triggered module
  -> Review when findings are needed
  -> Decision when the output is semantic
  -> accepted design result | Grilling | Research | Prototype | Plan
```

Design owns lifecycle order, module calls and returns, and terminals. It does not own module procedures, Review findings, or Decision.

## Runtime calls

| When | Load | Return to |
|---|---|---|
| Terms, ownership, or a domain boundary block the work | [Domain Modeling](modules/domain-modeling.md) | Candidate model or human-owned gap to Design or Plan |
| A controlling source fact or factual path is missing | [Research](modules/research.md) | Evidence or frontier to Design |
| Human value or experience is the missing information | [Prototype](modules/prototype.md) | Bounded evidence to Design |
| A human-owned decision is unresolved | [Grilling](modules/grilling.md) | Accepted rule or next question to Design |
| A final Grilling decision record governs Design work | [Grilling](modules/grilling.md) | Exact intent baseline and accepted rules bind the Design candidate and Plan handoff |
| A candidate needs findings | [Review](modules/review.md) | Findings to the current Design operation |
| A frozen Design candidate has a semantic choice | [Review](modules/review.md), then [Decision](modules/decision.md) | Findings and semantic disposition to the current Design operation |
| A temporary human action is needed | [Manual Procedure](modules/manual-procedure.md) | Action result to Design |
| Learning or practice is needed | [Teaching](modules/teaching.md) | Learning result to Design |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Design has a current entry result or bounded concern | `design_operation` | The matching module operation under Design's lifecycle |
| A module returns a material result | `design_closure` | Determined Plan handoff, declared return, or the existing gap call |

After each module or meaningful internal operation return, apply bounded closure with Design's current goal, accepted constraints, and declared consumer. A determined result follows that consumer mechanically. A semantic fork uses the existing Review then Decision call; unresolved meaning returns to Grilling, missing facts return to Research, and missing experience returns to Prototype. A bound Design result hands off to Plan.

## Terminals

`design_to_plan`, `design_question`, `design_research`, `design_prototype`, and `design_blocked`.
