# Release

## Admission

| Field | Value |
|---|---|
| `family_id` | `release` |
| `stage` | `4` |
| `input` | exact Work release handoff, current repository/environment snapshot, and optional current authenticated invalidation-event set |
| `output` | one Release terminal |
| `authority` | obligation preparation, validation, reconciliation, and human authority gate |
| `consumer` | Work recovery, human gate, authorized effect handoff, or rejection |

Release does not publish, deploy, activate, or perform external effects.

## Obligation contract

Applicability is exactly `applicable`, `inapplicable_with_evidence`, or `unknown_conflicting`. Unknown or conflicting applicability blocks scheduling and routes to clarification or the smallest upstream owner. The obligation graph is versioned, complete for every applicable obligation, cycle-free, and stable-ordered.

Each scheduled obligation binds `obligation_id`, scope and predecessor digests, prerequisites, resources, conflict set, lease, idempotency identity, candidate contract, validation, review surface, recovery, and consumer. Closure is immutable and binds candidate, deterministic evidence, review and fix lineage, authorization state, repository snapshot, and consumer.

## Decision table

| Condition | Module | Result |
|---|---|---|
| valid Work release handoff | `release.admission` | Release entry |
| current snapshot required | `release.snapshot` | bound snapshot |
| obligation applicability unclear | `release.applicability` | applicable set or clarification |
| obligations have dependencies | `release.graph` | obligation graph |
| graph is valid | `release.invalidation-state.initialize` | current invalidation state |
| graph and invalidation state are current | `release.schedule` | ordered obligations |
| obligation is admitted | `release.obligation` | obligation candidate |
| candidate requires validation | `release.validate` | validation result |
| obligation candidate requires review | `release.review-request.compile` then `release.review` | findings |
| finding requires change | `release.fix` | successor candidate |
| current candidate, validation, review, and authorization state pass | `release.close` | immutable obligation closure |
| obligation graph has no authenticated invalidation event | `release.invalidation-state.initialize` | empty invalidation state |
| accepted source changes | `release.invalidate` | affected obligation set |
| obligation results require reconciliation | `release.reconcile` | reconciled release state |
| reconciliation is complete and no authority gate remains | `release.complete-state.compile` | complete state |
| roadmap completion is reached | `release.context-lifecycle` | CONTEXT.md lifecycle review before the terminal compiles |
| external authority is required | `release.effect-request.compile` then `release.authority-gate` | authorized-effect handoff, hold, revision, or rejection |
| release predicates are complete | `release.terminal` | typed terminal |

## Operation

1. Validate the Work release handoff.
2. When mode is `parallel-optimized` or `sequential-optimized`, complete one optimized preworkflow for each admitted obligation task and separately scheduled reconciliation task before its first operation; omit it in both normal modes.
3. Pin the current Work, Plan, registry, repository, capability, and invalidation bindings.
4. Classify every registry obligation; preserve evidence for inapplicability and fail closed on unknown or conflict.
5. Build the complete cycle-free obligation graph, bind current invalidation, initialize the obligation state set only on first scheduling, and otherwise emit its digest-linked rescheduling successor.
6. Lease and run each admitted obligation within its authority ceiling and idempotency identity; emit the current or successor repository snapshot and refresh the binding snapshot before validation.
7. Validate each candidate against the refreshed binding snapshot. Each review request has one `review_kind`: use `implementation_logic` for implementation or instruction changes, and `checklist_close` for a roadmap-checkbox or merge-PR review. A lifecycle may emit both requests at their separate gates; when review is inapplicable, proceed from validation to closure.
8. Do not close an obligation before its validation result and any applicable review result are current; a `checklist_close` result must have `checklist_verdict=done`.
9. Apply bounded fixes and rerun every affected validation and review.
10. Compile the current candidate, validation, applicable review, fix lineage, snapshot, and authorization state into immutable `release.obligation-closure.v0.1` and update `release.obligation-closure-set.v0.1`.
11. Revoke affected closures and preserve only proven-independent closures after authenticated invalidation; emit the authenticated event-set form and preserve it through snapshot, applicability, graph, and invalidation-state refresh before rescheduling.
12. Reconcile every obligation from the applicability result, obligation graph, current obligation state set, closure set, and invalidation state; account for applicable, evidenced-inapplicable, held, invalidated, and closed states.
13. When an external effect is requested, compile the exact disclosure and effect-row set from reconciliation, require human disposition, and emit the typed authorized-effect handoff only on approval; at roadmap completion the CONTEXT.md lifecycle review runs before the handoff is emitted; retain `live_effect=false`.
14. When no authority gate remains, run the CONTEXT.md lifecycle review at roadmap completion through `successor-v0/modules/domain-modeling.md` — the disposition-gated prune-and-archive with promoted-candidate retirement — then compile `release.complete-state.v0.1` from reconciliation, current bindings, the latest authorization state, and the frozen terminal consumer; invoke `release.terminal`.
15. Re-enter Stage 0 and Release before reconciliation, disposition, or terminal compilation.

