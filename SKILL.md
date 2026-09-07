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

Run Stage 0 before every engineering task. Evaluate direct overlays first. When no stage is active and no direct overlay is selected, Route intake is exclusive; a family signal is eligible only after the current intake result routes it. Then select one family route or Route terminal:

| Signal | Required route or overlay |
|---|---|
| Design grilling | `deep-decision-review` with `review_context=grill_infrastructure`, `review_kind=grill`: one reviewer with Standards + Specification |
| Runtime/instructional implementation review | `stage-0.review` with `review_kind=implementation_logic`: two reviewers, one Standards and one Specification |
| Roadmap checklist closure | `stage-0.review` with `review_kind=checklist_close`: two reviewers, Standards + Correctness and Specification-compliance + Correctness |
| Merge-PR review | `stage-0.review` with `review_kind=checklist_close`: two reviewers, Standards + Correctness and Specification-compliance + Correctness |
| Explicit unslop or structural-thinking request, or admitted structural-slop finding | `stage-0.think` |
| Explicit diagnosis request with a valid Work entry and bounded symptom | `work.diagnose` |
| Explicit research request or family evidence request | `research` |
| Explicit temporary human-only action request | `manual-procedure` |
| Explicit teaching, learning, practice, or competence-building request | `teaching` |
| Applicable unsuppressed failure signal | `route.attention` |
| Route intake greenfield decision or value gap requiring untangling | `deep-decision-review` with `review_context=intake` |
| Unresolved meaning, scope, trade-off, or human decision | Design |
| Architecture, specification, or ticket work from a Design handoff | Plan |
| Authorized implementation or fix from a Plan handoff | Work |
| Release-candidate preparation from Work | Release |
| No family transition or failure | active-stage continuation or Route terminal |

`successor-v0/stage-0-route.md` composes every applicable overlay and resolves consumer conflicts. A no-active-stage, no-direct-overlay operation always performs Route intake before Design or Plan family routing; a family signal never bypasses the current intake result. Family registries define module triggers, versioned inputs and outputs, authority, runtime file, consumers, and failure routes. Activate a module only when its trigger, prerequisites, current bindings, authority, runtime file, and consumer validate. A family-local controller operation without a module file remains governed by the family contract.

## Runtime modules

Load only admitted module files. Every registry module uses `devskill.module.v0.2`. A doctrine row in the Route Module-loading table is admitted by its row condition alone, carries no envelope, and makes no admission decision. Every versioned module input and output carries `runtime.module-envelope.v0.1`:

`{contract_version, task_id, scope_digest, input_digest, output_digest, producer, consumer, authority, status, failure_route, retry_budget}`

`status` is exactly `complete`, `blocked`, `reopen`, or `invalidated`. Input `output_digest` is null; an output without a current non-null `output_digest` cannot be complete. `failure_route` is null only for `complete`; every other status names the typed route. A module file is authoritative for its repeatable procedure; the family controller remains authoritative for lifecycle order and terminals.

Every retained capability must be executable through its trigger, prerequisites, operation, output, authority, failure route, recovery, and consumer. Research, Manual Procedure, and Teaching are the only new cross-family module files admitted by the P16 decision record; existing owners remain singular.

## Read admission

Before any model, subagent, reviewer, verifier, or current-context pass reads repository or document content, the active family controller or host adapter compiles `runtime.read-plan.v0.1` with the target path or source IDs, deterministic discovery command and result, file count, byte count, line count when text, conservative token estimate, declared semantic coverage units, per-unit file/read/tool/output budgets, partition order, coverage accumulator, exact synthesis consumer, failure route, and remaining retry budget. Evidence workers may report changed size or scope but cannot alter the plan.

Use host-native listing, search, metadata, and count tools such as `rg --files`, targeted `rg -n`, byte metadata, or line counts before content reads. Do not use a model card to discover whether its own target fits. Classify binary, generated, unavailable, and uncountable inputs explicitly; an unmeasurable target is oversized until bounded.

| Result | Required execution |
|---|---|
| Complete target fits every declared budget | Admit one bounded content-read unit. |
| Target exceeds any budget or fit is uncertain | Partition before the first content read by declared file, section, symbol, source/test, roadmap outcome, or other authoritative seam. |
| Parallel mode | Dispatch bounded evidence units; persist each fragment; run planning, review, verification, or synthesis only from the complete coverage set with source reads disabled. |
| Sequential mode | Read one bounded unit, persist its fragment and `runtime.read-frontier.v0.1`, emit `runtime.handoff.v0.1`, re-enter, and continue until coverage is complete; then run a read-free synthesis pass. |

Every read fragment binds the read-plan digest, coverage-unit ID, source digest or freshness identity, observed coverage, omissions, status, remaining `retry_budget`, and next consumer. Synthesis is inadmissible until the coverage accumulator proves every required unit complete or explicitly blocked. A changed target, digest, budget, or consumer invalidates the plan. An oversized failure never retries the same read scope.

## Review topology

| Review surface | Required reviewer assignment |
|---|---|
| Grilling | one reviewer with Standards + Specification |
| Runtime/instructional implementation slice | two reviewers: one Standards and one Specification; add a separate Correctness assignment when validated code-artifact declarations select hard-code checks |
| Roadmap checklist closure or merge-PR review | two reviewers: reviewer 1 Standards + Correctness; reviewer 2 Specification-compliance + Correctness |

In either parallel mode, each assignment owns one independent bounded reviewer result persisted before its declared consumer transition. A fitting surface uses one reviewer subagent. An oversized surface uses assignment-bound evidence cards plus one read-free reviewer synthesis card; every card retains the same assignment ID and axis ceiling. Sequential modes execute the same assignments as bounded current-context passes with handoffs between read units. A missing assignment or incomplete coverage blocks review; no topology may be merged into one reviewer or replaced by inline review in a parallel mode.

