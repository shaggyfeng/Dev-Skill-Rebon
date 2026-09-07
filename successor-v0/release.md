# Release

## Admission

| Field | Value |
|---|---|
| `family_id` | `release` |
| `stage` | `4` |
| `input` | exact Work release handoff preserving Route artifact-touch classification, convention-packet digest, maintenance-map digest when `brownfield`, current repository/environment snapshot, and optional current authenticated invalidation-event set |
| `output` | one Release terminal |
| `authority` | obligation preparation, validation, reconciliation, and human authority gate |
| `consumer` | Work recovery, human gate, authorized effect handoff, or rejection |

Release does not publish, deploy, activate, or perform external effects.

## Obligation contract

Applicability is exactly `applicable`, `inapplicable_with_evidence`, or `unknown_conflicting`. Unknown or conflicting applicability blocks scheduling and routes to clarification or the smallest upstream owner. The obligation graph is versioned, complete for every applicable obligation, cycle-free, and stable-ordered.

Each scheduled obligation binds `obligation_id`, scope and predecessor digests, prerequisites, resources, conflict set, lease, idempotency identity, candidate contract, validation, `review_surface`, declared `artifact_kinds`, `family_context`, authority sources, recovery, and consumer. Closure is immutable and binds candidate, deterministic evidence, review and fix lineage, authorization state, repository snapshot, and consumer.

## Decision table

| Condition | Module | Result |
|---|---|---|
| valid Work release handoff | `release.admission` | Release entry |
| current binding snapshot and obligation registry are current | `release.applicability` | applicable, evidenced-inapplicable, or unknown/conflicting disposition |
| release predicates are complete | `release.terminal` | typed terminal |
| release evidence required before applicability, validation, or reconciliation | `research` | sourced evidence packet |
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
| reconciliation is complete, lifecycle result is current or `not_applicable`, and no authority gate remains | `release.complete-state.compile` | complete state |
| roadmap completion is reached | `release.context-lifecycle.request` | context lifecycle request |
| current lifecycle request is admitted | `release.context-lifecycle.proposal` | context lifecycle proposal |
| lifecycle proposal has entries requiring disposition | `release.context-lifecycle.disposition` | human disposition |
| lifecycle proposal is empty or disposition is current | `release.context-lifecycle.result` | typed lifecycle result |
| external authority is required | `release.effect-request.compile` then `release.authority-gate` | authorized-effect handoff, hold, revision, or rejection |

## Operation