## State machine

```text
work_release_handoff -> release_entry
release_entry -> authorization_state_bound | release_blocked
authorization_state_bound -> snapshot_bound | release_blocked
snapshot_bound -> applicability_bound
applicability_bound -> graph_ready | clarification_required
graph_ready -> invalidation_state_bound | release_blocked
invalidation_state_bound -> schedule_ready | release_blocked
schedule_ready -> obligation_running | reconciliation
obligation_running -> candidate_snapshot_refresh | invalidated | release_blocked
candidate_snapshot_refresh -> obligation_validation | release_blocked
obligation_validation -> obligation_review_request | obligation_closure | release_blocked
obligation_review_request -> obligation_review | release_blocked
obligation_review [checklist_verdict=done or not_applicable] -> obligation_closure
obligation_review [checklist_verdict=not_done] -> obligation_fix
obligation_review [checklist_verdict=blocked or review_not_admitted or review_axis_blocked] -> release_blocked
obligation_fix -> candidate_snapshot_refresh | release_blocked
obligation_closure -> obligation_complete | release_blocked
obligation_complete -> obligation_running | reconciliation
invalidated -> snapshot_refresh | reconciliation | work_reopened
snapshot_refresh -> snapshot_bound | release_blocked
reconciliation -> effect_request_compilation | terminal_input_compilation | release_blocked
effect_request_compilation -> authority_gate | release_blocked
authority_gate [lifecycle_review_complete_at_roadmap_completion] -> authorized_effect_handoff | release_held | release_rejected
terminal_input_compilation [lifecycle_review_complete_at_roadmap_completion] -> terminal_compile | release_blocked
terminal_compile -> release_closed | release_blocked | release_rejected
```

## Module registry

