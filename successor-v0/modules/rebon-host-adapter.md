# Rebon host adapter

## Admission

| Field | Value |
|---|---|
| `host` | `rebon` |
| `input` | admitted provider-neutral task and selected mode |
| `output` | host execution binding and result envelope |
| `authority` | native dispatch and host state only |
| `consumer` | current DevSkill family task |

Rebon does not choose mode, change the applied plan, grant approval, or replace a DevSkill review.

## Tool profile

| Mode | Required tools |
|---|---|
| any Rebon mode | `TaskCreate`, `TaskGet`, `TaskList`, `TaskUpdate` |
| `parallel-normal` | `Workflow`, `parallel`, `pipeline`, `agent`, `StructuredOutput`; `Workflow` is the mandatory outer dispatcher |
| `parallel-optimized` | parallel-normal tools plus `EnterPlanMode`, `PlanLedger`, `ExitPlanMode` |
| `sequential-normal` | sequential step runner |
| `sequential-optimized` | sequential step runner plus `EnterPlanMode`, `PlanLedger`, `ExitPlanMode` |

Discover current native schemas with `ToolSearch`. Missing required capability returns `tool_profile_not_admitted`.

## Task lifecycle

Use host task tools for every admitted Rebon operation:

1. `TaskCreate` one record for each explicit plan work unit; when no explicit unit exists, create one record for the admitted operation.
2. `TaskList` after creation and before frontier selection, dispatch, resume, recovery, split, replacement, or reconciliation.
3. `TaskGet` the selected current task before every dispatch, resume, recovery, split, replacement, or reconciliation.
4. `TaskUpdate` after dispatch, module result, review, fix, verification, integration, block, recovery, or terminal result.

Host task identity binds `{operation_id, family_id, task_id}` plus `phase_id` or `slice_id` only when the owning family defines it. Do not infer a phase/slice Cartesian product. A task record does not authorize work, replace a review, or establish DevSkill completion.

Host task states are:

`planned`, `ready`, `running`, `blocked`, `needs_fix`, `verified`, `integrated`, `cancelled`, `completed`.

`completed` means host synchronization only. DevSkill terminal state is separate.

## Mode dispatch

| Mode | Execution |
|---|---|
| `parallel-normal` | bounded Workflow reader, writer/implementer, reviewer, verifier, and integrator cards; `parallel`, `pipeline`, `agent`, and `StructuredOutput` remain inside the Workflow; no optimized preworkflow or optimized retry rounds |
| `parallel-optimized` | one task-scoped four-role preworkflow before each admitted task, then bounded Workflow |
| `sequential-normal` | provider-neutral sequential step runner; no `agent()` or `parallel()` |
| `sequential-optimized` | one task-scoped four-role sequential preworkflow before each admitted task, then sequential step runner |

Bind optimized host operations to the task scope and reuse predicate in `SKILL.md`. Apply that rule to every Rebon family task.

## Named-agent binding

When Rebon is the host and either parallel mode is selected, classify every spawned subagent task before dispatch:

| Task | `owner_role` | Named agent |
|---|---|---|
| evidence, repository reading, task breaking | `Explore` | `Explore` |
| planning, decomposition, dependency shaping | `Plan` | `Plan` |
| Standards, Specification, verification, review | `verification` | `verification` |
| exact admitted implementation, Plan Applier | `batch-worker` | `batch-worker` |
| no matching specialized role | `general-purpose` | resolved available general-purpose agent |

Apply the binding to Workflow cards, parallel branches, pipeline callbacks, direct Agent calls, background LocalAgent tasks, nested delegates, continuations, recovery workers, review workers, verification workers, fix workers, and attention sidecars.

Review card topology in either parallel mode is exact: grilling uses one `verification` card with Standards + Specification; an implementation slice uses two `verification` cards, one Standards and one Specification; roadmap checklist closure and merge-PR review use two `verification` cards, one Standards + Correctness and one Specification-compliance + Correctness. Each card binds `review_kind`, `assignment_id`, assigned axes, named agent, budgets, frozen digest, persisted result, and consumer. A missing, merged, or unpersisted assignment blocks aggregation.

If an explicit role/agent pair conflicts or a named selector is unavailable, reclassify the task and select the best available role. Use explicit `general-purpose` when no specialized role fits. Unresolved binding returns `agent_role_not_admitted`.

Non-Rebon hosts never use these bindings.

## Optimized preworkflow

| Role | Input | Output | Authority |
|---|---|---|---|
| Reader/Task Breaker | bounded task scope | compact evidence/task summary | read and decomposition only |
| Planner | compact summary | bounded plan | plan only |
| Deep Reviewer | bounded plan | findings and corrections | findings only |
| Plan Applier | reviewed bounded plan | applied plan or handoff | exact plan scope only |

`parallel-optimized` dispatches the four roles through Workflow. `sequential-optimized` runs them in order and stores the accepted plan in the host planning handoff. Neither path grants approval, implementation authority, or checklist closure.

## Structured cards