Families construct review requests but must declare `review_surface`, artifact kinds, family context, authority sources, and the frozen surface. Review derives fixed assignments and conditional code checks from those bindings; a code-check assignment is additive to the two required implementation axes and cannot be omitted when its declaration predicate matches. No family may remove topology-mandated axes.

Implementation review is a per-slice barrier. One `implementation_logic` request contains exactly one admitted `slice_id`; its two reviewers, fixes, repeated review, verification, and integration resolve before another Work slice is admitted. Candidates from multiple slices are never accumulated into one implementation review.

Checklist-close and merge-PR review may span multiple integrated slices only through a current `runtime.read-plan.v0.1`. Oversized closure surfaces partition into bounded evidence units before reviewer dispatch; each required assignment consumes a complete persisted coverage set and emits one read-free axis result before aggregation.


Module prerequisites are the matched trigger, complete current versioned input and envelope, admitted authority, available runtime file, and valid consumer. Missing or stale prerequisites return `blocked` or `invalidated` through the module's declared failure route before its operation begins.

Do not recursively invoke the same module for the same `{task_id, scope_digest}`. Retry only after changed task state, scope, evidence, or result and while the bound finite `retry_budget` remains above zero. A module cannot advance when its output is missing, stale, blocked, reopened, invalidated, outside authority, or bound to another consumer.

## Re-entry

Re-entry reads only the files required by its trigger:

| Trigger | Required read |
|---|---|
| context compaction or resume | `SKILL.md`, `mode-gate.md`, `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| family or stage change | `successor-v0/stage-0-route.md`, destination family |
| delegation or Workflow return | `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| review, fix, or verification result | `successor-v0/stage-0-route.md`, `successor-v0/modules/review.md`, current family, active module files |
| invalidation or reopen | `successor-v0/stage-0-route.md`, current family, affected active module files, predecessor result and authority sources, active read plan, and successor packet or scope |
| scope or consumer change | `successor-v0/stage-0-route.md`, source and destination family, affected active module files, current predecessor result, and destination consumer contract |
| tool or schema failure | `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| direct Research request | `SKILL.md`, `mode-gate.md`, `successor-v0/stage-0-route.md`, `successor-v0/modules/research.md`, current family, active adapter |
| direct Manual Procedure request | `SKILL.md`, `mode-gate.md`, `successor-v0/stage-0-route.md`, `successor-v0/modules/manual-procedure.md`, current family, active adapter |
| direct Teaching request | `SKILL.md`, `mode-gate.md`, `successor-v0/stage-0-route.md`, `successor-v0/modules/teaching.md`, current family, active adapter |
| provider, host, mode, or roadmap-checkbox change | `mode-gate.md`, `successor-v0/stage-0-route.md`, current family, active module files, active adapter |
| mutation, approval, release, or roadmap-checkbox transition | current family, active module files, applicable review contract |

`active adapter` means the adapter selected by the current or proposed host; omit it when no adapter applies.

Every invalidation, reopen, scope change, consumer change, or recovery retry binds a finite nonnegative integer `retry_budget`. A retry is admitted only after changed task state, scope, evidence, or result; it decrements the budget before dispatch and persists the remaining value in every successor envelope and handoff. Zero remaining units return the module's typed blocked, clarification, hold, or reopen terminal; no unchanged retry or unbounded recovery loop is valid.

Re-entry validates `{activation, admitted_profile, family, active_modules, contract_versions, task_id, scope, scope_digest, authority, active_read_plan, coverage_accumulator, remaining_read_frontier, predecessor_result, review_profile, optimized_result_when_required, retry_budget, next_consumer, failure_route}`. Read fields are `not_applicable` only when the current operation consumes no repository or document content. Missing or stale values return `devskill_not_revalidated`.

Re-entry is internal. Do not print checkpoints, ledgers, digests, retries, preworkflow results, role outputs, or task synchronization.

## Completion

A module may advance only when its declared versioned output and digest exist, its input and scope bindings are current, its authority is valid, its failure route and consumer are bound, every applicable read-plan coverage unit is complete or explicitly blocked, its completion predicate passes, and its next transition is valid. A blocked, reopened, invalidated, failed, or partial-coverage result never advances as complete.

The clean package is runtime-only: reports, schemas, indexes, catalogs, scaffolds, placeholders, test harnesses, evidence records, and project-specific controllers cannot satisfy a required capability or enter the package as a substitute for runtime instruction logic.

## Review

| Condition | `review_kind` |
|---|---|
| Design grilling | `grill` |
| Design decision or runtime/instructional implementation slice | `implementation_logic` |
| Roadmap checklist closure or merge-PR review | `checklist_close` |

`deep-decision-review` owns grilling and design-decision review. `successor-v0/modules/review.md` owns implementation-slice review, checklist-close review, axes, verdicts, assignment aggregation, and reviewer authority.

Persistent session review activates only from an exact user review phrase or a deterministic review offer followed by affirmative acceptance. Model inference never activates persistence; an unanswered offer remains `activation_pending`.

## Parallel modes

Both parallel modes require usable subagents on the dispatching host. A delegated task consumes the matching parent-admitted profile; its successful dispatch proves parent subagent capability, and it does not require or test nested-subagent capability. `parallel-normal` may delegate bounded reader, writer/implementer, reviewer, verifier, and integrator tasks without optimized preworkflow or repeated context-optimization rounds. Each task has one outcome, exact scope, finite budget, output contract, and consumer; dependency-independent write-disjoint tasks may run together and same-artifact writers serialize, except that Work implementation slices remain serialized by the per-slice review barrier. `parallel-optimized` also follows the optimized-task contract below.

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
