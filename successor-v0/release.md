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
| Release needs bounded human-experience evidence | [Prototype](modules/prototype.md) | Evidence to Release |
| Human-owned release meaning, priority, or authority is unresolved | [Grilling](modules/grilling.md) | Accepted rule or next human question to Release |
| A Plan-declared phase, roadmap, or merge unit needs closure | [Review](modules/review.md), then [Decision](modules/decision.md) | Three-axis findings and disposition to the declared closure consumer |
| A compatible host enters or advances a Release boundary | [Host Enforcement](modules/host-enforcement.md) | Checkpoint-scoped reconciliation, effect, or return; `instruction-guided` when no interception exists |

Only a Plan-declared closure unit uses the closure review topology. A non-effect result is a valid release terminal when no eligible lifecycle mutation exists.

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| A Work result reaches Release | `reconciliation_started` | Reconcile the current result and its eligible lifecycle operations |
| An eligible lifecycle operation has its declared input | `effect_ready` | Its declared effect, exact semantic write, non-effect result, or Plan-declared closure review |
| A closure proposal has required findings and disposition | `closure_returned` | Declared closure consumer receives findings or an upstream reopen |

## Operation

1. Enter `reconciliation_started` for the current Work result.
2. At `effect_ready`, use the declared Domain Modeling, Review, Decision, Common Sense, or Research call. Release never invents an eligible effect.
3. For a Plan-declared phase, roadmap, or merge unit, return only after `closure_returned`; Review and Decision findings determine its declared closure disposition.
4. Apply bounded closure to a release result. A determined result uses its declared terminal; a factual gap uses Research, an experience gap uses Prototype or human, human-owned meaning uses Grilling, and a frozen semantic candidate uses Review then Decision.

## Terminals

`release_complete`, `release_no_effect`, `release_to_work`, `release_to_plan`, `release_to_design`, and `release_blocked`.