1. Validate the Work release handoff.
2. When mode is `parallel-optimized` or `sequential-optimized`, complete one optimized preworkflow for each admitted obligation task and separately scheduled reconciliation task before its first operation; omit it in both normal modes.
3. Pin the current Work, Plan, registry, repository, capability, and invalidation bindings.
4. Classify every registry obligation; preserve evidence for inapplicability and fail closed on unknown or conflict. After the current binding snapshot and obligation registry are current, invoke `release.applicability` and retain its typed disposition before graph construction.
5. When release evidence is required before applicability, validation, or reconciliation, invoke `research` with producer `Release`, the exact consuming obligation or reconciliation scope, and the current mutable seams; when constructing `research.request.v0.1`, bind the current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first Research content read and carry `read_plan_digest`, `coverage_digest`, and remaining frontier in the request/result. An unusable binding follows `release_blocked` or `research_invalidated` and cannot advance the Release node. Consume the current evidence packet or typed no-match result; `research_invalidated` reopens the affected Release node against a successor seam digest. Build the complete cycle-free obligation graph, bind current invalidation, initialize the obligation state set only on first scheduling, and otherwise emit its digest-linked rescheduling successor.
6. Lease and run each admitted obligation within its authority ceiling and idempotency identity; emit the current or successor repository snapshot and refresh the binding snapshot before validation.
7. Validate each candidate against the refreshed binding snapshot. Every applicable review request declares `review_surface`, `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, and consumer. Use `implementation_logic` for implementation or instruction changes and `checklist_close` for a roadmap-checkbox or merge-PR review. A lifecycle may emit both requests at their separate gates; when review is inapplicable, proceed from validation to closure.
8. Do not close an obligation before its validation result and any applicable review result are current. An `implementation_logic` result must be clean (`findings=[]`, including any derived Correctness assignment); a `checklist_close` result must have `checklist_verdict=done`.
9. Apply bounded fixes and rerun every affected validation and review.
10. Compile the current candidate, validation, applicable review, fix lineage, snapshot, and authorization state into immutable `release.obligation-closure.v0.1` and update `release.obligation-closure-set.v0.1`.
11. Revoke affected closures and preserve only proven-independent closures after authenticated invalidation; emit the authenticated event-set form and preserve it through snapshot, applicability, graph, and invalidation-state refresh before rescheduling.
12. Reconcile every obligation from the applicability result, obligation graph, current obligation state set, closure set, and invalidation state; account for applicable, evidenced-inapplicable, held, invalidated, and closed states.
13. When an external effect is requested, compile the exact disclosure and effect-row set from reconciliation, require human disposition, and emit the typed authorized-effect handoff only on approval; when roadmap completion applies, traverse the Release-owned context lifecycle request, proposal, disposition, and result sequence through Domain Modeling and require its current result before effect-request compilation; outside roadmap completion the lifecycle arm is `not_applicable`; retain `live_effect=false`.
14. When no authority gate remains, at roadmap completion traverse the Release-owned context lifecycle request, proposal, disposition, and result sequence through `successor-v0/modules/domain-modeling.md` before terminal input compilation; outside roadmap completion the lifecycle arm is `not_applicable`. Then compile `release.complete-state.v0.1` from reconciliation, current bindings, the latest authorization state, the current lifecycle-result digest when applicable, and the frozen terminal consumer; invoke `release.terminal`.
15. Re-enter Stage 0 and Release before reconciliation, disposition, or terminal compilation.

## Context lifecycle representation

The roadmap-completion lifecycle is a Release-owned sequence with Domain Modeling proposal and write authority:

```text
reconciliation [roadmap_completion=true]
 -> context_lifecycle_requested | release_blocked
context_lifecycle_requested
 -> context_lifecycle_proposal | release_blocked
context_lifecycle_proposal [proposal_empty]
 -> context_lifecycle_resolved
context_lifecycle_proposal [proposal_requires_disposition]
 -> context_lifecycle_disposition | release_blocked
context_lifecycle_disposition
 -> context_lifecycle_resolved | release_blocked
context_lifecycle_resolved
 -> effect_request_compilation | terminal_input_compilation | release_blocked
reconciliation [roadmap_completion=false]
 -> context_lifecycle_not_applicable
context_lifecycle_not_applicable
 -> effect_request_compilation | terminal_input_compilation
