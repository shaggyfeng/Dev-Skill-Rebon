# Work

## Admission

| Field | Value |
|---|---|
| `family_id` | `work` |
| `stage` | `3` |
| `input` | approved Plan handoff, ticket set, current repository snapshot, execution profile |
| `output` | one Work terminal or exact Release handoff |
| `authority` | admitted implementation scope and bounded integration only |
| `consumer` | Plan recovery, next Work slice, Release, or clarification |

No implementation-slice Work operation starts without an admitted handoff, exact slice scope, write set, verification, recovery route, and next consumer. The no-work terminal compiler is admitted only from validated entry, graph, frontier, snapshot, and closed-or-excluded-node evidence.

## Slice contract

One Work task is one independently dispatchable implementation slice. It binds `slice_id`, objective, inputs, write set, dependencies, review surface, verification, recovery, and consumer.

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
| admitted slice | `work.execution` | candidate result |
| interruption is not a semantic invalidation | `work.uninterrupted-run` | resume same slice |
| implementation result exists | `work.review` | implementation review findings |
| finding requires change | `work.fix` | bounded successor candidate |
| candidate passes review and has a declared verification scope | `work.close-proposal.compile` then `work.verify` | verification result |
| verification passes and the admitted integration set is declared | `work.slice-closure.compile` then `work.integration` | integrated slice |
| accepted source invalidates slice | `work.invalidation` | affected successor scope |
| next slice is eligible | `work.continuation` | next frontier |
| phase, context, session, harness, directory, or role boundary requires transfer | `handoff` | boundary result or handoff |
| integrated slice satisfies the terminal predicate | `work.complete-state.compile` then `work.terminal` | Release handoff |

## Operation

1. Validate the Plan handoff and repository snapshot; emit the initial `work.graph.v0.1` and `work.frontier.v0.1`.
2. If the initial graph has no eligible slice, compile `work.complete-state.v0.1` only when every node is already closed or excluded; otherwise select one eligible slice. In either optimized mode, load `successor-v0/modules/optimized-preworkflow.md` and complete it for every admitted Work task before that task's first task-specific operation. Reuse is valid only for unchanged `{host, mode, family, task_id, scope_digest, next_consumer}`.
3. Admit the exact write set and lease/authority binding.
4. Run `work.admission.revalidate` before execution; revoke stale authority or emit a typed deferral and successor frontier for temporary ineligibility.
5. Execute the slice or resume its uninterrupted run; persist the candidate and successor repository snapshot.
6. Invoke `successor-v0/modules/review.md` with `implementation_logic`.
7. Apply bounded fixes and rerun the required review when findings exist.
8. Compile `work.close-proposal.v0.1` from the candidate, review result, declared verification scope, successor repository snapshot, and consumer; invoke `work.verify`.
9. If a roadmap checkbox is being changed or a merge-PR review is requested, invoke `successor-v0/modules/review.md` with `checklist_close` before integration or PR closure. Route `not_done` to Fix and `blocked` to a typed hold; only `done` may continue.
10. Compile `work.slice-closure-set.v0.1` from the verified candidate, verification result, admitted integration set, successor repository snapshot, and consumer; when a roadmap checkbox is being changed or a merge-PR review is requested, include the current `checklist_close` result; invoke `work.integration`.
11. Invoke `work.continuation` after every integration to emit the successor frontier; when its terminal predicate holds, compile `work.complete-state.v0.1` from integration, the successor frontier, the post-integration snapshot, and the frozen terminal consumer; invoke `work.terminal`.
12. Re-enter Stage 0 and Work before selecting another slice or returning a consumer.

## Parallel-normal dispatch

`parallel-normal` does not load or run optimized preworkflow. It may dispatch these bounded roles when usable subagents exist:

| Role | Authority and output |
|---|---|
| Reader | Read-only evidence for one slice, symptom, dependency, or interface surface. |
| Writer/implementer | One candidate inside one admitted lease and exact write set. |
| Reviewer | Findings only against one frozen candidate and review contract. |
| Verifier | Deterministic focused or integration evidence; no implementation mutation. |
| Integrator | Reconcile compatible reviewed and verified candidates inside an admitted integration write set. |

Each task binds one outcome, exact input scope, finite read/tool/output budget, versioned output, authority ceiling, failure route, and consumer. Dependency-independent write-disjoint tasks may run together. Same-artifact, same-public-seam, and dependent writers serialize. One integrator consumes all required outputs. Worker completion status never substitutes for persisted output, review, read-back, or verification.

## State machine