| Module | Versioned input | Versioned output | Actor and ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | planning only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact current Release or Recovery task |
| `release.admission` | `work.release-handoff.v0.1` + `release.repository-snapshot.v0.1` + optional current authenticated `release.invalidation-event-set.v0.1` | `release.entry.v0.1` preserving optional invalidation-event lineage plus initialized `release.authorization-state.v0.1` with `state=no_external_effect_requested` and `live_effect=false` plus empty `release.obligation-closure-set.v0.1` | scope/currency only | family-local | `release_not_admitted` | authorization-state and snapshot binding |
| `release.snapshot` | `release.entry.v0.1` + current `release.repository-snapshot.v0.1` + `release.registry-set.v0.1` + current `release.authorization-state.v0.1` + optional current `release.invalidation-event-set.v0.1`, `release.obligation-state-set.v0.1`, candidate digest, and fix lineage | successor `release.binding-snapshot.v0.1` plus current authorization, repository, invalidation-event, obligation-state, candidate, and fix lineage | evidence only | family-local | `snapshot_blocked` | applicability resolver or candidate validation |
| `release.applicability` | `release.binding-snapshot.v0.1` + `release.obligation-registry.v0.1` | `release.obligation-applicability.v0.1` | classification only | family-local | `applicability_blocked` | graph builder |
| `release.graph` | `release.obligation-applicability.v0.1` plus optional current `release.invalidation-event-set.v0.1` from the binding snapshot | `release.obligation-graph.v0.1` preserving current invalidation-event lineage | graph construction only | family-local | `graph_blocked` | invalidation-state initializer |
| `release.invalidation-state.initialize` | `release.binding-snapshot.v0.1` + `release.obligation-graph.v0.1` + optional current authenticated `release.invalidation-event-set.v0.1` | current `release.invalidation.v0.1` with `state=none` or the authenticated event set and its lineage | current-state binding only | family-local | `invalidation_state_blocked` | scheduler or reconciliation |
| `release.schedule` | `release.obligation-graph.v0.1` + current `release.invalidation.v0.1` + `release.resource-state.v0.1` + optional current `release.obligation-state-set.v0.1` | `release.dispatch-set.v0.1` plus initial `release.obligation-state-set.v0.1` when none exists or its digest-linked successor on rescheduling, preserving invalidated and proven-preserved dispositions and the invalidation-state digest | exact dispatch only | family-local | `schedule_blocked` | obligation executor or reconciliation |
| `release.obligation` | `release.dispatch-set.v0.1` + `release.evidence-contract.v0.1` + current `release.obligation-state-set.v0.1` + current `release.repository-snapshot.v0.1` | `release.obligation-candidate.v0.1` plus successor `release.obligation-state-set.v0.1` and current or successor `release.repository-snapshot.v0.1` with changed-artifact lineage | candidate mechanics only | family-local | `obligation_blocked` | snapshot refresh, then validation |
| `release.validate` | `release.obligation-candidate.v0.1` + current `release.binding-snapshot.v0.1` + current `release.obligation-state-set.v0.1` | `release.validation.v0.1` plus successor `release.obligation-state-set.v0.1` | evidence only | family-local | `validation_failed` | `release.review-request.compile` when review applies; `release.close` when it does not; fix or reconciliation on their predicates |
| `release.review-request.compile` | current `release.obligation-candidate.v0.1` + current `release.validation.v0.1` + `release.binding-snapshot.v0.1` + applicable review predicate + exact producer and consumer contracts | frozen `review.request.v0.1` bound to the candidate/validation digests and selected review kind/axes | Review-input compilation only | family-local | `review_not_admitted` | `release.review` |
| `release.review` | `review.request.v0.1` + `release.validation.v0.1` | `review.result.v0.1` | findings/verdict only | `successor-v0/modules/review.md` | `review_not_admitted` or `review_axis_blocked` | fix or closure compiler |
| `release.fix` | `release.obligation-candidate.v0.1` + `review.result.v0.1` + current `release.obligation-state-set.v0.1` + current `release.repository-snapshot.v0.1` | successor `release.obligation-candidate.v0.1` + `release.fix-lineage.v0.1` + successor `release.obligation-state-set.v0.1` + successor `release.repository-snapshot.v0.1` with changed-artifact lineage | bounded mutation only | family-local | `fix_blocked` | snapshot refresh, validation, then successor review-request compilation when applicable |
| `release.close` | `release.obligation-candidate.v0.1` + `release.validation.v0.1` + applicable `review.result.v0.1` + optional `release.fix-lineage.v0.1` + current `release.binding-snapshot.v0.1` + current `release.authorization-state.v0.1` + current `release.obligation-state-set.v0.1` + current `release.obligation-closure-set.v0.1` | `release.obligation-closure.v0.1` + digest-linked successor `release.obligation-closure-set.v0.1` + successor `release.obligation-state-set.v0.1` | immutable closure compilation only | family-local | `closure_blocked` | obligation scheduler or reconciliation |
| `release.invalidate` | `release.invalidation-event.v0.1` + `release.obligation-graph.v0.1` + current `release.obligation-state-set.v0.1` | `release.invalidation.v0.1` + authenticated `release.invalidation-event-set.v0.1` + successor `release.obligation-state-set.v0.1` | affected revocation/preservation only | family-local | upstream reopen | snapshot refresh, reconciliation, or Work reopen |
| `release.reconcile` | `release.obligation-applicability.v0.1` + `release.obligation-graph.v0.1` + current `release.obligation-state-set.v0.1` + `release.obligation-closure-set.v0.1` + current `release.binding-snapshot.v0.1` + `release.invalidation.v0.1` with `state=none` or an authenticated event set | `release.reconciliation.v0.1` preserving `{applicability_digest, graph_digest, obligation_state_digest, closure_digest, binding_snapshot_digest, invalidation_digest, one disposition per obligation, producer, consumer}` | integration evidence only | family-local | `reconciliation_blocked` | effect-request or terminal compiler |
| `release.effect-request.compile` | `release.reconciliation.v0.1` + `release.binding-snapshot.v0.1` + current `release.authorization-state.v0.1` + `release.frozen-terminal-consumer.v0.1` + exact requested external-effect rows | `release.disclosure.v0.1` + `release.effect-row-set.v0.1`, preserving effect digest, authority required, `live_effect=false`, producer, and consumer | Disclosure-input compilation only | family-local | `human_gate_pending` | `release.authority-gate` |
| `release.authority-gate` | current `release.authorization-state.v0.1` + `release.disclosure.v0.1` + `release.effect-row-set.v0.1` | successor `release.authorization-state.v0.1` plus `release.human-disposition.v0.1` and, only when approved, `release.family-packet.v0.1` with terminal `authorized_effect_handoff`; all preserve `live_effect=false` | exact human disposition only | family-local | `human_gate_pending` | named terminal consumer, hold, revision, or rejection |
| `release.complete-state.compile` | `release.reconciliation.v0.1` + `release.binding-snapshot.v0.1` + latest `release.authorization-state.v0.1` + `release.frozen-terminal-consumer.v0.1` + optional `release.human-disposition.v0.1` | `release.complete-state.v0.1` | Terminal-input compilation only | family-local | `terminal_input_blocked` | `release.terminal` |
| `release.context-lifecycle` | `release.reconciliation.v0.1` + the project `CONTEXT.md` | lifecycle review result with disposition-gated prune-and-archive proposals | bounded review and proposal only | `successor-v0/modules/domain-modeling.md` | `reconciliation_blocked` | `release.complete-state.compile` |
| `release.terminal` | `release.complete-state.v0.1` | `release.family-packet.v0.1` | terminal compilation only | family-local | `release_blocked` | named terminal consumer |
| `release.attention-sidecar.emit` | `release.family-packet.v0.1` + `release.frozen-terminal-consumer.v0.1` | `attention.lifecycle-mutation-sidecar.v0.1` | sidecar binding only | family-local | `sidecar_blocked` | `stage-0.attention-table-maintenance` |

