# Review

## Admission

| Field | Value |
|---|---|
| `module_id` | `stage-0.review` |
| `contract_version` | `devskill.module.v0.2` |
| `input` | candidate, exact surface, authority sources, producer, consumer, review context, review kind, review state, mode, and reviewer assignment set |
| `output` | findings or checklist verdict |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| `authority` | findings and verdict only |
| `consumer` | originating family, Route overlay/requester, or checklist closure |

`review.request.v0.1` binds `review_kind`, `review_context`, `review_state`, candidate digest, exact surface, authority source IDs, producer contract, consumer contract, mode, roadmap-checkbox intent, reviewer assignments with assignment IDs and axes, dispatch owner, and return consumer. `review.result.v0.1` binds the same context, digest, and surface, persisted reviewer assignments, independent axis results, findings, disposition, blocked reason, a mandatory `checklist_verdict` for `checklist_close`, and return consumer.

`review.axis-result-set.v0.1` binds the request digest, frozen surface, reviewer assignments, one current `review.axis-result.v0.1` for every assigned reviewer/axis pair, omitted additive axes with reasons, and aggregate consumer. Assignment identity is distinct from axis identity; duplicate Correctness results therefore remain separate and cannot be collapsed.

## Review selector

| Condition | `review_kind` | Required axes |
|---|---|---|
| Grilling supplied by `deep-decision-review` | `grill` | Standards, Specification |
| Design decision or runtime/instructional implementation slice | `implementation_logic` | Standards, Specification |
| Roadmap checklist closure or merge-PR review is being performed | `checklist_close` | Standards, Specification-compliance, Correctness |

Correctness is not a design or implementation-slice axis. A checklist verdict is forbidden for `implementation_logic`.

An explicit human review request may add named axes or a harder review surface but cannot remove either required implementation axis, remove any checklist-close axis, or create a checklist verdict outside `checklist_close`. A separately delegated single-axis worker returns only `review.axis-result.v0.1` to the required aggregate; it is not a complete Review terminal.

## Review state

| Input | State | Transition |
|---|---|---|
| exact subject request | `one_shot` | findings to one return consumer |
| request to keep review active | `persistent_requested` | explicit confirmation or `clarification_required` |
| confirmed persistent review | `persistent_active` | carry inside frozen scope until disable or scope terminal |
| request to disable persistent review | `persistent_disable_requested` | explicit confirmation or `clarification_required` |
| confirmed disable | `persistent_inactive` | no persistent carry |

One-shot review does not change persistent state. Persistent activation binds confirmation actor, scope, profile, activation operation, carry history, disable condition, and last consumer. An ambiguous request remains `one_shot`.

Persistent session review covers only the non-grill review surfaces this module owns — `implementation_logic` and `checklist_close`; during grill rounds the unconditional grill review is the only review layer. A keep-review-active request made during a grill round is a recorded explicit activation confirmation effective at the next non-grill operation; the recorded request persists as state if no non-grill operation follows.

## Reviewer topology

| Review surface | Reviewer assignments |
|---|---|
| Grilling supplied by `deep-decision-review` | one assignment: reviewer-1 Standards + Specification |
| Runtime/instructional implementation slice | reviewer-1: Standards; reviewer-2: Specification |
| Roadmap checklist closure or merge-PR review | reviewer-1: Standards + Correctness; reviewer-2: Specification-compliance + Correctness |

In either parallel mode, each assignment is one bounded reviewer subagent. Sequential modes execute the same assignments as separate current-context passes. `review_kind=grill` is accepted only with a persisted assignment set supplied by `deep-decision-review`; this module aggregates it and does not redispatch it. Missing, merged, or unpersisted assignments block aggregation.

## Axis contract

Every assigned reviewer receives the same frozen candidate digest and exact review surface and returns findings plus the next consumer. Only the aggregate may emit `disposition` or a checklist verdict. Every required assignment must resolve; `not_applicable` is invalid for a required axis.

| Axis | Scope |
|---|---|
| Standards | repository and engineering standards |
| Specification | candidate meaning or behavior against accepted requirements, specification, and producer/consumer contracts |
| Specification-compliance | exact roadmap and accepted specification fulfillment |
| Correctness | runtime behavior, integration, and declared validation |

Every finding binds `finding_id`, assignment ID, axis, severity, affected surface, violated authority or requirement, consequence, failure mechanism, required correction, finding status, and next consumer. Finding count and severity never substitute for assignment completion.

When the review surface contains code artifacts, the review runs the code disciplines: the assume-wrong stance, mechanism findings, and severity-ordered output.

## Axis procedures

