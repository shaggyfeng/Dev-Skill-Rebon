---
name: dev-skill
description: Mandatory engineering route for repository inspection, planning, implementation, diagnosis, testing, review, refactoring, release, and roadmap closure.
when_to_use: Use for every software-engineering task. Do not use for unrelated non-engineering requests.
user-invocable: true
disable-model-invocation: false
---

# DevSkill runtime

Use this package as the sole DevSkill runtime instruction source. Do not search this package for history, evidence, tests, catalogs, validators, or legacy records.

## Activation

Before repository inspection or task work, emit:

`DevSkill: ACTIVE`

If activation cannot be emitted, stop with `devskill_not_active`.

## Admission

Run `mode-gate.md` and obtain its complete admitted profile before task work.

No repository inspection, mutation, delegation, Workflow, or checklist update occurs before `route_status=admitted`.

## Load order

1. `manifest.json`
2. `mode-gate.md`
3. `successor-v0/stage-0-route.md`
4. The selected family: `successor-v0/design.md`, `successor-v0/plan.md`, `successor-v0/work.md`, or `successor-v0/release.md`
5. The exact `successor-v0/modules/*.md` file named by each admitted registry row, after its trigger and prerequisites match and before module execution
6. `successor-v0/modules/review.md` when review is active
7. `successor-v0/modules/rebon-host-adapter.md` when host is Rebon

Resolve DevSkill instruction paths only inside this package.

## Route and triggers

Run Stage 0 before every engineering task. Evaluate every applicable direct overlay, then select one family route or Route terminal:

| Signal | Required route or overlay |
|---|---|
| Design grilling | `deep-decision-review` with `review_context=grill_infrastructure`, `review_kind=grill`: one reviewer with Standards + Specification |
| Runtime/instructional implementation review | `stage-0.review` with `review_kind=implementation_logic`: two reviewers, one Standards and one Specification |
| Roadmap checklist closure | `stage-0.review` with `review_kind=checklist_close`: two reviewers, Standards + Correctness and Specification-compliance + Correctness |
| Merge-PR review | `stage-0.review` with `review_kind=checklist_close`: two reviewers, Standards + Correctness and Specification-compliance + Correctness |
| Explicit unslop or structural-thinking request, or admitted structural-slop finding | `stage-0.think` |
| Explicit diagnosis request with a valid Work entry and bounded symptom | `work.diagnose` |
| Applicable unsuppressed failure signal | `route.attention` |
| Route intake greenfield decision or value gap requiring untangling | `deep-decision-review` with `review_context=intake` |
| Unresolved meaning, scope, trade-off, or human decision | Design |
| Architecture, specification, or ticket work from a Design handoff | Plan |
| Authorized implementation or fix from a Plan handoff | Work |
| Release-candidate preparation from Work | Release |
| No family transition or failure | active-stage continuation or Route terminal |

`successor-v0/stage-0-route.md` composes every applicable overlay and resolves consumer conflicts. Family registries define module triggers, versioned inputs and outputs, authority, runtime file, consumers, and failure routes. Activate a module only when its trigger, prerequisites, current bindings, authority, runtime file, and consumer validate. A family-local controller operation without a module file remains governed by the family contract.

## Runtime modules

Load only admitted module files. Every registry module uses `devskill.module.v0.2`. A doctrine row in the Route Module-loading table is admitted by its row condition alone, carries no envelope, and makes no admission decision. Every versioned module input and output carries `runtime.module-envelope.v0.1`:

`{contract_version, task_id, scope_digest, input_digest, output_digest, producer, consumer, authority, status, failure_route}`

`status` is exactly `complete`, `blocked`, `reopen`, or `invalidated`. Input `output_digest` is null; an output without a current non-null `output_digest` cannot be complete. `failure_route` is null only for `complete`; every other status names the typed route. A module file is authoritative for its repeatable procedure; the family controller remains authoritative for lifecycle order and terminals.

## Review topology

| Review surface | Required reviewer assignment |
|---|---|
| Grilling | one reviewer with Standards + Specification |
| Runtime/instructional implementation slice | two reviewers: one Standards and one Specification |
| Roadmap checklist closure or merge-PR review | two reviewers: reviewer 1 Standards + Correctness; reviewer 2 Specification-compliance + Correctness |

In either parallel mode, each assignment is one bounded reviewer subagent and its result is persisted before integration. Sequential modes execute the same assignments as separate current-context passes. A missing assignment blocks review; no topology may be merged into one reviewer or replaced by inline review in a parallel mode.

For an implementation slice, both the Standards and Specification reviewers inspect runtime/instructional logic and reachable behavior within their assigned axis. Premature context, tool-count, delegation, or optimization changes are outside this review unless they change the runtime contract.

Module prerequisites are the matched trigger, complete current versioned input and envelope, admitted authority, available runtime file, and valid consumer. Missing or stale prerequisites return `blocked` or `invalidated` through the module's declared failure route before its operation begins.

Do not recursively invoke the same module for the same `{task_id, scope_digest}`. Retry only after changed task state, scope, evidence, or result. A module cannot advance when its output is missing, stale, blocked, reopened, invalidated, outside authority, or bound to another consumer.

## Re-entry

Re-entry reads only the files required by its trigger:

