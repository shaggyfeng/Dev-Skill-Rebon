# Work

## Admission

| Field | Value |
|---|---|
| `family_id` | `work` |
| `stage` | `3` |
| `input` | approved Plan handoff and ticket set preserving Route artifact-touch classification, convention-packet digest, maintenance-map digest when `brownfield`, current repository snapshot, and execution profile |
| `output` | one Work terminal or exact Release handoff |
| `authority` | admitted implementation scope and bounded integration only |
| `consumer` | Plan recovery, next Work slice, Release, or clarification |

No implementation-slice Work operation starts without an admitted handoff, exact slice scope, write set, current read plan or explicit `not_applicable` read binding, verification, recovery route, finite `retry_budget`, and next consumer. The no-work terminal compiler is admitted only from validated entry, graph, frontier, snapshot, and closed-or-excluded-node evidence.

## Slice contract

One Work task is one independently dispatchable implementation slice. It binds `slice_id`, objective, inputs, write set, dependencies, `review_surface`, declared `artifact_kinds`, `family_context`, authority sources, exact frozen subject, current `runtime.read-plan.v0.1`, coverage accumulator, remaining read frontier when applicable, finite `retry_budget`, verification, recovery, and consumer.

## Slice transition representation

The targeted Work relation is dependency-ordered and one-slice-at-a-time:

```text
pre_dispatch_revalidation
 -> executing | diagnosis_requested | admission_revocation | deferred | work_blocked
diagnosis_requested
 -> diagnosis_result
 diagnosis_result
 -> diagnosis_disposition
 diagnosis_disposition
 -> executing | successor_scope | plan_reopened | design_reopened | work_blocked
executing
 -> candidate | interrupted | work_invalidated
candidate
 -> logic_review
logic_review
 -> close_proposal | fix_required | work_blocked
close_proposal
 -> verification | research_requested | work_blocked
verification
 -> diagnosis_requested | slice_closure_proposal | work_invalidated | work_blocked
slice_closure_proposal
 -> integration_proposal | work_blocked
integration_proposal
 -> integrated | work_invalidated | work_blocked
integrated
 -> checklist_close_readiness | continuation
continuation
 -> frontier_ready | terminal_proposal
```

| Gate | Current evidence | Allowed result and consumer |
|---|---|---|
| diagnosis disposition | current diagnosis, objective, lease, write set, verification seam, dependency/consumer bindings, and finite retry budget | exactly one of `resume_current_slice`, `successor_recovery_scope`, `plan_reopen`, or `design_reopen`; Work execution, successor scope, Plan, or Design |
| execution | admitted slice, lease, write set, current snapshot, and read-plan coverage | one `work.candidate.v0.1` to the immediate review barrier |
| implementation review | frozen candidate and declaration-complete `review.request.v0.1` | clean result to close proposal, findings to bounded fix, or typed block |
| verification | clean close proposal, successor snapshot, current read plan, and bounded verification coverage | diagnosis disposition, slice-closure proposal, invalidation, or typed block |
| integration | verified slice closure and admitted integration write set | integrated successor snapshot to checklist readiness or continuation |
| continuation | latest graph/frontier and integration evidence | next frontier or terminal proposal |

All later relation groups wait for the preceding gate; a diagnosis result never bypasses its disposition compiler, and a stale or invalidated result cannot advance.

## Decision table

| Condition | Module | Result |
|---|---|---|
| admitted task in either optimized mode | `optimized.preworkflow` | current applied plan before task-specific work |
| admitted task in `parallel-normal` | family-local bounded dispatch | reader, writer/implementer, reviewer, verifier, or integrator output without optimized preworkflow |
| valid Plan handoff | `work.entry.validate` | Work entry |
| initial graph has no eligible slice and every node is already closed or excluded | `work.complete-state.compile` then `work.terminal` | `work_complete` |
| exact slice can start | `work.admission` | admitted slice |
| admitted slice before execution or changed environment/source/authority | `work.admission.revalidate` | current admission or revocation |
| environment or accepted source changed | `work.admission.revalidate` | current admission or invalidation |
| lease or authority invalid | `work.admission-revocation` | revoked slice |
| applicable symptom, failed verification, regression, performance failure, or unexplained runtime behavior | `work.diagnose` | diagnosis result |
| completed `work.diagnosis-result.v0.1` | `work.diagnosis-disposition.compile` | one typed diagnosis disposition |
| active merge or rebase state | `work.merge-recovery` | conflict-state admission and intent-preserving recovery |
| bounded evidence required before implementation or verification | `research` | sourced evidence packet |
| admitted slice | `work.execution` | candidate result |
| interruption is not a semantic invalidation | `work.uninterrupted-run` | resume same slice |
| implementation result exists | `work.review` | implementation review findings |
| finding requires change | `work.fix` | bounded successor candidate |
| candidate passes review and has a declared verification scope | `work.close-proposal.compile` then `work.verify` | verification result |
| verification passes and the admitted integration set is declared | `work.slice-closure.compile` then `work.integration` | integrated slice |
| integrated scope has roadmap-checkbox or merge-PR intent | `work.checklist-close-readiness.compile` | continuation while incomplete or frozen checklist-close request when ready |
| checklist-close request is ready | `work.review` | `done`, `not_done`, or `blocked` after partitioned complete-surface review |
| accepted source invalidates slice | `work.invalidation` | affected successor scope |
| next slice is eligible | `work.continuation` | next frontier |
| phase, context, session, harness, directory, or role boundary requires transfer | `handoff` | boundary result or handoff |
| integrated slice satisfies the terminal predicate | `work.complete-state.compile` then `work.terminal` | Release handoff |