| Axis | Required inspection |
|---|---|
| Standards | Check repository rules, applicable maintained baselines, trigger precision, prerequisites, inputs/outputs, authority, invariants, transitions, dependencies, consumers, recovery, contradictions, omissions, safety boundaries, and rendering integrity. |
| Specification | Trace each accepted requirement through trigger, prerequisites, versioned input/output, authority, operation/decision logic, invariants, recovery, completion, controller binding, re-entry, consumer, and reachable terminal; report missing, conflicting, ambiguous, or unusable links. |
| Specification-compliance | Trace every exact roadmap outcome to substantive implementation, integration, declared validation, and next consumer; reject proxy substitution and deferred required work. |
| Correctness | Inspect runtime behavior, state transitions, integration, failure paths, edge cases, and declared validation against the frozen candidate. |

## Operation

1. Bind `review_kind`, `review_context`, `review_state`, candidate digest, surface, authority sources, producer, consumer, mode, roadmap-checkbox intent, dispatch owner, and exact reviewer assignments with distinct assignment IDs.
2. When mode is `parallel-optimized` or `sequential-optimized`, complete one optimized preworkflow for the admitted review task before the first review operation; omit it in both normal modes.
3. For `review_kind=grill`, validate the deep-review dispatch owner and aggregate the supplied persisted assignment set without redispatch. Otherwise dispatch the exact reviewer topology for the selected review surface. Each assignment reviews the same frozen digest and surface. Aggregate only after every assigned result is current and persisted.
4. Re-enter Stage 0 and the originating family when present; otherwise return to the Route overlay/requester.
5. Return findings for `implementation_logic`.
6. For `checklist_close`, return `done`, `not_done`, or `blocked` only after both reviewer assignments and their duplicate Correctness results resolve; `checklist_verdict` is mandatory.

## Checklist closure

`checklist_close` requires the exact roadmap item, accepted design and specification, substantive implementation, integration point, declared validation, next consumer, and no proxy substitution.

Schemas, indexes, catalogs, migration records, scaffolds, placeholders, reports, test harnesses, passing tests, and evidence bundles do not replace required instruction logic or runtime behavior.

## State machine

```text
candidate -> implementation_review
implementation_review -> implementation_findings | blocked
close_proposal -> checklist_close_review
checklist_close_review -> done | not_done | blocked
```

Every transition requires a current candidate digest, complete required axes, valid authority, and a named consumer.

A `grill` request does not traverse this state machine: the module validates the deep-review dispatch owner, aggregates the supplied persisted assignment set, and returns it to `deep-decision-review`.

## Module registry

| Module | Versioned input | Versioned output | Actor and ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | planning only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact frozen review or Recovery request |
| `review.standards` | `review.request.v0.1` | `review.axis-result.v0.1` | findings only | family-local | `review_axis_blocked` | review aggregate |
| `review.specification` | `review.request.v0.1` | `review.axis-result.v0.1` | findings only | family-local | `review_axis_blocked` | implementation review aggregate |
| `review.specification_compliance` | `review.request.v0.1` | `review.axis-result.v0.1` | findings only | family-local | `review_axis_blocked` | checklist-close aggregate |
| `review.correctness` | `review.request.v0.1` | `review.axis-result.v0.1` | findings only | family-local | `review_axis_blocked` | checklist-close aggregate |
| `stage-0.review` | `review.axis-result-set.v0.1` | `review.result.v0.1` | findings and allowed verdict only | family-local | `review_not_admitted` | originating family, Route overlay/requester, or checklist owner |

## Invariants

- Every axis uses the same frozen candidate digest and review surface.
- Every assignment has a distinct assignment ID; missing, stale, merged, or unpersisted assignments cannot produce a verdict.
- Implementation review never produces a checklist verdict.
- Grilling is not dispatched by this module; `deep-decision-review` owns its one two-axis reviewer assignment.
- Reviewers cannot mutate candidates, roadmap state, or human gates.
- Persistent review carries only inside its confirmed frozen scope.
- A blocked close proposal reopens only after its blocker or candidate changes.
- Every checklist verdict traces each required outcome to substantive implementation, integration, validation, and consumer; proxy artifacts cannot satisfy the trace.

## Recovery

- Missing candidate or authority source: reject the review request.
- Stale digest: invalidate the result and review the current candidate.
- Missing axis: return `blocked`.
- Missing reviewer assignment or duplicate-axis collapse: return `blocked`.
- Proxy substitution: return `not_done` with the missing work.
- Reviewer mutation or approval attempt: discard the result and rerun findings-only.
- Stale or invalid persistent state: reject the transition and preserve the last valid state.

## Terminals

`implementation_findings`, `done`, `not_done`, and `blocked` are the only Review terminals.


