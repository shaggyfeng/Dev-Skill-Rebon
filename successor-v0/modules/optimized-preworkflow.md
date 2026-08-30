# Optimized preworkflow

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | any admitted task in `parallel-optimized` or `sequential-optimized` |
| Input | `optimized.preworkflow-input.v0.1`: host, mode, family, `task_id`, `scope_digest`, objective, accepted/excluded scope, authoritative paths/digests, constraints/gates, budgets, output contract, consumer |
| Output | `optimized.applied-plan.v0.1`: evidence summary, atomic task graph, reviewed corrections, applied cards, finding dispositions, verification/recovery, status, consumer |
| Authority | read, decompose, review, and apply plan corrections only |
| Failure | `preworkflow_not_admitted` |
| Consumer | exact admitted Route, Design, Plan, Work, Review, Release, or Recovery task |

## Stage contracts

| Stage | Versioned output | Consumer |
|---|---|---|
| Reader/Task Breaker evidence cards | `optimized.evidence-fragment.v0.1` | Reader/Task Breaker summary card |
| Reader/Task Breaker summary card | `optimized.evidence-summary.v0.1` | Planner |
| Planner | `optimized.plan-candidate.v0.1` | Deep Reviewer |
| Deep Reviewer | `optimized.plan-review.v0.1` | Plan Applier |
| Plan Applier | `optimized.applied-plan.v0.1` | exact admitted family or Recovery task |

Each stage output carries the shared envelope, predecessor output digest, role authority, complete/blocked status, and next consumer. `sequential-optimized` additionally loads `successor-v0/modules/handoff-and-boundaries.md` and emits `runtime.handoff.v0.1` between each adjacent stage.

## Operation

1. Reader/Task Breaker evidence cards read only bounded admitted units and emit `optimized.evidence-fragment.v0.1`.
2. The Reader/Task Breaker summary card consumes persisted evidence fragments, binds `maxFiles=0` and `maxReads=0`, and emits `optimized.evidence-summary.v0.1` with coverage, atomic subtasks, dependencies, and unresolved questions.
3. Planner consumes the exact summary digest and emits `optimized.plan-candidate.v0.1` with one-outcome cards, exact inputs/exclusions, dependencies, read/write sets, budgets, output contract, verification, recovery, and consumers.
4. Deep Reviewer consumes the plan digest and emits `optimized.plan-review.v0.1` with findings for authority, completeness, dependency order, context fit, conflicts, verification, and missing substantive work.
5. Plan Applier consumes the plan and review digests and emits `optimized.applied-plan.v0.1` with every finding disposition.
6. In `sequential-optimized`, emit and validate `runtime.handoff.v0.1` after each stage before the next stage begins; re-enter this module and the handoff module after every transfer.
7. Validate identical task/scope bindings and predecessor digest continuity across all role and evidence outputs.
8. Admit task-specific work only after the applied plan is complete.

## Mode behavior

| Mode | Behavior |
|---|---|
| `parallel-optimized` | four bounded subagent roles; persisted valid structured output is terminal |
| `sequential-optimized` | same roles in order through planning tools and durable handoffs |
| `parallel-normal` | module forbidden; use bounded family-owned reader/writer/reviewer delegation |
| `sequential-normal` | module forbidden |

Every card binds `card_id`, `task_id`, objective, inputs, exclusions, dependencies, read/write sets, owner role, context/tool/output budgets, output contract, verification, recovery, and next consumer.

## Invariants

- Run once after minimum task admission and before the first task-specific operation for every optimized task in every family, including Recovery.
- One outcome per card; broad read/write/review combinations split.
- Evidence cards read; synthesis, planning, and review cards consume persisted evidence and bind `maxFiles=0` and `maxReads=0`.
- Same-artifact writers serialize; dependency-independent write-disjoint cards may parallelize.
- Human gates remain unresolved and preworkflow never implements, approves, or closes checklists.
- Reuse requires current `{host, mode, family, task_id, scope_digest, next_consumer}`.

## Recovery

- Oversized input: estimate, partition, summarize, and aggregate.
- Missing structured output: split into bounded evidence cards followed by a read-free synthesis card before bounded recovery.
- `parallel-optimized` retry: smaller schema and explicit single structured-output call.
- Optimized synthesis recovery binds the smaller schema, `maxFiles=0`, `maxReads=0`, and one immediate structured-output call.
- An unchanged retry is forbidden; after one failed broad card, split by bounded evidence scope before retry.
- Valid output followed by worker continuation: persist the result and terminate the worker.
- Failed stage: preserve valid predecessors and rerun the failed stage plus dependents.
- Changed binding: invalidate and rerun the preworkflow.
- Missing or stale sequential handoff: return `preworkflow_not_admitted` through `handoff_blocked` or `boundary_revalidation_required`.

## Completion

Complete only when all required stage outputs exist, bindings match, every finding has a disposition, every card is bounded, and the exact task consumer is named.