```text
plan_handoff -> work_entry
work_entry -> frontier_ready | work_blocked | clarification_required
frontier_ready -> slice_admitted | no_work_terminal_proposal | work_blocked | plan_reopened
no_work_terminal_proposal -> work_complete | work_blocked
slice_admitted -> pre_dispatch_revalidation
pre_dispatch_revalidation -> executing | admission_revocation | deferred | work_blocked
deferred -> frontier_ready | work_blocked
admission_revocation -> successor_scope | plan_reopened | design_reopened | work_blocked
executing -> candidate | interrupted | work_invalidated
interrupted -> uninterrupted_run | work_blocked
uninterrupted_run -> executing | work_blocked
candidate -> logic_review
logic_review [review_complete] -> fix_required | close_proposal
logic_review [review_not_admitted or review_axis_blocked] -> work_blocked
fix_required -> candidate | work_blocked
close_proposal -> verification | work_blocked
verification -> checklist_close_proposal | slice_closure_proposal | work_invalidated | work_blocked
checklist_close_proposal -> checklist_close_review
checklist_close_review [checklist_verdict=done] -> slice_closure_proposal
checklist_close_review [checklist_verdict=not_done] -> fix_required
checklist_close_review [checklist_verdict=blocked or review_not_admitted or review_axis_blocked] -> work_blocked
slice_closure_proposal -> integration_proposal | work_blocked
integration_proposal -> integrated | work_invalidated | work_blocked
integrated -> continuation
continuation -> frontier_ready | terminal_proposal
terminal_proposal -> frontier_ready | work_closed_release_candidate | work_blocked
work_invalidated -> successor_scope | plan_reopened | design_reopened
successor_scope -> frontier_ready | work_blocked
```

## Module registry

The Decision table, Operation, and state transitions own trigger selection. A `family-local` module file value means the Work controller owns the operation. Rebon rows remain host-adapter-owned and require the active Rebon adapter.

