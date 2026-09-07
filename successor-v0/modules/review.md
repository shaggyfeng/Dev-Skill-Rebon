# Review

## Admission

| Field | Value |
|---|---|
| `module_id` | `stage-0.review` |
| `contract_version` | `devskill.module.v0.2` |
| `input` | candidate, exact surface, `review_surface`, declared `artifact_kinds`, `family_context`, authority sources, exact frozen subject, producer, consumer, review context, review kind, review state, mode, and reviewer assignment set |
| `output` | findings or checklist verdict |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| `authority` | findings and verdict only |
| `consumer` | originating family, Route overlay/requester, or checklist closure |

`review.request.v0.1` binds `review_kind`, `review_context`, `review_state`, candidate digest, exact surface manifest, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer contract, consumer contract, mode, roadmap-checkbox intent, one `slice_id` for `implementation_logic` when produced by Work, reviewer assignments with assignment IDs and axes, dispatch owner, return consumer, and remaining `retry_budget`. `review.result.v0.1` binds the same context, digest, surface, `review_surface`, `artifact_kinds`, `family_context`, current read-plan and coverage-accumulator digests, persisted reviewer assignments including any conditional Correctness assignment, independent axis results, findings, disposition, blocked reason, a mandatory `checklist_verdict` for `checklist_close`, remaining `retry_budget`, and return consumer.

`review.axis-result-set.v0.1` binds the request digest, frozen surface, reviewer assignments, one current `review.axis-result.v0.1` for every assigned reviewer/axis pair, omitted additive axes with reasons, and aggregate consumer. Assignment identity is distinct from axis identity; duplicate Correctness results therefore remain separate and cannot be collapsed.

`review.axis-evidence-fragment.v0.1` binds the request and read-plan digests, assignment ID and axis ceiling, coverage-unit ID, source IDs and digests, bounded observations, unresolved fields, omissions, status, remaining `retry_budget`, and synthesis consumer. It is evidence only and cannot emit the axis disposition, aggregate findings, or checklist verdict.

## Review selector

| Condition | `review_kind` | Required axes |
|---|---|---|
| Grilling supplied by `deep-decision-review` | `grill` | Standards, Specification |
| Design decision or runtime/instructional implementation slice | `implementation_logic` | Standards, Specification; add Correctness when the validated code-artifact declaration selects hard-code checks |
| Roadmap checklist closure or merge-PR review is being performed | `checklist_close` | Standards, Specification-compliance, Correctness |

Correctness is not a required design axis or default implementation-slice axis; a validated code-bearing declaration adds it as a separate implementation review assignment. A checklist verdict is forbidden for `implementation_logic`.

An explicit human review request may add named axes or a harder review surface but cannot remove either required implementation axis, remove any checklist-close axis, or create a checklist verdict outside `checklist_close`. A separately delegated single-axis worker returns only `review.axis-result.v0.1` to the required aggregate; it is not a complete Review terminal.

## Review declarations

The originating family declares `review_surface`, `artifact_kinds`, `family_context`, authority sources, and the exact frozen subject before Review admission. `review_surface` is an explicit contract value such as `core_logic`, `hybrid`, `ui`, or `documentation`; it is never inferred from a file extension, model wording, or unstated intent. A missing or conflicting declaration fails closed to the `hybrid` requirements and remains invalid for completion until the producer corrects the binding.

`artifact_kinds` declares only the artifacts actually present, using terms such as `code`, `controller_state`, `validation`, `schema`, `transport`, `authorization`, `ui`, `documentation`, or `decision`. Review derives the compact code-check profile from these declarations. `core_logic` is a review-surface label and does not itself select hard-code checks. Any declared artifact kind of `code`, `controller_state`, `validation`, `schema`, `transport`, or `authorization` selects the same additive Correctness assignment, including on `ui` or `documentation` surfaces. The fixed `checklist_close` topology always retains its Correctness components for checklist evidence; those components are not conditional hard-code activation. A conditional implementation Correctness assignment may be omitted only for a pure non-code `implementation_logic` surface, with its declaration-based reason. `hybrid` therefore cannot suppress checks for a load-bearing artifact, and a family cannot suppress a derived assignment. Git fixed-point rules apply only when the applicable family declares a Git operation.