| Trigger | Required read |
|---|---|
| context compaction or resume | `SKILL.md`, `mode-gate.md`, `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| family or stage change | `successor-v0/stage-0-route.md`, destination family |
| delegation or Workflow return | `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| review, fix, or verification result | `successor-v0/stage-0-route.md`, `successor-v0/modules/review.md`, current family, active module files |
| tool or schema failure | `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| invalidation, reopen, changed scope, or changed consumer | `successor-v0/stage-0-route.md`, current family, active module files |
| provider, host, mode, or roadmap-checkbox change | `mode-gate.md`, `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| mutation, approval, release, or roadmap-checkbox transition | current family, active module files, applicable review contract |

`active adapter` means the adapter selected by the current or proposed host; omit it when no adapter applies.

Re-entry validates `{activation, admitted_profile, family, active_modules, contract_versions, task_id, scope, scope_digest, authority, predecessor_result, review_profile, optimized_result_when_required, next_consumer, failure_route}`. Missing or stale values return `devskill_not_revalidated`.

Re-entry is internal. Do not print checkpoints, ledgers, digests, retries, preworkflow results, role outputs, or task synchronization.

## Completion

A module may advance only when its declared versioned output and digest exist, its input and scope bindings are current, its authority is valid, its failure route and consumer are bound, its completion predicate passes, and its next transition is valid. A blocked, reopened, invalidated, or failed result never advances as complete.

Every retry, resume, split, or recovery uses a finite budget and must change task state or a bound scope/result; otherwise return a typed hold, block, split, or reopen result.

## Review

| Condition | `review_kind` |
|---|---|
| Design grilling | `grill` |
| Design decision or runtime/instructional implementation slice | `implementation_logic` |
| Roadmap checklist closure or merge-PR review | `checklist_close` |

`deep-decision-review` owns grilling and design-decision review. `successor-v0/modules/review.md` owns implementation-slice review, checklist-close review, axes, verdicts, assignment aggregation, and reviewer authority.

Persistent session review activates only from an exact user review phrase or a deterministic review offer followed by affirmative acceptance. Model inference never activates persistence; an unanswered offer remains `activation_pending`.

## Parallel modes

Both parallel modes require usable subagents on the dispatching host. A delegated task consumes the matching parent-admitted profile; its successful dispatch proves parent subagent capability, and it does not require or test nested-subagent capability. `parallel-normal` may delegate bounded reader, writer/implementer, reviewer, verifier, and integrator tasks without optimized preworkflow or repeated context-optimization rounds. Each task has one outcome, exact scope, finite budget, output contract, and consumer; dependency-independent write-disjoint tasks may run together and same-artifact writers serialize. `parallel-optimized` also follows the optimized-task contract below.

| Parallel role | Authority ceiling |
|---|---|
| Reader | read-only evidence and bounded task decomposition |
| Writer/implementer | one candidate inside the active family authority and admitted write set |
| Reviewer | findings only against one frozen request |
| Verifier | deterministic validation evidence only |
| Integrator | reconcile compatible reviewed outputs without new meaning, authority, approval, or checklist closure |

Subagent lifecycle: create a fresh subagent for each bounded job; wait for its final result and persist or classify it; close the completed handle before ending the job. Close a prior handle only when creation of the new job fails, then retry once; if creation still fails, switch to a sequential mode and do not continue parallel dispatch. Never reuse a failed handle, and never replace a required parallel job with inline work.

## Optimized modes

An optimized task is the smallest independently dispatchable unit with one objective, bounded input scope, `task_id`, output contract, and next consumer.

| Family | Optimized task unit |
|---|---|
| Route | one Route operation or separately dispatched overlay |
| Design | one grill ticket group or separately dispatched investigation |
| Plan | one architecture, specification, ticket-set, or held-plan revalidation unit |
| Review | one frozen review request containing all required axes |
| Work | one admitted implementation slice |
| Release | one admitted obligation or separately scheduled reconciliation unit |
| Recovery | one replacement or resumed unit with changed scope or `task_id` |

Both optimized modes load `successor-v0/modules/optimized-preworkflow.md` and run its four-role preworkflow once after task identity and minimum admission bindings exist and before the task's first task-specific operation. This applies to every task in every family, including Route, Design, Plan, Review, Work, Release, recovery, and separately dispatched overlays. Internal modules, review axes, validation, retries, and fixes reuse the result. A change to `{host, mode, family, task_id, scope_digest, next_consumer}` invalidates reuse.

After compaction or resume, reuse the exact durable result only when it is present, current, complete, and bound to that scope; otherwise rerun the preworkflow before task-specific work.

`parallel-optimized` uses bounded Reader/Task Breaker, Planner, Deep Reviewer, and Plan Applier subagents.

`sequential-optimized` runs the same four roles sequentially through planning tools and a durable handoff. It does not use the parallel subagent/schema retry path.

## User-visible boundary

Show activation, admission, focused questions, approvals, blockers, required corrections, explicit profile corrections, and concise stage or terminal results. Keep runtime checkpoints internal.

## Rebon boundary

When Rebon is the host, load `successor-v0/modules/rebon-host-adapter.md`. That adapter solely owns Rebon tools, task state, named-agent binding, structured output, and host recovery. Non-Rebon hosts do not use Rebon bindings.