```

| Gate | Owner | Required packet | Next consumer |
|---|---|---|---|
| `context_lifecycle_requested` | Release | reconciliation, roadmap-completion scope, context/history identities or explicit absence, promoted candidates, current read-plan and coverage binding | Domain Modeling proposal |
| `context_lifecycle_proposal` | Domain Modeling | immutable stable retained/removal/retirement IDs, evidence, authority, predecessor digests, history entries, and empty/no-change status | human disposition or typed no-change result |
| `context_lifecycle_disposition` | human | exact proposal and per-entry accept/reject decisions | Domain Modeling result |
| `context_lifecycle_resolved` | Domain Modeling | applied, retained, rejected, retired IDs and successor context/history digests or no-change | effect-request or terminal-input compiler |
| `context_lifecycle_blocked` | Domain Modeling | retained failure reason and current retry binding | Release `release_blocked` |

Effect-request and terminal-input compilation consume the current lifecycle-result digest whenever roadmap completion applies; outside roadmap completion the lifecycle is `not_applicable`.

## State machine

```text
work_release_handoff -> release_entry
release_entry -> authorization_state_bound | release_blocked
authorization_state_bound -> snapshot_bound | release_blocked
snapshot_bound -> research_requested | applicability_requested
research_requested -> research_evidence | research_no_match | release_blocked
research_evidence -> applicability_requested | obligation_validation | reconciliation | research_invalidated
research_no_match -> applicability_requested | obligation_validation | reconciliation | release_blocked
research_invalidated -> research_successor_bound | snapshot_refresh | clarification_required
research_successor_bound -> research_requested
applicability_requested -> applicability_bound | clarification_required
applicability_bound -> graph_ready | clarification_required
graph_ready -> invalidation_state_bound | release_blocked
invalidation_state_bound -> schedule_ready | release_blocked
schedule_ready -> obligation_running | reconciliation
obligation_running -> candidate_snapshot_refresh | invalidated | release_blocked
candidate_snapshot_refresh -> obligation_validation | release_blocked
obligation_validation -> obligation_review_request | obligation_closure | release_blocked
obligation_review_request -> obligation_review | release_blocked
obligation_review [review_kind=implementation_logic and findings=[]] -> obligation_closure
obligation_review [review_kind=implementation_logic and findings!=[]] -> obligation_fix
obligation_review [review_kind=checklist_close and checklist_verdict=done] -> obligation_closure
obligation_review [review_kind=checklist_close and checklist_verdict=not_done] -> obligation_fix
obligation_review [checklist_verdict=blocked or review_not_admitted or review_axis_blocked] -> release_blocked
obligation_fix -> candidate_snapshot_refresh | release_blocked
obligation_closure -> obligation_complete | release_blocked
obligation_complete -> obligation_running | reconciliation
invalidated -> snapshot_refresh | reconciliation | work_reopened
snapshot_refresh -> snapshot_bound | release_blocked
reconciliation [roadmap_completion=true] -> context_lifecycle_requested | release_blocked
reconciliation [roadmap_completion=false] -> context_lifecycle_not_applicable
context_lifecycle_requested -> context_lifecycle_proposal | release_blocked
context_lifecycle_proposal [proposal_empty] -> context_lifecycle_resolved
context_lifecycle_proposal [proposal_requires_disposition] -> context_lifecycle_disposition | release_blocked
context_lifecycle_disposition -> context_lifecycle_resolved | release_blocked
context_lifecycle_resolved -> effect_request_compilation | terminal_input_compilation | release_blocked
context_lifecycle_not_applicable -> effect_request_compilation | terminal_input_compilation
effect_request_compilation -> authority_gate | release_blocked
authority_gate [roadmap_completion=false or lifecycle_result_current_at_roadmap_completion] -> authorized_effect_handoff | release_held | release_rejected
terminal_input_compilation [roadmap_completion=false or lifecycle_result_current_at_roadmap_completion] -> terminal_compile | release_blocked
terminal_compile -> release_closed | release_blocked | release_rejected
```

## Module registry

| Module | Versioned input | Versioned output | Actor and ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | planning only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact current Release or Recovery task |
| `release.admission` | `work.release-handoff.v0.1` + `release.repository-snapshot.v0.1` + optional current authenticated `release.invalidation-event-set.v0.1` | `release.entry.v0.1` preserving optional invalidation-event lineage plus initialized `release.authorization-state.v0.1` with `state=no_external_effect_requested` and `live_effect=false` plus empty `release.obligation-closure-set.v0.1` | scope/currency only | family-local | `release_not_admitted` | authorization-state and snapshot binding |
| `release.snapshot` | `release.entry.v0.1` + current `release.repository-snapshot.v0.1` + `release.registry-set.v0.1` + current `release.authorization-state.v0.1` + optional current `release.invalidation-event-set.v0.1`, `release.obligation-state-set.v0.1`, candidate digest, and fix lineage | successor `release.binding-snapshot.v0.1` plus current authorization, repository, invalidation-event, obligation-state, candidate, and fix lineage | evidence only | family-local | `snapshot_blocked` | applicability resolver or candidate validation |
| `release.applicability` | `release.binding-snapshot.v0.1` + `release.obligation-registry.v0.1` | `release.obligation-applicability.v0.1` | classification only | family-local | `applicability_blocked` | graph builder |
| `research` | `research.request.v0.1` with Release producer and consumer | `research.evidence.v0.1` | Researcher; sourced evidence only | `successor-v0/modules/research.md` | `research_blocked`, `research_invalidated`, or `research_no_match` | applicability, validation, reconciliation, or Handoff |
| `release.graph` | `release.obligation-applicability.v0.1` plus optional current `release.invalidation-event-set.v0.1` from the binding snapshot | `release.obligation-graph.v0.1` preserving current invalidation-event lineage | graph construction only | family-local | `graph_blocked` | invalidation-state initializer |
| `release.invalidation-state.initialize` | `release.binding-snapshot.v0.1` + `release.obligation-graph.v0.1` + optional current authenticated `release.invalidation-event-set.v0.1` | current `release.invalidation.v0.1` with `state=none` or the authenticated event set and its lineage | current-state binding only | family-local | `invalidation_state_blocked` | scheduler or reconciliation |
| `release.schedule` | `release.obligation-graph.v0.1` + current `release.invalidation.v0.1` + `release.resource-state.v0.1` + optional current `release.obligation-state-set.v0.1` | `release.dispatch-set.v0.1` plus initial `release.obligation-state-set.v0.1` when none exists or its digest-linked successor on rescheduling, preserving invalidated and proven-preserved dispositions and the invalidation-state digest | exact dispatch only | family-local | `schedule_blocked` | obligation executor or reconciliation |
| `release.obligation` | `release.dispatch-set.v0.1` + `release.evidence-contract.v0.1` + current `release.obligation-state-set.v0.1` + current `release.repository-snapshot.v0.1` | `release.obligation-candidate.v0.1` plus successor `release.obligation-state-set.v0.1` and current or successor `release.repository-snapshot.v0.1` with changed-artifact lineage | candidate mechanics only | family-local | `obligation_blocked` | snapshot refresh, then validation |
| `release.validate` | `release.obligation-candidate.v0.1` + current `release.binding-snapshot.v0.1` + current `release.obligation-state-set.v0.1` | `release.validation.v0.1` plus successor `release.obligation-state-set.v0.1` | evidence only | family-local | `validation_failed` | `release.review-request.compile` when review applies; `release.close` when it does not; fix or reconciliation on their predicates |
| `release.review-request.compile` | current `release.obligation-candidate.v0.1` + current `release.validation.v0.1` + `release.binding-snapshot.v0.1` + applicable review predicate + exact producer and consumer contracts | frozen `review.request.v0.1` bound to the candidate/validation digests, selected review kind/axes, `review_surface`, `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, and consumer | Review-input compilation only | family-local | `review_not_admitted` | `release.review` |
| `release.review` | `review.request.v0.1` carrying the current candidate, validation, declarations, exact frozen subject, producer, consumer, and assignment set + `release.validation.v0.1` | `review.result.v0.1` | findings/verdict only | `successor-v0/modules/review.md` | `review_not_admitted` or `review_axis_blocked` | fix or closure compiler |
| `release.fix` | `release.obligation-candidate.v0.1` + `review.result.v0.1` + current `release.obligation-state-set.v0.1` + current `release.repository-snapshot.v0.1` | successor `release.obligation-candidate.v0.1` + `release.fix-lineage.v0.1` + successor `release.obligation-state-set.v0.1` + successor `release.repository-snapshot.v0.1` with changed-artifact lineage | bounded mutation only | family-local | `fix_blocked` | snapshot refresh, validation, then successor review-request compilation when applicable |
| `release.close` | `release.obligation-candidate.v0.1` + `release.validation.v0.1` + applicable clean `review.result.v0.1` (`findings=[]` for `implementation_logic` or `checklist_verdict=done` for `checklist_close`) + optional `release.fix-lineage.v0.1` + current `release.binding-snapshot.v0.1` + current `release.authorization-state.v0.1` + current `release.obligation-state-set.v0.1` + current `release.obligation-closure-set.v0.1` | `release.obligation-closure.v0.1` + digest-linked successor `release.obligation-closure-set.v0.1` + successor `release.obligation-state-set.v0.1` | immutable closure compilation only | family-local | `closure_blocked` | obligation scheduler or reconciliation |
| `release.invalidate` | `release.invalidation-event.v0.1` + `release.obligation-graph.v0.1` + current `release.obligation-state-set.v0.1` | `release.invalidation.v0.1` + authenticated `release.invalidation-event-set.v0.1` + successor `release.obligation-state-set.v0.1` | affected revocation/preservation only | family-local | upstream reopen | snapshot refresh, reconciliation, or Work reopen |
| `release.reconcile` | `release.obligation-applicability.v0.1` + `release.obligation-graph.v0.1` + current `release.obligation-state-set.v0.1` + `release.obligation-closure-set.v0.1` + current `release.binding-snapshot.v0.1` + `release.invalidation.v0.1` with `state=none` or an authenticated event set | `release.reconciliation.v0.1` preserving `{applicability_digest, graph_digest, obligation_state_digest, closure_digest, binding_snapshot_digest, invalidation_digest, one disposition per obligation, producer, consumer}` | integration evidence only | family-local | `reconciliation_blocked` | context-lifecycle request, effect-request, or terminal compiler |
| `release.effect-request.compile` | `release.reconciliation.v0.1` + `release.binding-snapshot.v0.1` + current `release.authorization-state.v0.1` + `release.frozen-terminal-consumer.v0.1` + exact requested external-effect rows + current `release.context-lifecycle-result.v0.1` when roadmap completion applies | `release.disclosure.v0.1` + `release.effect-row-set.v0.1`, preserving effect digest, lifecycle-result digest when applicable, authority required, `live_effect=false`, producer, and consumer | Disclosure-input compilation only | family-local | `human_gate_pending` | `release.authority-gate` |
| `release.authority-gate` | current `release.authorization-state.v0.1` + `release.disclosure.v0.1` + `release.effect-row-set.v0.1` | successor `release.authorization-state.v0.1` plus `release.human-disposition.v0.1` and, only when approved, `release.family-packet.v0.1` with terminal `authorized_effect_handoff`; all preserve `live_effect=false` | exact human disposition only | family-local | `human_gate_pending` | named terminal consumer, hold, revision, or rejection |
| `release.complete-state.compile` | `release.reconciliation.v0.1` + `release.binding-snapshot.v0.1` + latest `release.authorization-state.v0.1` + `release.frozen-terminal-consumer.v0.1` + optional `release.human-disposition.v0.1` + current `release.context-lifecycle-result.v0.1` when roadmap completion applies | `release.complete-state.v0.1` preserving the lifecycle-result digest when applicable | Terminal-input compilation only | family-local | `terminal_input_blocked` | `release.terminal` |
| `release.context-lifecycle.request` | `release.reconciliation.v0.1` + roadmap-completion scope + current context/history identities or explicit absence + promoted candidate set + current `runtime.read-plan.v0.1` and coverage binding | `release.context-lifecycle-request.v0.1` | Release lifecycle sequencing only | family-local | `release_blocked` | Domain Modeling proposal |
| `release.context-lifecycle.proposal` | `release.context-lifecycle-request.v0.1` + admitted context/history reads and current read-plan coverage | `release.context-lifecycle-proposal.v0.1` with stable retained/removal/retirement IDs, evidence and authority, predecessor digests, history entries, and empty/no-change status | Domain Modeling proposal formation only | `successor-v0/modules/domain-modeling.md` | `context_lifecycle_blocked` | Release disposition or typed no-change result |
| `release.context-lifecycle.disposition` | `release.context-lifecycle-proposal.v0.1` + exact human per-entry accept/reject decisions | `release.context-lifecycle-disposition.v0.1` bound to the exact proposal and per-entry decisions | Exact human disposition only | family-local | `human_gate_pending` or `release_blocked` | Domain Modeling result |
| `release.context-lifecycle.result` | `release.context-lifecycle-request.v0.1` + `release.context-lifecycle-proposal.v0.1` + `release.context-lifecycle-disposition.v0.1` or explicit empty/no-change result + current context/history | `release.context-lifecycle-result.v0.1` with applied, retained, rejected, and retired IDs plus successor context/history digests or no-change and result digest | Domain Modeling; disposition-gated write and result only | `successor-v0/modules/domain-modeling.md` | `context_lifecycle_blocked` | Release effect-request or complete-state compiler |
| `release.terminal` | `release.complete-state.v0.1` | `release.family-packet.v0.1` | terminal compilation only | family-local | `release_blocked` | named terminal consumer |
| `release.attention-sidecar.emit` | `release.family-packet.v0.1` + `release.frozen-terminal-consumer.v0.1` | `attention.lifecycle-mutation-sidecar.v0.1` | sidecar binding only | family-local | `sidecar_blocked` | `stage-0.attention-table-maintenance` |