| Module | Versioned input | Versioned output | Actor/authority ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | Read, decompose, review, and apply plan corrections only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | Exact current Work or Recovery task |
| `work.entry.validate` | `plan.work-handoff.v0.1` plus `work.repository-snapshot.v0.1` | `work.entry.v0.1` with current snapshot provenance and the admitted slice contract plus initial `work.graph.v0.1`, `work.frontier.v0.1`, `work.invalidation-set.v0.1`, and `work.active-closure-set.v0.1` | Admission binding only | `family-local` | `work_entry_blocked`, Plan recovery, or Design reopen | Work admission or no-work terminal compiler |
| `work.admission` | `work.entry.v0.1` plus current `work.frontier.v0.1` | `work.admission.v0.1` with `{task_id, slice_id, scope_digest, write_set, verification_scope, integration_write_set, consumer, producer}` plus `work.dispatch-lease.v0.1`, `work.selected-set.v0.1`, `work.operation.v0.1`, and `work.candidate-lease-set.v0.1` | Eligibility and exact lease issuance only | `family-local` | `slice_not_admitted` or Plan recovery | Pre-dispatch revalidation |
| `work.admission.revalidate` | `work.admission.v0.1` plus `work.selected-set.v0.1` plus current `work.frontier.v0.1`, `work.repository-snapshot.v0.1`, `work.invalidation-set.v0.1`, `work.operation.v0.1`, and `work.candidate-lease-set.v0.1` | `work.pre-dispatch-revalidation.v0.1` plus current `work.repository-snapshot.v0.1`, `work.unaffected-set-evidence.v0.1`, and exactly one of: current `work.execution-argument.v0.1`; `work.invalidation-event.v0.1` with revocation reason; or `work.deferral.v0.1` plus successor `work.frontier.v0.1` | Mechanical currency and eligibility only | `family-local` | Revoke, typed deferral, invalidation, or block | Admission revocation, Work frontier selector, or execution |
| `work.admission-revocation` | `work.pre-dispatch-revalidation.v0.1` plus `work.invalidation-event.v0.1` plus `work.selected-set.v0.1` plus `work.operation.v0.1` plus `work.candidate-lease-set.v0.1` plus `work.active-closure-set.v0.1` plus `work.unaffected-set-evidence.v0.1` | `work.admission-revocation.v0.1` plus `work.preserved-admission-set.v0.1` | Revocation and independently proven preservation only | `family-local` | `work_blocked` or Plan recovery | Lease issuer, Work recovery, or terminal |
| `work.uninterrupted-run` | `work.uninterrupted-request.v0.1` | `work.uninterrupted-authorization.v0.1` | Human-confirmed continuation only | `family-local` | Cancel, revise, revoke, impossible, or uncovered gate | Work execution or continuation compiler |
| `work.execution` | `work.execution-argument.v0.1` plus current `work.repository-snapshot.v0.1` | `work.candidate.v0.1` plus successor `work.repository-snapshot.v0.1` with predecessor and changed-artifact digests plus frozen `review.request.v0.1` with `review_kind=implementation_logic`, `review_context=implementation_slice`, `review_state=one_shot`, and assignments `reviewer-1=Standards`, `reviewer-2=Specification` | Implementer; exact admitted leased write set only | `successor-v0/modules/implementation-and-tdd.md` | `execution_blocked`, `verification_failed`, or upstream reopen | `work.review` with `implementation_logic` |
| `work.diagnose` | `work.diagnosis-request.v0.1` | `work.diagnosis-result.v0.1` | Read, reproduce, and bounded temporary instrumentation only | `successor-v0/modules/diagnosing-bugs.md` | `diagnosis_environment_required` or `diagnosis_blocked` | `work.execution`, Work recovery, or architecture survey |
| `handoff` | `runtime.boundary-input.v0.1` | `runtime.boundary-result.v0.1` or `runtime.handoff.v0.1` | Context transfer only | `successor-v0/modules/handoff-and-boundaries.md` | `handoff_blocked` or `boundary_revalidation_required` | Exact next Work module, role, session, or family |
| `work.close-proposal.compile` | `work.candidate.v0.1` from `work.execution` or `work.fix` plus `review.result.v0.1` from `work.review` plus `work.admission.v0.1` from `work.admission` plus successor `work.repository-snapshot.v0.1` from `work.execution` or `work.fix` | `work.close-proposal.v0.1` preserving `{candidate_digest, review_digest, scope_digest, verification_scope, write_set, snapshot_digest, producer_lineage, consumer}` and the shared envelope | Closure-input compilation only | `family-local` | `verification_proposal_blocked` | `work.verify` |
| `work.verify` | `work.close-proposal.v0.1` plus its successor `work.repository-snapshot.v0.1` | `work.verification.v0.1` plus validated successor `work.repository-snapshot.v0.1` preserving `{candidate_digest, scope_digest, verification_scope, observed_result, snapshot_digest, producer, consumer}` and, when `roadmap_checkbox_update=yes` or `merge_pr_review=yes`, frozen `review.request.v0.1` with `review_kind=checklist_close`, `review_context=checklist_close`, the exact roadmap item when applicable, verified candidate/snapshot digests, and reviewer assignments `reviewer-1=Standards + Correctness` and `reviewer-2=Specification-compliance + Correctness` | Deterministic evidence only | `family-local` | `verification_failed`, mechanical fix, or upstream reopen | Fix; checklist-close review when `roadmap_checkbox_update=yes` or `merge_pr_review=yes`; otherwise `work.slice-closure.compile` |
| `work.review` | `review.request.v0.1` | `review.result.v0.1` | Findings or checklist verdict only | `successor-v0/modules/review.md` | `review_not_admitted` or `review_axis_blocked` | Fix; `work.close-proposal.compile` after `implementation_logic`; `work.slice-closure.compile` after `checklist_close` |
| `work.fix` | `work.candidate.v0.1` plus `review.result.v0.1` plus current `work.repository-snapshot.v0.1` | `work.candidate.v0.1` plus `work.fix-lineage.v0.1`, successor `work.repository-snapshot.v0.1` with predecessor and changed-artifact digests, and successor frozen `review.request.v0.1` | Exact bounded mutation only | `family-local` | `fix_blocked` or upstream reopen | Implementation re-review |
| `work.invalidation` | `work.invalidation-event.v0.1` plus current `work.invalidation-set.v0.1`, `work.graph.v0.1`, and `work.active-closure-set.v0.1` | `work.invalidation.v0.1` plus successor `work.invalidation-set.v0.1` and `work.unaffected-set-evidence.v0.1` | Revocation and independently proven preservation only | `family-local` | Plan or Design reopen | Successor scope, recovery, or terminal |
| `work.slice-closure.compile` | `work.candidate.v0.1` from `work.execution` or `work.fix` plus `work.verification.v0.1` and successor `work.repository-snapshot.v0.1` from `work.verify` plus `work.admission.v0.1` from `work.admission` including `integration_write_set` plus `review.result.v0.1` with `review_kind=checklist_close`, `checklist_verdict=done`, and matching candidate digest when `roadmap_checkbox_update=yes` or `merge_pr_review=yes` | `work.slice-closure-set.v0.1` preserving `{candidate_digest, verification_digest, scope_digest, integration_write_set, snapshot_digest, checklist_review_digest=not_applicable or current review digest, producer_lineage, consumer}` and the shared envelope | Integration-input compilation only | `family-local` | `integration_proposal_blocked` | `work.integration` |
| `work.integration` | `work.slice-closure-set.v0.1` from `work.slice-closure.compile` plus the current successor `work.repository-snapshot.v0.1` from `work.verify` plus current `work.active-closure-set.v0.1` | `work.integration.v0.1` plus successor post-integration `work.repository-snapshot.v0.1` and successor `work.active-closure-set.v0.1`, preserving `{closure_digest, candidate_digest, verification_digest, scope_digest, integration_write_set, snapshot_digest, predecessor_snapshot_digest, producer, consumer}` and the shared envelope | Admitted integration evidence and write set only | `family-local` | `integration_blocked`, conflict, or invalidation | Continuation |
| `work.continuation` | current `work.graph.v0.1` plus current `work.frontier.v0.1` from `work.entry` or a prior continuation plus `work.integration.v0.1` | `work.continuation.v0.1` plus successor `work.frontier.v0.1` with frontier, predecessor frontier, and producer lineage | Route inside unchanged authorization only | `family-local` | `work_blocked` or named reopen | Work admission or terminal |
| `work.complete-state.compile` | either `work.integration.v0.1` plus its post-integration snapshot and latest frontier from `work.continuation`, or `work.entry.v0.1` plus its initial graph, frontier, snapshot, and proof that every node is closed or excluded; both variants include `work.frozen-terminal-consumer.v0.1` | `work.complete-state.v0.1` preserving `{completion_reason=integrated or no_work_required, integration_digest=not_applicable or current digest, frontier_digest, snapshot_digest, frozen_terminal_consumer, producer_lineage, consumer}` and the shared envelope | Terminal-input compilation only | `family-local` | `terminal_proposal_blocked` | `work.terminal` |
| `work.terminal` | `work.complete-state.v0.1` | `work.family-packet.v0.1` plus optional `work.release-handoff.v0.1`, preserving the complete-state digest and terminal consumer | Terminal-specific only | `family-local` | `work_incomplete` or named owner | Release, Plan recovery, clarification, or exit |
| `work.attention-sidecar.emit` | `work.family-packet.v0.1` plus `work.frozen-terminal-consumer.v0.1` plus `attention.table-mutation-envelope.v0.1` | `attention.lifecycle-mutation-sidecar.v0.1` | Sidecar binding only; cannot change terminal | `family-local` | `sidecar_blocked` | `stage-0.attention-table-maintenance` |
| `work.rebon-schema-contract` | `rebon.tool-profile-request.v0.1` | `rebon.tool-profile.v0.1` | Host discovery and admission only | `successor-v0/modules/rebon-host-adapter.md` | `tool_profile_not_admitted` | Rebon dispatcher |
| `work.rebon-task-list-contract` | `rebon.task-binding-request.v0.1` | `rebon.task-binding.v0.1` | Host task-ledger synchronization only | `successor-v0/modules/rebon-host-adapter.md` | `task_list_not_admitted` | Current family task |
| `work.rebon-display-adapter` | `rebon.workflow-binding.v0.1` plus persisted native result | `rebon.result-envelope.v0.1` | Host result translation only | `successor-v0/modules/rebon-host-adapter.md` | `adapter_not_admitted` | Current Work consumer |

