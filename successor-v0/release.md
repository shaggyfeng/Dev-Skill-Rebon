# Release family

## Lifecycle

```text
Work result
  -> reconciliation
  -> eligible lifecycle operations
  -> Review and Decision when semantic
  -> effect or non-effect result
  -> closure review | Work, Plan, or Design reopen
```

Release owns reconciliation, lifecycle order, result consumption, effect or terminal compilation, and returns. It does not own Domain Modeling writes, Common Sense matching, Review findings, Decision, or implementation recovery.

## Runtime calls

| When | Load | Return to |
|---|---|---|
| Accepted domain meaning or release context needs an exact write | [Domain Modeling](modules/domain-modeling.md) | Exact semantic write or no change to Release |
| A reusable outcome pattern is eligible | [Review](modules/review.md), then [Decision](modules/decision.md), then [Common Sense](modules/common-sense.md) | Accepted table update or no change to the project table |
| Factual release evidence is missing | [Research](modules/research.md) | Evidence or gap to Release |
| A roadmap or merge proposal needs closure | [Review](modules/review.md), then [Decision](modules/decision.md) | Findings and checklist disposition to the checklist owner |

Only a checklist or merge proposal uses the closure review topology. A non-effect result is a valid release terminal when no eligible lifecycle mutation exists.

## Terminals

`release_complete`, `release_no_effect`, `release_to_work`, `release_to_plan`, `release_to_design`, and `release_blocked`.