## Invariants

- A Release obligation cannot exceed the Work handoff authority.
- A successful tool result cannot imply publication, deployment, activation, or external effect.
- A stale snapshot invalidates only the affected obligations and their dependents.
- Reconciliation must account for every applicable obligation and dependency.
- Human authority gates are explicit, typed, and revocable.
- Validation precedes closure; review runs only when the obligation's review predicate applies.
- Authorized effect handoff is not external effect execution.
- Reconciliation accounts for every registry obligation and preserves exact invalidation lineage.
- `release.admission` initializes authorization state and the empty closure set; `release.authority-gate` emits the successor authorization state, and terminal or authorized-handoff compilation consumes the latest state.
- Scheduling requires and preserves the current invalidation-state digest.
- External-effect disclosure and effect rows are compiled from reconciliation; only the authority gate can emit `authorized_effect_handoff`, and it never performs the effect.
- Every applicable Release review consumes a request compiled after current validation; a fix returns through validation before a successor request is compiled.
- `release.close` rejects a `checklist_close` result unless `checklist_verdict=done`; `not_done` returns to fix and `blocked` remains held.
- Rescheduling after invalidation preserves the authenticated event-set digest through snapshot, applicability, graph, and invalidation-state binding.
- First scheduling initializes the obligation state set; rescheduling consumes its current digest and emits a successor without losing invalidated or preserved dispositions.
- Candidate creation and fixes emit current or successor repository snapshots; binding refresh precedes validation, review-request compilation, closure, reconciliation, and terminal compilation.
- `release.schedule` initializes the obligation state set; obligation execution, validation, fixes, closure, holds, and invalidation emit successor entries.
- `release.reconcile` rejects any obligation missing an applicability, graph, current-state, closure, or invalidation disposition.
- Every obligation closure consumes the current closure-set digest and emits its successor; reconciliation consumes the latest set.
- Exactly one Release terminal is emitted and it never performs a live effect.

## Recovery

- Any unresolved obligation returns a hold, block, rejection, or upstream reopen.
- Refresh snapshot and applicability before rescheduling invalidated obligations.
- Review or validation findings return the obligation to bounded fix and affected reruns.
- A finite retry budget ends in a typed hold, block, rejection, or upstream reopen.

## Terminals

`release_closed`, `authorized_effect_handoff`, `release_held`, `release_blocked`, `release_rejected`, `work_reopened`, and `clarification_required` are the only Release terminals.


