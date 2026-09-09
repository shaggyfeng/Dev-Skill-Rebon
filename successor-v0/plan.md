# Plan family

## Lifecycle

```text
accepted Design result
  -> project and architecture facts
  -> candidate plan operation
  -> Review when findings are needed
  -> Decision when the output is semantic
  -> specification and tickets
  -> approved Work handoff | Design or Research return
```

Plan owns lifecycle order, module calls and returns, and terminals. It does not own architecture, specification, ticket, Research, Review, or Decision procedures.

## Runtime calls

| When | Load | Return to |
|---|---|---|
| Existing architecture or friction must be understood | [Improve Codebase Architecture](modules/improve-codebase-architecture.md) | Survey or Design return to Plan |
| A new architecture or seam is needed | [Codebase Design](modules/codebase-design.md) | Candidate architecture to Plan |
| A consequential plan spans operations, sessions, or actors | [Two-Layer Development Planning](modules/two-layer-development-planning.md) | Paired candidate view to Plan |
| A requirement contract is needed | [To-Spec](modules/to-spec.md) | Candidate specification to Plan |
| Work must be decomposed | [To-Tickets](modules/to-tickets.md) | Candidate ticket set to Plan |
| Terms or source facts are missing | [Domain Modeling](modules/domain-modeling.md) or [Research](modules/research.md) | Resolved input or upstream return to Plan |
| Planning needs bounded human-experience evidence | [Prototype](modules/prototype.md) | Evidence to the current Plan operation |
| Human-owned planning meaning, priority, or authority is unresolved | [Grilling](modules/grilling.md) | Accepted rule or next human question to Plan |
| A final Grilling decision record governs planning | [Grilling](modules/grilling.md) | Exact intent baseline and accepted rules bind the plan outcome, specification, tickets, and closure-unit selection |
| A plan candidate needs findings | [Review](modules/review.md) | Findings to the current Plan operation |
| A frozen plan candidate has a semantic choice | [Review](modules/review.md), then [Decision](modules/decision.md) | Findings and semantic disposition to the current Plan operation |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Accepted Design result and current planning obligation are present | `plan_operation` | The matching planning module operation |
| A module returns a material plan result | `plan_closure` | Determined Work handoff, declared return, or the existing gap call |

When an accepted Design result or final Grilling decision record covers a complete planned outcome, declare its smallest closure unit from that scope and the planned tickets: one complete implementation, one phase, the roadmap, or a merge. An unresolved unit choice uses Review then Decision. A roadmap unit closes every planned phase before the full roadmap. A Work slice contributes to a closure unit; a checkbox does not create one.

Every plan candidate that selects, recommends, narrows, or records an option follows Review then Decision. After each module or meaningful internal operation return, apply bounded closure with Plan's current goal, accepted constraints, and declared consumer. A determined result follows that consumer mechanically; a factual gap returns to Research, an experience gap returns to Prototype or human, human-owned meaning returns to Grilling, and a frozen semantic candidate uses the existing Review then Decision call. A complete approved ticket set hands off to Work; missing accepted Design meaning returns to Design.

## Terminals

`plan_to_work`, `plan_to_design`, `plan_to_research`, and `plan_blocked`.
