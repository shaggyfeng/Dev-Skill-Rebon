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
| A plan candidate needs findings | [Review](modules/review.md) | Findings to the current Plan operation |
| A plan output is semantic | [Decision](modules/decision.md) | Semantic disposition to the current Plan operation |

Every plan candidate that selects, recommends, narrows, or records an option follows Review then Decision. A complete approved ticket set hands off to Work. Missing meaning returns to Design; missing controlling facts return to Research.

## Terminals

`plan_to_work`, `plan_to_design`, `plan_to_research`, and `plan_blocked`.