Every parallel structured card binds:

`maxIterations`, `maxToolCalls`, `maxFiles`, `maxReads`, `maxOutputTokens`, and a valid structured-output terminal/stop hook.

Card read/synthesis boundary:

- A card with `maxReads > 0` is an evidence card and may emit only bounded evidence fragments or task-break data.
- A planning, review, decision, or synthesis card consumes persisted evidence, binds `maxFiles=0` and `maxReads=0`, and cannot perform repository exploration.
- A task requiring repository exploration and synthesis is split into evidence cards followed by one synthesis card before dispatch.
- A split uses declared coverage units; source/test pairing is used only when the input declares that seam.

Dispatch rules:

1. Admit the current structured-output hook and budgets.
2. Require exactly one structured-output call.
3. Validate and persist a valid result before consuming later worker status.
4. Treat the persisted result as terminal for that worker.
5. Split an oversized evidence or review card before retry.
6. In `parallel-optimized`, retry only with a smaller schema and explicit single-call output instruction.
7. After missing output or an oversized broad card, dispatch bounded evidence cards followed by a read-free synthesis card; do not retry the same broad card.

Smaller-schema single-call recovery applies only to `parallel-optimized`; it is forbidden in `parallel-normal` and both sequential modes.

Statuses:

| Condition | Status | Route |
|---|---|---|
| no valid result before worker return | `structured_output_missing` | split or bounded recovery when eligible; otherwise `orchestration_blocked` |
| budget ends before result | `iteration_budget_exhausted_before_output` | split or bounded recovery when eligible; otherwise `orchestration_blocked` |
| result persisted before later worker continuation | `structured_output_received_but_worker_continued` | `result_persisted` |

The last status retains the persisted result and follows the completed-result route.

## State machine

```text
profile_unresolved -> tool_profile_not_admitted
profile_admitted -> task_records_bound
task_records_bound -> preworkflow_pending | execution_ready
preworkflow_pending -> execution_ready | preworkflow_not_admitted
execution_ready -> running | card_not_admitted | agent_role_not_admitted
running -> result_persisted | structured_output_missing | iteration_budget_exhausted_before_output | orchestration_blocked | interrupted
structured_output_missing | iteration_budget_exhausted_before_output -> split | orchestration_blocked
result_persisted -> host_task_updated -> DevSkill_consumer
interrupted -> resumed | split | orchestration_blocked
resumed | split -> preworkflow_pending | execution_ready
```

## Re-entry and recovery

- `TaskList` and `TaskGet` before resume, recovery, split, replacement, or plan reopen.
- Re-enter Stage 0 and the current family before the next host call after a task result or changed consumer.
- Re-run optimized preworkflow for a new or invalidated scope, or when the exact durable result is absent, stale, or incomplete after resume.
- Reconcile host tasks against the current applied plan; do not repair conflicts from model memory or by deleting records.
- Persist a valid structured result before reporting worker exhaustion.
- Return a typed block when required schema, task state, plan digest, role binding, or next consumer is missing.

## Module registry

`work.parallel-card-set.v0.1` binds operation/family/task IDs, scope digest, dependency graph, one-outcome cards, owner roles, budgets, output contracts, recovery, and consumers. `rebon.workflow-result.v0.1` binds the native run/card IDs, persisted structured outputs, statuses, errors, task-ledger updates, and provider-neutral consumer. Both retain the originating provider-neutral authority ceiling.

| Module | Versioned input | Versioned output | Actor and ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `work.rebon-schema-contract` | `rebon.tool-profile-request.v0.1` | `rebon.tool-profile.v0.1` | discovery/admission only | adapter-local | `tool_profile_not_admitted` | Rebon dispatcher |
| `work.rebon-task-list-contract` | `rebon.task-binding-request.v0.1` | `rebon.task-binding.v0.1` | host state only | adapter-local | `task_list_not_admitted` | current family task |
| `work.ultrawork-orchestration` | `work.parallel-card-set.v0.1` | `rebon.workflow-binding.v0.1` | native dispatch only | adapter-local | `orchestration_blocked` | Rebon dispatcher |
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | planning only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact current family or Recovery task |
| `work.rebon-display-adapter` | `rebon.workflow-result.v0.1` | `rebon.result-envelope.v0.1` | result translation only | adapter-local | `adapter_not_admitted` | current DevSkill family consumer |

## Invariants

- Rebon host state never grants DevSkill authority, approval, review completion, or checklist closure.
- Every spawned Rebon parallel task has one admitted named-agent binding.
- Every structured card has finite numeric budgets and exactly one terminal structured-output call.
- A persisted valid structured result cannot be replaced by later worker failure status.

## Terminals

`DevSkill_consumer`, `tool_profile_not_admitted`, `task_list_not_admitted`, `card_not_admitted`, `agent_role_not_admitted`, `preworkflow_not_admitted`, and `orchestration_blocked` are the only adapter terminals.

The adapter is dispatch and synchronization only. It cannot mutate the project outside the exact admitted provider-neutral family task.