## Invariants

- Research evidence may inform Release applicability or validation but cannot authorize an external effect or replace the human authority gate.
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
- Every applicable Release review consumes a declaration-complete request compiled after current validation; implementation review findings must be empty before closure, while checklist closure requires `checklist_verdict=done`.
- A fix returns through validation before a successor review request is compiled.
- `release.close` rejects any finding-bearing implementation review, any non-`done` checklist review, or a missing review declaration; `not_done` returns to fix and `blocked` remains held.
- Rescheduling after invalidation preserves the authenticated event-set digest through snapshot, applicability, graph, and invalidation-state binding.
- Candidate creation and fixes emit current or successor repository snapshots; binding refresh precedes validation, review-request compilation, closure, reconciliation, and terminal compilation.
- `release.schedule` initializes the obligation state set; obligation execution, validation, fixes, closure, holds, and invalidation emit successor entries.
- `release.reconcile` rejects any obligation missing an applicability, graph, current-state, closure, or invalidation disposition.
- At roadmap completion, Release sequences `context_lifecycle_requested -> context_lifecycle_proposal -> context_lifecycle_disposition -> context_lifecycle_resolved` before effect or terminal compilation; outside roadmap completion the lifecycle arm is `not_applicable`. Both downstream compilers consume the current lifecycle-result digest when applicable.
- A lifecycle proposal is immutable and its result is disposition-gated; empty/no-change and all-rejected results do not write context files, while partial acceptance applies only accepted entries.
- Every obligation closure consumes the current closure-set digest and emits its successor; reconciliation consumes the latest set.
- Exactly one Release terminal is emitted and it never performs a live effect.
- First scheduling initializes the obligation state set; rescheduling consumes its current digest and emits a successor without losing invalidated or preserved dispositions.

## Recovery

- Any unresolved obligation returns a hold, block, rejection, or upstream reopen.
- A stale or invalidated Research result never advances applicability, validation, or reconciliation; it emits a successor request against the new seam digest and re-enters the affected Release node only after that successor packet is current.
- Refresh snapshot and applicability before rescheduling invalidated obligations.
- Review or validation findings return the obligation to bounded fix and affected reruns.
- A finite retry budget ends in a typed hold, block, rejection, or upstream reopen.
- A missing or stale lifecycle binding, incomplete lifecycle read coverage, authority conflict, missing or stale human disposition, lifecycle write/read-back failure, or exhausted retry returns `context_lifecycle_blocked` from Domain Modeling and maps to `release_blocked`; no effect or terminal compilation consumes a stale lifecycle result.

## Terminals

`release_closed`, `authorized_effect_handoff`, `release_held`, `release_blocked`, `release_rejected`, `work_reopened`, and `clarification_required` are the only Release terminals.