## Invariants

- A slice cannot write outside its admitted write set.
- A lease does not grant authority beyond the Work handoff.
- An implementation result cannot advance without both logic-review axes.
- A roadmap-checkbox update cannot advance without both `checklist_close` reviewer assignments: Standards + Correctness and Specification-compliance + Correctness.
- Verification cannot be replaced by a passing tool status without declared behavior coverage.
- Integration cannot consume a stale or invalidated candidate.
- When `roadmap_checkbox_update=yes` or `merge_pr_review=yes`, `work.slice-closure.compile` rejects any missing, stale, non-`checklist_close`, or non-`done` review result; when both are `no`, the checklist review field is `not_applicable`.
- Execution, fixes, verification, and integration emit successor repository snapshots; each downstream compiler consumes the newest snapshot and preserves predecessor lineage.
- `work.entry.validate` emits the initial graph and frontier; `work.continuation` emits each successor frontier; terminal compilation consumes the latest frontier only.
- Work admission initializes every selected-set, operation, lease-set, invalidation-set, closure-set, and unaffected-set binding required by pre-dispatch revalidation or revocation.
- The no-work terminal is valid only from entry evidence proving every graph node already closed or excluded.
- Deferral preserves authority and closure state, emits a successor frontier with the deferred slice ineligible, and cannot advance that slice as complete.
- Changed accepted source invalidates affected future work and preserves only proven-unaffected slices.
- Work never publishes, deploys, activates, or changes external effects.
- Optimized modes cannot begin any Work task-specific operation without a current `optimized.applied-plan.v0.1`; `parallel-normal` cannot invoke that module.

## Recovery

- Recovery creates a successor scope when task identity, scope, digest, or consumer changes.
- Mechanical failure returns the smallest authorized fix and reruns affected review and verification.
- Semantic, scope, authority, normative, or empirical change reopens Plan or Design.
- A finite retry budget ends in a typed block, split, hold, or upstream reopen.

## Terminals

`work_closed_release_candidate`, `work_complete`, `work_blocked`, `plan_reopened`, `design_reopened`, and `clarification_required` are the only Work terminals.

