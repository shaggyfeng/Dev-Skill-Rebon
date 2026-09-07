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
| A candidate needs findings | [Review](modules/review.md) | Findings to the current Design operation |
| A Design output is semantic | [Decision](modules/decision.md) | Semantic disposition to the current Design operation |
| A temporary human action is needed | [Manual Procedure](modules/manual-procedure.md) | Action result to Design |
| Learning or practice is needed | [Teaching](modules/teaching.md) | Learning result to Design |

Apply the semantic-choice test after each module result. A bound Design result hands off to Plan. Unresolved meaning, scope, authority, or trade-off returns to Grilling; missing facts return to Research.

## Terminals

`design_to_plan`, `design_question`, `design_research`, `design_prototype`, and `design_blocked`.
