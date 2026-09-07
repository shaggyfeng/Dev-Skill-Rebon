# Optimized preworkflow

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | any admitted task in `parallel-optimized` or `sequential-optimized` |
| Input | `optimized.preworkflow-input.v0.1`: host, mode, family, `task_id`, `scope_digest`, objective, accepted/excluded scope, authoritative paths/digests, constraints/gates, budgets, `retry_budget`, output contract, consumer |
| Output | `optimized.applied-plan.v0.1`: read-plan digest, evidence summary, atomic task graph, reviewed corrections, applied cards, finding dispositions, verification/recovery, `retry_budget`, status, consumer |
| Authority | read, decompose, review, and apply plan corrections only |
| Failure | `preworkflow_not_admitted` |
| Consumer | exact admitted Route, Design, Plan, Work, Review, Release, or Recovery task |

## Stage contracts

| Stage | Versioned output | Consumer |
|---|---|---|
| Deterministic read admission | `runtime.read-plan.v0.1` | Reader/Task Breaker evidence cards |
| Reader/Task Breaker evidence cards | `optimized.evidence-fragment.v0.1` | Reader/Task Breaker summary card |
| Reader/Task Breaker summary card | `optimized.evidence-summary.v0.1` | Planner |
| Planner | `optimized.plan-candidate.v0.1` | Deep Reviewer |
| Deep Reviewer | `optimized.plan-review.v0.1` | Plan Applier |
| Plan Applier | `optimized.applied-plan.v0.1` | exact admitted family or Recovery task |

Each stage output carries the shared envelope, predecessor output digest, role authority, complete/blocked status, remaining `retry_budget`, and next consumer. `sequential-optimized` additionally loads `successor-v0/modules/handoff-and-boundaries.md` and emits `runtime.handoff.v0.1` between each adjacent stage.

## Preworkflow transition representation

The optimized preworkflow is one bounded sequence with read admission preceding every content reader and read-free synthesis after complete coverage:

```text
admitted_task
 -> read_plan_bound | preworkflow_not_admitted
read_plan_bound
 -> evidence_partitioned | evidence_unit_admitted
evidence_unit_admitted
 -> evidence_fragment_persisted | preworkflow_not_admitted
evidence_partitioned
 -> evidence_fragment_persisted | preworkflow_not_admitted
evidence_fragment_persisted
 -> coverage_complete | evidence_unit_admitted | preworkflow_not_admitted
coverage_complete
 -> evidence_summary
evidence_summary
 -> plan_candidate | preworkflow_not_admitted
plan_candidate
 -> plan_review | preworkflow_not_admitted
plan_review
 -> applied_plan | preworkflow_not_admitted
applied_plan
 -> task_specific_work
```

| Gate | Required binding | Result and next consumer |
|---|---|---|
| `read_plan_bound` | current target, deterministic discovery, budgets, coverage accumulator, frontier, consumer, and finite `retry_budget` | admitted bounded evidence units |
| `evidence_fragment_persisted` | one admitted coverage unit, source identity, observed coverage, omissions, status, remaining frontier, and predecessor digest | coverage accumulator or next bounded unit |
| `coverage_complete` | every required unit complete or explicitly blocked, with no duplicate or overlapping unit | read-free summary card with `maxFiles=0` and `maxReads=0` |
| `plan_candidate` | exact summary digest, one outcome per card, dependencies, scopes, budgets, verification, recovery, and consumer | Deep Reviewer |
| `plan_review` | current plan digest, findings, authority/completeness/dependency/context/verification checks | Plan Applier |
| `applied_plan` | every finding disposition, predecessor continuity, current bindings, and structured output persistence | exact admitted family or Recovery task |

In `sequential-optimized`, each gate emits a validated `runtime.handoff.v0.1` before the next unit or stage; in `parallel-optimized`, bounded role results persist before read-free synthesis. Reuse and recovery never consume stale scope or retry the same failed broad read unchanged.

## Operation