Declarations are evidence and routing inputs, not reviewer permission. The family may add checks within its surface, but it cannot remove topology-mandated assignments or convert findings into implementation, approval, publication, or checklist authority.
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
| Runtime/instructional implementation slice | reviewer-1: Standards; reviewer-2: Specification; reviewer-3: Correctness when the validated code-artifact declaration selects hard-code checks |
| Roadmap checklist closure or merge-PR review | reviewer-1: Standards + Correctness; reviewer-2: Specification-compliance + Correctness |

The topology is fixed by `review_kind` and the declared review surface. Each assignment produces one independent axis result. When its read plan fits, one reviewer pass reads and reports. When it does not fit, assignment-bound evidence passes cover the partitioned surface and one read-free synthesis pass emits the axis result. Families may add bounded checks, but they cannot remove a required assignment, merge independent axis results or evidence coverage, or select a cross-axis winner. A conditional implementation Correctness assignment is omitted only when no validated code-bearing artifact declaration matches; the omission carries the exact surface, artifact kinds, and reason. The fixed Correctness components of `checklist_close` are never omitted. Every assignment carries the current remaining `retry_budget`.

## Axis contract

Every assigned reviewer receives the same frozen candidate digest, exact review-surface manifest, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, and returns findings plus the next consumer. Each assignment has its own complete coverage accumulator over that manifest. Only the aggregate may emit `disposition` or a checklist verdict. Every required assignment must resolve; `not_applicable` is invalid for required checklist axes or required implementation axes. The only allowed `not_applicable` Correctness result is the omitted additive implementation assignment for a validated pure non-code declaration, and it must carry the exact reason.

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

1. Bind `review_kind`, `review_context`, `review_state`, candidate digest, exact surface manifest, `review_surface`, declared `artifact_kinds`, `family_context`, authority sources, producer, consumer, mode, roadmap-checkbox intent, dispatch owner, exact reviewer assignments with distinct assignment IDs, and remaining `retry_budget`. Validate the declaration before deriving any conditional code checks; when the code-check predicate matches, add and persist a separate Correctness assignment. A Work-produced `implementation_logic` request without exactly one admitted `slice_id`, or with artifacts from another slice, returns `review_not_admitted`.
2. Before reviewer dispatch or external content reading, use deterministic listing, search, metadata, and count tools to compile `runtime.read-plan.v0.1` for the complete frozen surface. Bind file, byte, line, conservative token, read, tool, and output budgets plus declared coverage units. When the request already contains the complete bounded subject and no repository or document source is read, bind the read plan and coverage accumulator as `not_applicable`. In an optimized mode an applicable plan is an input to the task-scoped optimized preworkflow; in a normal mode it directly controls family-owned review dispatch.
3. When mode is `parallel-optimized` or `sequential-optimized`, complete one optimized preworkflow for the admitted review task before the first reviewer evidence pass; omit it in both normal modes.
4. For `review_kind=grill`, validate the deep-review dispatch owner and aggregate the supplied persisted assignment set without redispatch. Otherwise, for each assignment, either dispatch one fitting reviewer pass or partition the surface before reading into bounded assignment-bound passes that emit `review.axis-evidence-fragment.v0.1`, followed by one read-free synthesis pass that emits the axis result. Parallel modes persist every evidence fragment; sequential modes emit and validate a read-frontier handoff after every evidence unit. Every pass retains the assignment ID and axis ceiling.
5. Aggregate only after every assignment's coverage accumulator proves the complete frozen surface covered or explicitly blocked and every required axis result is current and persisted. Missing, stale, duplicate, undeclared-overlap, or incomplete coverage blocks aggregation.
6. Re-enter Stage 0 and the originating family when present; otherwise return to the Route overlay/requester.
7. Return findings for `implementation_logic`. The originating Work slice resolves findings, reruns affected review, verifies, and integrates before another slice is admitted.
8. For `checklist_close`, return `done`, `not_done`, or `blocked` only after both reviewer assignments, their duplicate Correctness results, and their complete coverage accumulators resolve; `checklist_verdict` is mandatory.