## Operation

1. Validate the Plan handoff and repository snapshot.
2. Before emitting a graph or selecting a slice, inspect whether Git reports an active merge or rebase. If it does, emit `merge_recovery_requested`, validate the recovery request, and run `work.merge-recovery`; do not select or mutate an implementation slice. If an explicit recovery request is present but Git reports no active state, return `work_blocked` with reason `merge_recovery_blocked`. When no active merge/rebase state and no recovery request exist, continue ordinary Work graph and slice selection.
3. Emit the initial `work.graph.v0.1` and `work.frontier.v0.1`. If the initial graph has no eligible slice, compile `work.complete-state.v0.1` only when every node is already closed or excluded and no checklist intent remains; when roadmap-checkbox or merge-PR intent applies, route the proved closed scope through `work.checklist-close-readiness.compile` and `checklist_close` before terminal compilation. Otherwise select one eligible slice. In either optimized mode, load `successor-v0/modules/optimized-preworkflow.md` and complete it for every admitted Work task before that task's first task-specific operation. Reuse is valid only for unchanged `{host, mode, family, task_id, scope_digest, next_consumer}`.
4. Admit the exact write set and lease/authority binding, together with the current read-plan, coverage, frontier, and finite `retry_budget` bindings.
5. Run `work.admission.revalidate` before execution; revoke stale authority or emit a typed deferral and successor frontier for temporary ineligibility.
6. When bounded evidence is required before implementation or verification, first compile or validate a current `runtime.read-plan.v0.1` for the exact research source boundary or verification scope, partition oversized or uncertain scope into admitted coverage units, and bind the remaining frontier, budgets, consumer, coverage accumulator, and `retry_budget`.
   Only then invoke `research` with producer `Work`, the exact consuming slice or verification scope, and the current mutable seams. Consume the current evidence packet or typed no-match result; a `research_invalidated` result emits a successor scope and cannot be consumed as current.