1. Before dispatching Reader/Task Breaker or opening target content, validate and reuse a supplied current `runtime.read-plan.v0.1`; when none exists, use deterministic listing, search, metadata, and count tools to compile it. Compare file, byte, line, conservative token, read, tool, and output estimates with the card and remaining-context budgets and validate the remaining `retry_budget`. A stale or mismatched supplied plan is invalidated, not silently replaced after reading begins.
2. If the complete target does not fit every budget or fit is uncertain, partition it by declared authoritative seams before the first content read. A broad discovery card without a current passing read plan is not admitted.
3. Reader/Task Breaker evidence cards read only one or more bounded coverage units admitted by the plan and emit `optimized.evidence-fragment.v0.1` with read-plan and coverage-unit bindings.
4. The Reader/Task Breaker summary card consumes the complete persisted coverage set, binds `maxFiles=0` and `maxReads=0`, and emits `optimized.evidence-summary.v0.1` with coverage, atomic subtasks, dependencies, and unresolved questions.
5. Planner consumes the exact summary digest and emits `optimized.plan-candidate.v0.1` with one-outcome cards, exact inputs/exclusions, dependencies, read/write sets, budgets, output contract, verification, recovery, and consumers.
6. Deep Reviewer consumes the plan digest and emits `optimized.plan-review.v0.1` with findings for authority, completeness, dependency order, context fit, conflicts, verification, and missing substantive work.
7. Plan Applier consumes the plan and review digests and emits `optimized.applied-plan.v0.1` with every finding disposition.
8. In `sequential-optimized`, emit and validate `runtime.handoff.v0.1` after every bounded evidence unit and each later stage before the next unit or stage begins; each evidence handoff carries the read-plan digest, completed coverage, remaining frontier, accumulator digest, remaining budgets, and remaining `retry_budget`. Re-enter this module and the handoff module after every transfer.
9. Validate identical task/scope/read-plan bindings and predecessor digest continuity across all role and evidence outputs.
10. Admit task-specific work only after the applied plan is complete.

## Mode behavior

| Mode | Behavior |
|---|---|
| `parallel-optimized` | four bounded subagent roles; persisted valid structured output is terminal |
| `sequential-optimized` | same roles in order through planning tools and durable handoffs |
| `parallel-normal` | module forbidden; use bounded family-owned reader/writer/reviewer delegation |
| `sequential-normal` | module forbidden |

Every card binds `card_id`, `task_id`, objective, inputs, exclusions, dependencies, read/write sets, owner role, read-plan and coverage-unit IDs when reading, file/byte/line/token/read/tool/output budgets, `retry_budget`, output contract, verification, recovery, and next consumer.

## Invariants

- Run once after minimum task admission and before the first task-specific operation for every optimized task in every family, including Recovery.
- Deterministic read admission precedes every content-reading card; target-size discovery never consumes a Reader/Task Breaker model iteration.
- One outcome per card; broad read/write/review combinations split.
- Evidence cards read; synthesis, planning, and review cards consume persisted evidence and bind `maxFiles=0` and `maxReads=0`.
- Every coverage unit appears exactly once in the accumulator; missing, stale, duplicate, or overlapping undeclared units block summary synthesis.
- Same-artifact writers serialize; dependency-independent write-disjoint cards may parallelize.
- Human gates remain unresolved and preworkflow never implements, approves, or closes checklists.
- Reuse requires current `{host, mode, family, task_id, scope_digest, next_consumer}`.

## Recovery

- Oversized or uncertain input detected before reading: partition, summarize, and aggregate under the current read plan.
- Oversized input discovered after reading begins: preserve completed fragments, invalidate the unread frontier, compile a smaller successor read plan, and never retry the failed scope unchanged.
- Missing structured output: split into bounded evidence cards followed by a read-free synthesis card before bounded recovery.
- Every recovery packet and retry binds the current finite nonnegative `retry_budget`; decrement it before each retry and persist the successor value.
- `parallel-optimized` retry: smaller schema and explicit single structured-output call, only while `retry_budget > 0`.
- Optimized synthesis recovery binds the smaller schema, `maxFiles=0`, `maxReads=0`, and one immediate structured-output call; unchanged scope or zero retry budget returns `preworkflow_not_admitted`.
- An unchanged retry is forbidden; after one failed broad card, split by bounded evidence scope before retry. A valid output is persisted and terminal before any later worker continuation.
- Failed stage: preserve valid predecessors and rerun the failed stage plus dependents only with a changed frontier and remaining retry budget.
- Changed binding: invalidate and rerun the preworkflow only after decrementing `retry_budget`; zero returns `preworkflow_not_admitted` with reason `retry_budget_exhausted`.
- Missing or stale sequential handoff: return `preworkflow_not_admitted` through `handoff_blocked` or `boundary_revalidation_required`; do not dispatch when `retry_budget=0`.

## Completion

Complete only when the current read plan exists, every required coverage unit is complete or explicitly blocked, all required stage outputs exist, bindings match, every finding has a disposition, every card is bounded, the remaining retry budget is current, and the exact task consumer is named.