## Checklist closure

`checklist_close` requires the exact roadmap item, accepted design and specification, substantive implementation, integration point, declared validation, next consumer, and no proxy substitution. Before reading, partition an oversized multi-slice or phase surface by roadmap outcome and its linked implementation, integration, and verification evidence. Each assignment reviews every required outcome through bounded evidence passes and one read-free axis synthesis; no partial partition can emit the checklist verdict. A `not_done` result maps every finding to its affected outcome, slice or integrated scope, required correction, dependency order, verification seam, and exact recovery consumer so the originating family can compile bounded recovery work without reviewer mutation.

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
| `review.correctness` | `review.request.v0.1` | `review.axis-result.v0.1` | findings only | family-local | `review_axis_blocked` | implementation aggregate when its code-artifact declaration predicate matches, or the fixed checklist-close aggregate |
| `stage-0.review` | `review.axis-result-set.v0.1` | `review.result.v0.1` | findings and allowed verdict only | family-local | `review_not_admitted` | originating family, Route overlay/requester, or checklist owner |

## Invariants

- Every axis uses the same frozen candidate digest and exact declared review surface.
- Every assignment has a distinct assignment ID; missing, stale, merged, or unpersisted assignments cannot produce a verdict.
- Every assignment has a current read plan and complete independent coverage accumulator; evidence passes cannot cross assignment authority or replace the axis synthesis result.
- Work implementation review contains exactly one slice and resolves before the next slice is admitted; multi-slice batching is forbidden.
- Missing or conflicting review declarations fail closed to the hybrid requirements and cannot produce a complete result until the producer binding is corrected.
- Hard-code checks activate only from validated code-artifact declarations; a matching declaration adds a separate Correctness assignment to implementation review, and Git fixed-point rules never apply to non-Git review.
- Implementation review never produces a checklist verdict.
- Grilling is not dispatched by this module; `deep-decision-review` owns its one two-axis reviewer assignment.
- Reviewers cannot mutate candidates, roadmap state, or human gates.
- Persistent review carries only inside its confirmed frozen scope.
- A blocked close proposal reopens only after its blocker or candidate changes.
- Every checklist verdict traces each required outcome to substantive implementation, integration, validation, and consumer; proxy artifacts cannot satisfy the trace.

## Completion

Complete only when the declared review surface, artifact kinds, family context, authority sources, the current read plan and per-assignment coverage accumulators are complete or correctly `not_applicable`, fixed assignments, all required axis results are current, and remaining `retry_budget` is current; every implementation Correctness assignment is current when its code-artifact declaration predicate matches, otherwise its omitted additive assignment records the non-code reason; fixed checklist-close Correctness components are always current; and one consumer is named.

## Recovery

- Missing candidate or authority source: reject the review request.
- Missing or conflicting `review_surface`, `artifact_kinds`, or `family_context`: fail closed to hybrid requirements and return `review_not_admitted` until the producer repairs the binding.
- Stale digest: invalidate the result and review the current candidate.
- Missing axis: return `blocked`.
- Missing reviewer assignment or duplicate-axis collapse: return `blocked`.
- Oversized or uncertain surface before reading: partition under the current read plan; do not dispatch a broad reviewer.
- Reviewer budget exhaustion or incomplete coverage: preserve valid evidence fragments, compile a smaller successor read plan for the remaining frontier, decrement and persist `retry_budget`, and rerun only the affected evidence units and synthesis; an unchanged broad retry is forbidden. Zero budget returns `review_axis_blocked` for an axis or `review_not_admitted` for the aggregate.
- Proxy substitution: return `not_done` with the missing work.
- Reviewer mutation or approval attempt: discard the result and rerun findings-only.
- Stale or invalid persistent state: reject the transition and preserve the last valid state.

## Terminals

`implementation_findings`, `done`, `not_done`, and `blocked` are the only Review terminals.