7. Before invoking `work.execution`, `work.diagnose`, or any other reachable recovery reader, compile or validate a current `runtime.read-plan.v0.1`, partition oversized or uncertain scope into admitted coverage units, and bind the remaining frontier, budgets, consumer, coverage accumulator, and `retry_budget`. In `parallel-normal`, dispatch only bounded evidence units followed by read-free synthesis; in `sequential-normal`, persist each fragment and `runtime.handoff.v0.1` before the next unit. For `work.execution`, then execute exactly one slice or resume its uninterrupted run and persist the candidate, successor repository snapshot, and exact review-surface manifest; for `work.diagnose` or another recovery reader, pass the bound packet to that consumer and await its typed result. When a current diagnosis result returns, invoke `work.diagnosis-disposition.compile` as its sole Work consumer before continuation. Require `accepted_meaning_unchanged`, `current_objective_preserved`, `fix_scope_within_admitted_write_sets`, `regression_seam_within_verification_scope`, `dependencies_and_consumer_unchanged`, and `lease_revalidation_possible`; route by broadest affected authority first: diagnosis failure to `work_blocked`, Design reopen to `design_reopened`, Plan reopen to `plan_reopened`, successor recovery scope to `successor_scope`, and same-slice recovery to `executing`; ambiguity returns `clarification_required`. For an affected candidate, use existing `work.invalidation`; pre-execution recovery omits candidate invalidation when no candidate exists, while verification-origin recovery invalidates the failed closure proposal, verification result, and dependent closure state before bounded correction, review, and verification repeat.
8. Enter the slice review barrier immediately. Invoke `successor-v0/modules/review.md` with `implementation_logic` for that `slice_id`; pass `review_surface`, `artifact_kinds`, `family_context`, authority source IDs, the frozen subject, and the derived conditional code-check assignment when applicable. Do not admit, execute, or accumulate another slice candidate while this barrier is open.
9. When findings exist, transition to `fix_required` and invoke `work.fix`. Before reading any finding-linked candidate or artifact, compile or validate a current `runtime.read-plan.v0.1`, partition oversized or uncertain scope into admitted coverage units, and bind the remaining frontier, budgets, coverage accumulator, consumer, and `retry_budget`. Apply only the exact bounded fix. Before reading back or digesting changed artifacts, compile or validate a current post-mutation `runtime.read-plan.v0.1`; preserve changed-artifact digests and coverage provenance, decrement and persist `retry_budget` on the successor, then return to `logic_review`. A review-complete result with findings cannot advance to close proposal.
10. Compile `work.close-proposal.v0.1` from the candidate, clean review result, declared verification scope, successor repository snapshot, and consumer. Before verification reads files, logs, or linked evidence, compile `runtime.read-plan.v0.1`; partition oversized verification surfaces into bounded evidence units and one read-free verification synthesis. Invoke `work.verify` only under that plan, and require its output to preserve the current read-plan digest and coverage-accumulator digest.
11. Compile `work.slice-closure-set.v0.1` from the verified candidate, verification result with a read-plan digest matching the current plan and coverage accumulator, admitted integration set, successor repository snapshot, and consumer; validate that provenance before invoking `work.integration`.
12. After integration, when a roadmap checkbox update or merge-PR review is requested, compile `work.checklist-close-readiness.v0.1` for the exact roadmap item or PR surface, including `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, consumer, and the required checklist reviewer assignments. If required slices or checklist-recovery findings remain open, keep the checkbox or PR closure unchanged and continue to the next slice. When the complete required surface and every recovery slice are integrated, invoke `successor-v0/modules/review.md` with `checklist_close`; compile its deterministic read plan and partitions before reviewer reading.
13. For checklist `not_done`, map every finding into dependency-ordered bounded recovery slices, carry and decrement each slice's finite `retry_budget`, and run each through implementation review, verification, and integration before rebuilding readiness and the complete checklist read plan. Route `blocked` or exhausted retry budget to a typed hold. Only `done` may authorize the exact checkbox update or merge-PR closure transition.
14. Invoke `work.continuation` after integration when checklist review is inapplicable, not yet ready, or `done`; when its terminal predicate holds, compile `work.complete-state.v0.1` from integration, the successor frontier, the post-integration snapshot, applicable checklist result, and frozen terminal consumer; invoke `work.terminal`.
15. Re-enter Stage 0 and Work before selecting another slice or returning a consumer.

## Merge and rebase recovery

`work.merge-recovery-request.v0.1` binds the active Work task, current merge or rebase state, conflicting files and hunks, accepted intent from both sides, merge goal, history evidence, current `runtime.read-plan.v0.1`, coverage accumulator, remaining frontier, read budgets, finite `retry_budget`, verification scope, explicit abort decision, producer, and consumer. It is admitted only while Git reports an active merge or rebase; an ordinary textual conflict without active state is not enough.

### Operation

1. Before reading history, conflicting files, or hunks, compile or validate the current `runtime.read-plan.v0.1`, partition the conflict evidence into bounded coverage units, and bind the remaining frontier, budgets, and `retry_budget`. Inspect the current merge/rebase state and every admitted conflicting unit before editing.
2. Recover each side's original intent from commit messages and available primary task, issue, or review records. Missing intent blocks resolution rather than inviting invention.
3. Resolve each hunk by preserving both accepted intents where compatible and adding no new behavior. When meanings conflict, reopen Plan or Design with the conflict evidence; Work never chooses new meaning.
4. Before reading resolved files or running checks after mutation, compile or validate a current post-resolution read plan and successor frontier; invalidate the prior plan when digests, scope, budgets, or consumer changed. Run the declared project checks and repair regressions introduced by the resolution only while `retry_budget > 0`.
5. Continue the normal merge or rebase process, including remaining commits when rebasing. Abort only when an explicit recovery decision authorizes abort; otherwise preserve the active state and return a bounded recovery result. Every retry decrements and persists `retry_budget`; zero returns `work_blocked` with reason `retry_budget_exhausted`.
6. Complete only when the terminal evidence proves Git no longer reports an active conflict state and the successor snapshot, read-plan/coverage provenance, verification, trade-off record, remaining `retry_budget`, and next consumer are current.

The result binds `intent_a`, `intent_b`, `resolution_class` (`compatible`, `semantic_reopen`, or `explicit_abort`), changed hunks, successor read-plan and coverage digests, remaining `retry_budget`, verification evidence, `git_conflict_state=false` for the successful terminal, and the next consumer. The operation records a trade-off for incompatible hunks without deciding the meaning inside Work.

## Parallel-normal dispatch

`parallel-normal` does not load or run optimized preworkflow. It may dispatch these bounded roles when usable subagents exist:

| Role | Authority and output |
|---|---|
| Reader | Read-only evidence for one slice, symptom, dependency, or interface surface. |
| Writer/implementer | One candidate inside one admitted lease and exact write set. |
| Reviewer | Findings only against one frozen candidate and review contract. |
| Verifier | Deterministic focused or integration evidence; no implementation mutation. |
| Integrator | Reconcile compatible reviewed and verified candidates inside an admitted integration write set. |

Each task binds one outcome, exact input scope, current read plan when content is read, finite file/byte/line/token/read/tool/output budgets, finite `retry_budget`, versioned output, authority ceiling, failure route, and consumer. Parallel roles may support one admitted slice, but implementation slices traverse execution, review, fix, verification, and integration one slice at a time. Checklist-close review is a separate post-integration barrier over the complete required roadmap or PR surface. One integrator consumes all required outputs. Worker completion status never substitutes for persisted output, review, read-back, or verification.

## State machine

```text
plan_handoff -> work_entry
work_entry -> frontier_ready | work_blocked | clarification_required
frontier_ready -> slice_admitted | no_work_terminal_proposal | merge_recovery_requested | work_blocked | plan_reopened
merge_recovery_requested -> merge_recovery_admitted | work_blocked [reason=merge_recovery_blocked]
merge_recovery_admitted -> merge_recovery_executing | admission_revocation | deferred | work_blocked
merge_recovery_executing -> merge_recovery_verified | semantic_reopen | explicit_abort_recovery | work_invalidated | work_blocked
merge_recovery_verified [git_conflict_state=false] -> continuation
semantic_reopen -> plan_reopened | design_reopened
explicit_abort_recovery -> successor_scope | work_blocked
no_work_terminal_proposal -> checklist_close_readiness | work_complete | work_blocked
slice_admitted -> pre_dispatch_revalidation
pre_dispatch_revalidation -> research_requested | diagnosis_requested | executing | admission_revocation | deferred | work_blocked
research_requested -> research_evidence | research_no_match | work_blocked
diagnosis_requested -> diagnosis_result | work_blocked
diagnosis_result -> diagnosis_disposition
diagnosis_disposition [diagnosis_failure] -> work_blocked
diagnosis_disposition [design_reopen] -> design_reopened
diagnosis_disposition [plan_reopen] -> plan_reopened
diagnosis_disposition [successor_recovery_scope] -> successor_scope
diagnosis_disposition [resume_current_slice] -> executing
research_evidence -> executing | verification | research_invalidated
research_no_match -> executing | verification | successor_scope | work_blocked
research_invalidated -> successor_scope | plan_reopened | design_reopened
deferred -> frontier_ready | work_blocked
admission_revocation -> successor_scope | plan_reopened | design_reopened | work_blocked
executing -> candidate | interrupted | work_invalidated
interrupted -> uninterrupted_run | work_blocked
uninterrupted_run -> executing | work_blocked
candidate -> logic_review
logic_review [review_complete and findings=[]] -> close_proposal
logic_review [review_complete and findings!=[]] -> fix_required
logic_review [review_not_admitted or review_axis_blocked] -> work_blocked
fix_required -> fix | work_blocked
fix -> candidate | work_blocked [reason=retry_budget_exhausted]
retry_budget_exhausted -> work_blocked [reason=retry_budget_exhausted]
close_proposal -> research_requested | verification | work_blocked
verification -> diagnosis_requested | slice_closure_proposal | work_invalidated | work_blocked
slice_closure_proposal -> integration_proposal | work_blocked
integration_proposal -> integrated | work_invalidated | work_blocked
integrated -> checklist_close_readiness | continuation
checklist_close_readiness [required scope incomplete] -> continuation
checklist_close_readiness [required scope integrated] -> checklist_close_proposal
checklist_close_proposal -> checklist_close_review
checklist_close_review [checklist_verdict=done] -> continuation
checklist_close_review [checklist_verdict=not_done] -> successor_scope
checklist_close_review [checklist_verdict=blocked or review_not_admitted or review_axis_blocked] -> work_blocked
continuation -> frontier_ready | terminal_proposal
terminal_proposal -> frontier_ready | work_closed_release_candidate | work_blocked
work_invalidated -> successor_scope | plan_reopened | design_reopened
successor_scope -> frontier_ready | work_blocked
```

## Module registry

The Decision table, Operation, and state transitions own trigger selection. A `family-local` module file value means the Work controller owns the operation. Rebon rows remain host-adapter-owned and require the active Rebon adapter. Every Work registry input and output carries the shared envelope's finite nonnegative `retry_budget`; each retry, reopen, invalidation, or recovery emits a successor packet with the decremented value, and zero returns its declared typed terminal without another dispatch.

| Module | Versioned input | Versioned output | Actor/authority ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | Read, decompose, review, and apply plan corrections only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | Exact current Work or Recovery task |
| `work.entry.validate` | `plan.work-handoff.v0.1` plus `work.repository-snapshot.v0.1` preserving Route artifact-touch, convention, and brownfield maintenance bindings | `work.entry.v0.1` with current snapshot provenance and the admitted slice contract plus initial `work.graph.v0.1`, `work.frontier.v0.1`, `work.invalidation-set.v0.1`, and `work.active-closure-set.v0.1` | Admission binding only | `family-local` | `work_entry_blocked`, Plan recovery, or Design reopen | Work admission or no-work terminal compiler |
| `work.admission` | `work.entry.v0.1` plus current `work.frontier.v0.1` | `work.admission.v0.1` with `{task_id, slice_id, scope_digest, write_set, verification_scope, integration_write_set, consumer, producer}` plus `work.dispatch-lease.v0.1`, `work.selected-set.v0.1`, `work.operation.v0.1`, and `work.candidate-lease-set.v0.1` | Eligibility and exact lease issuance only | `family-local` | `slice_not_admitted` or Plan recovery | Pre-dispatch revalidation |
| `work.admission.revalidate` | `work.admission.v0.1` plus `work.selected-set.v0.1` plus current `work.frontier.v0.1`, `work.repository-snapshot.v0.1`, `work.invalidation-set.v0.1`, `work.operation.v0.1`, and `work.candidate-lease-set.v0.1` | `work.pre-dispatch-revalidation.v0.1` plus current `work.repository-snapshot.v0.1`, `work.unaffected-set-evidence.v0.1`, and exactly one of: current `work.execution-argument.v0.1`; `work.invalidation-event.v0.1` with revocation reason; or `work.deferral.v0.1` plus successor `work.frontier.v0.1` | Mechanical currency and eligibility only | `family-local` | Revoke, typed deferral, invalidation, or block | Admission revocation, Work frontier selector, or execution |
| `work.admission-revocation` | `work.pre-dispatch-revalidation.v0.1` plus `work.invalidation-event.v0.1` plus `work.selected-set.v0.1` plus `work.operation.v0.1` plus `work.candidate-lease-set.v0.1` plus `work.active-closure-set.v0.1` plus `work.unaffected-set-evidence.v0.1` | `work.admission-revocation.v0.1` plus `work.preserved-admission-set.v0.1` | Revocation and independently proven preservation only | `family-local` | `work_blocked` or Plan recovery | Lease issuer, Work recovery, or terminal |
| `work.uninterrupted-run` | `work.uninterrupted-request.v0.1` | `work.uninterrupted-authorization.v0.1` | Human-confirmed continuation only | `family-local` | Cancel, revise, revoke, impossible, or uncovered gate | Work execution or continuation compiler |
| `work.execution` | `work.execution-argument.v0.1` plus current `work.repository-snapshot.v0.1`, current `runtime.read-plan.v0.1`, coverage accumulator, remaining frontier when applicable, and finite `retry_budget` | `work.candidate.v0.1` plus successor `work.repository-snapshot.v0.1` with predecessor and changed-artifact digests, current read-plan and coverage-accumulator digests, remaining frontier when applicable, exact review-surface manifest and frozen `review.request.v0.1` bound to one `slice_id`, with `review_kind=implementation_logic`, `review_context=implementation_slice`, `review_state=one_shot`, `review_surface`, `artifact_kinds`, `family_context`, authority source IDs, producer and consumer, and assignments `reviewer-1=Standards`, `reviewer-2=Specification`, plus `reviewer-3=Correctness` when the code-check predicate matches | Implementer; exact admitted leased write set only | `successor-v0/modules/implementation-and-tdd.md` | `execution_blocked`, `verification_failed`, or upstream reopen | immediate slice review barrier through `work.review` |
| `work.diagnose` | `work.diagnosis-request.v0.1` plus current `runtime.read-plan.v0.1`, coverage accumulator, remaining frontier when sequential, read budgets, and finite `retry_budget` | `work.diagnosis-result.v0.1` with read-plan and coverage provenance, remaining frontier when sequential, and remaining `retry_budget` | Read, reproduce, and bounded temporary instrumentation only | `successor-v0/modules/diagnosing-bugs.md` | `diagnosis_environment_required`, `diagnosis_blocked`, or `retry_budget_exhausted` | `work.execution`, Work recovery, or architecture survey |
| `work.diagnosis-disposition.compile` | `work.diagnosis-result.v0.1` plus current `work.operation.v0.1`, `work.execution-argument.v0.1` or `work.verification.v0.1`, active invalidation/closure/recovery bindings, and finite `retry_budget` | `work.diagnosis-disposition.v0.1` with exactly one of `resume_current_slice`, `successor_recovery_scope`, `plan_reopen`, or `design_reopen`, plus reason and consumer | Work controller; diagnosis disposition selection only | `family-local` | `clarification_required` or `work_blocked` | execution, successor scope, Plan reopen, or Design reopen |
| `work.merge-recovery` | `work.merge-recovery-request.v0.1` plus active `work.entry.v0.1`, current snapshot, conflict evidence, current read-plan/coverage/frontier, and finite `retry_budget` | `work.merge-recovery-result.v0.1` plus successor snapshot, successor read-plan and coverage provenance, remaining `retry_budget`, and `git_conflict_state` terminal evidence | Work controller; accepted-intent recovery only | `family-local` | `work_blocked` with reason `merge_recovery_blocked` or `retry_budget_exhausted`, `semantic_reopen`, or `explicit_abort_recovery` | continuation, Plan/Design reopen, or successor recovery scope |
| `research` | `research.request.v0.1` with current `runtime.read-plan.v0.1`, coverage accumulator, remaining frontier when sequential, read budgets, and finite `retry_budget`, with Work producer and consumer | `research.evidence.v0.1` with read-plan and coverage provenance, remaining frontier when sequential, and remaining `retry_budget` | Researcher; sourced evidence only | `successor-v0/modules/research.md` | `research_blocked`, `research_invalidated`, or `research_no_match` | implementation, verification, recovery, or Handoff |
| `handoff` | `runtime.boundary-input.v0.1` | `runtime.boundary-result.v0.1` or `runtime.handoff.v0.1` | Context transfer only | `successor-v0/modules/handoff-and-boundaries.md` | `handoff_blocked` or `boundary_revalidation_required` | Exact next Work module, role, session, or family |
| `work.close-proposal.compile` | `work.candidate.v0.1` from `work.execution` or `work.fix` plus current clean `review.result.v0.1` from `work.review` with `review_kind=implementation_logic` and `findings=[]` plus `work.admission.v0.1` from `work.admission` plus successor `work.repository-snapshot.v0.1` from `work.execution` or `work.fix` | `work.close-proposal.v0.1` preserving `{candidate_digest, review_digest, scope_digest, verification_scope, write_set, snapshot_digest, producer_lineage, consumer}` and the shared envelope | Closure-input compilation only | `family-local` | `verification_proposal_blocked` | `work.verify` |
| `work.verify` | `work.close-proposal.v0.1` plus its successor `work.repository-snapshot.v0.1`, current `runtime.read-plan.v0.1`, finite `retry_budget`, and bounded verification evidence fragments when partitioned | `work.verification.v0.1` plus explicit `read_plan_digest` and `coverage_accumulator_digest`, and validated successor `work.repository-snapshot.v0.1` preserving `{candidate_digest, scope_digest, verification_scope, observed_result, snapshot_digest, read_plan_digest, coverage_accumulator_digest, producer, consumer, retry_budget}` | Deterministic evidence only | `family-local` | `verification_failed`, incomplete coverage, mechanical fix, or upstream reopen | Fix or `work.slice-closure.compile` |
| `work.review` | `review.request.v0.1` plus the review-owned `runtime.read-plan.v0.1` and assignment coverage sets | `review.result.v0.1` with current read-plan and coverage-accumulator digests | Findings or checklist verdict only | `successor-v0/modules/review.md` | `review_not_admitted`, `review_axis_blocked`, or incomplete coverage | Fix after `implementation_logic`; continuation after `checklist_close=done`; bounded recovery frontier after `checklist_close=not_done` |
| `work.fix` | `work.candidate.v0.1` plus `review.result.v0.1`, current `work.repository-snapshot.v0.1`, current read-plan/coverage/frontier, and finite `retry_budget` | `work.candidate.v0.1` plus `work.fix-lineage.v0.1`, successor `work.repository-snapshot.v0.1` with predecessor and changed-artifact digests, successor read-plan and coverage provenance, remaining `retry_budget`, and successor frozen `review.request.v0.1` | Exact bounded mutation only | `family-local` | `fix_blocked`, `retry_budget_exhausted`, or upstream reopen | Implementation re-review |
| `work.invalidation` | `work.invalidation-event.v0.1` plus current `work.invalidation-set.v0.1`, `work.graph.v0.1`, and `work.active-closure-set.v0.1` | `work.invalidation.v0.1` plus successor `work.invalidation-set.v0.1` and `work.unaffected-set-evidence.v0.1` | Revocation and independently proven preservation only | `family-local` | Plan or Design reopen | Successor scope, recovery, or terminal |
| `work.slice-closure.compile` | `work.candidate.v0.1` from `work.execution` or `work.fix` plus current implementation `review.result.v0.1`, `work.verification.v0.1` with current `read_plan_digest` matching its coverage accumulator, successor `work.repository-snapshot.v0.1` from `work.verify`, and `work.admission.v0.1` including `integration_write_set` and `retry_budget` | `work.slice-closure-set.v0.1` preserving `{candidate_digest, implementation_review_digest, verification_digest, verification_read_plan_digest, scope_digest, integration_write_set, snapshot_digest, producer_lineage, consumer, retry_budget}` and the shared envelope | Integration-input compilation only | `family-local` | `integration_proposal_blocked` or `retry_budget_exhausted` | `work.integration` |
| `work.integration` | `work.slice-closure-set.v0.1` from `work.slice-closure.compile` plus the current successor `work.repository-snapshot.v0.1` from `work.verify` plus current `work.active-closure-set.v0.1` | `work.integration.v0.1` plus successor post-integration `work.repository-snapshot.v0.1` and successor `work.active-closure-set.v0.1`, preserving `{closure_digest, candidate_digest, verification_digest, scope_digest, integration_write_set, snapshot_digest, predecessor_snapshot_digest, producer, consumer}` and the shared envelope | Admitted integration evidence and write set only | `family-local` | `integration_blocked`, conflict, or invalidation | `work.checklist-close-readiness.compile` when checklist intent applies; otherwise continuation |
| `work.checklist-close-readiness.compile` | exact roadmap item or PR surface, current graph/frontier, active closure set, all required integration records, open checklist finding/recovery set, post-integration snapshot, checkbox/merge intent, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, and consumer | `work.checklist-close-readiness.v0.1` with required-scope manifest, integrated/missing slice IDs, open/resolved recovery IDs, evidence pointers, readiness result, and consumer; when ready, frozen `review.request.v0.1` with `review_kind=checklist_close`, the declarations, and required assignments | Mechanical readiness and review-input compilation only | `family-local` | incomplete scope or open recovery continues without mutation; stale or conflicting scope returns `work_blocked` | continuation when incomplete; `work.review` when ready |
| `work.continuation` | current `work.graph.v0.1` plus current `work.frontier.v0.1` from `work.entry` or a prior continuation plus `work.integration.v0.1` | `work.continuation.v0.1` plus successor `work.frontier.v0.1` with frontier, predecessor frontier, and producer lineage | Route inside unchanged authorization only | `family-local` | `work_blocked` or named reopen | Work admission or terminal |
| `work.complete-state.compile` | either `work.integration.v0.1` plus its post-integration snapshot and latest frontier from `work.continuation`, or `work.entry.v0.1` plus its initial graph, frontier, snapshot, and proof that every node is closed or excluded; both variants include `work.frozen-terminal-consumer.v0.1` and, when roadmap-checkbox or merge-PR intent applies, current ready `work.checklist-close-readiness.v0.1` plus `review.result.v0.1` with `checklist_verdict=done` | `work.complete-state.v0.1` preserving `{completion_reason=integrated or no_work_required, integration_digest=not_applicable or current digest, frontier_digest, snapshot_digest, checklist_review_digest=not_applicable or current done result, frozen_terminal_consumer, producer_lineage, consumer}` and the shared envelope | Terminal-input compilation only | `family-local` | `terminal_proposal_blocked` | `work.terminal` |
| `work.terminal` | `work.complete-state.v0.1` | `work.family-packet.v0.1` plus optional `work.release-handoff.v0.1`, preserving the complete-state digest and terminal consumer | Terminal-specific only | `family-local` | `work_incomplete` or named owner | Release, Plan recovery, clarification, or exit |
| `work.attention-sidecar.emit` | `work.family-packet.v0.1` plus `work.frozen-terminal-consumer.v0.1` plus `attention.table-mutation-envelope.v0.1` | `attention.lifecycle-mutation-sidecar.v0.1` | Sidecar binding only; cannot change terminal | `family-local` | `sidecar_blocked` | `stage-0.attention-table-maintenance` |
| `work.rebon-schema-contract` | `rebon.tool-profile-request.v0.1` | `rebon.tool-profile.v0.1` | Host discovery and admission only | `successor-v0/modules/rebon-host-adapter.md` | `tool_profile_not_admitted` | Rebon dispatcher |
| `work.rebon-task-list-contract` | `rebon.task-binding-request.v0.1` | `rebon.task-binding.v0.1` | Host task-ledger synchronization only | `successor-v0/modules/rebon-host-adapter.md` | `task_list_not_admitted` | Current family task |
| `work.rebon-display-adapter` | `rebon.workflow-binding.v0.1` plus persisted native result | `rebon.result-envelope.v0.1` | Host result translation only | `successor-v0/modules/rebon-host-adapter.md` | `adapter_not_admitted` | Current Work consumer |

## Invariants

- A slice cannot write outside its admitted write set.
- A lease does not grant authority beyond the Work handoff.
- An implementation result cannot advance without both logic-review axes and any derived Correctness assignment selected by its validated artifact declaration.
- One slice review barrier spans implementation review, fixes, repeated review, verification, and integration; another slice cannot be admitted or executed until it closes.
- Implementation candidates from different slices cannot share one `implementation_logic` request or one axis result set.
- A roadmap-checkbox update cannot advance without both `checklist_close` reviewer assignments: Standards + Correctness and Specification-compliance + Correctness.
- Verification cannot be replaced by a passing tool status without declared behavior coverage or a current `read_plan_digest` matching its `coverage_accumulator_digest`.
- Integration cannot consume a stale or invalidated candidate, a stale read plan, or a verification result whose `read_plan_digest` does not match the current coverage accumulator.
- A roadmap checkbox or merge-PR closure remains unchanged until post-integration readiness proves its complete required surface integrated and `checklist_close` returns `done` from a declaration-complete request.
- Work terminal compilation rejects pending, incomplete, stale, blocked, or `not_done` checklist closure whenever roadmap-checkbox or merge-PR intent applies.
- Execution, fixes, verification, and integration emit successor repository snapshots; each downstream compiler consumes the newest snapshot and preserves predecessor lineage.
- `work.entry.validate` emits the initial graph and frontier; `work.continuation` emits each successor frontier; terminal compilation consumes the latest frontier only.
- Work admission initializes every selected-set, operation, lease-set, invalidation-set, closure-set, and unaffected-set binding required by pre-dispatch revalidation or revocation.
- The no-work terminal is valid only from entry evidence proving every graph node already closed or excluded.
- Deferral preserves authority and closure state, emits a successor frontier with the deferred slice ineligible, and cannot advance that slice as complete.
- Changed accepted source invalidates affected future work and preserves only proven-unaffected slices.
- Merge/rebase recovery admits only active Git merge or rebase state, carries both accepted intents, and never invents behavior for incompatible meaning.
- A compatible resolution preserves both intents; a semantic conflict reopens Plan or Design; an abort requires an explicit recovery decision.
- Active merge/rebase state is detected before graph selection or mutation; `merge_recovery_blocked` is carried as a reason on the reachable `work_blocked` terminal.
- Research evidence remains separate from implementation authority and cannot authorize a slice, fix, or integration; a same-seam invalidation routes to a successor scope before the dependent transition.
- A completed diagnosis result is consumed only by `work.diagnosis-disposition.compile` and reaches exactly one typed disposition and consumer; it never continues directly to execution or closure.
- Successful merge/rebase recovery proves `git_conflict_state=false` before continuation or terminal closure.
- Work never publishes, deploys, activates, or changes external effects.

- Optimized modes cannot begin any Work task-specific operation without a current `optimized.applied-plan.v0.1`; `parallel-normal` cannot invoke that module.

- Recovery creates a successor scope when task identity, scope, digest, or consumer changes and decrements the finite `retry_budget`; zero returns `work_blocked` with reason `retry_budget_exhausted`.
- Mechanical failure returns the smallest authorized fix, decrements and persists `retry_budget`, and reruns affected review and verification only from a changed frontier.
- Checklist findings on prior integrated scope compile bounded dependency-ordered recovery slices; each slice traverses the ordinary review barrier, and closure review reruns from a fresh complete read plan after recovery integration.
- Semantic, scope, authority, normative, or empirical change reopens Plan or Design.
- Active merge/rebase state missing: return `merge_recovery_blocked` without editing.
- Missing intent or incompatible meaning: preserve both claims and route to Plan or Design reopen; Work cannot decide.
- Verification failure keeps the recovery slice open; explicit abort is the only abort route.
- Diagnosis-disposition ambiguity returns `clarification_required`; a selected recovery route preserves the current authority, consumer, and finite retry binding.

## Terminals

`work_closed_release_candidate`, `work_complete`, `work_blocked`, `plan_reopened`, `design_reopened`, and `clarification_required` are the only Work terminals.
