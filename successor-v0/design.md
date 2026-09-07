# Design

## Admission

| Field | Value |
|---|---|
| `family_id` | `design` |
| `stage` | `1` |
| `input` | Route intake result with artifact-touch classification, problem, value gap, constraints, evidence, dependencies, open decisions, current `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, and the current convention-packet digest; a `brownfield` entry also requires the current maintenance-map digest |
| `output` | `mind_cleared` or human-resolved Design packet |
| `authority` | decision frontier and candidate design only |
| `consumer` | Plan `to-spec` or clarification |

Design starts when meaning, scope, architecture direction, trade-offs, or acceptance decisions are unresolved. Human decisions remain authoritative.

## Decision table

| Gap | Module | Failure route |
|---|---|---|
| unresolved narrow decision | `grilling` | `clarification_required` |
| ordered decision refinement | `grilling` | recalculate frontier |
| new decision dimension | `grilling` | split frontier ticket |
| distinct decision owners | `grilling` | preserve ownership conflict |
| grill answer requires review | `deep-decision-review` | return findings to human disposition |
| vocabulary or relation gap | `domain-modeling` | return disputed meaning |
| testable empirical uncertainty | `prototype` | bounded retry or clarification |
| bounded evidence required before a Design decision | `research` | sourced evidence packet |
| structural representation required | `stage-0.think` | `structural_gap` |
| consequential workflow or authority decision | `two-layer-development-planning` | cross-view block |
| frontier closed without durable design | `design.packet.compile` | `mind_cleared` |
| frontier and views closed | `design.packet.compile` | packet approval |

## Operation

1. Bind the Design entry and initialize stable frontier tickets.
2. In an optimized mode, validate and load the `optimized.preworkflow` row and complete it before the admitted Design task or ticket group's first task-specific operation.
3. If the registry trigger requires bounded evidence before a Design decision, invoke `research` with producer `Design` and the exact consuming ticket or candidate; when constructing `research.request.v0.1`, bind the current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first Research content read and carry `read_plan_digest`, `coverage_digest`, and remaining frontier in the request/result. An unusable binding follows `research_blocked` or `research_invalidated` and cannot advance the frontier. Do not resolve or advance that decision until the current evidence packet or typed no-match result is present. A `research_invalidated` result emits a successor research request bound to the new seam digest and reopens the affected ticket against that successor.
4. Select a registry row only after its trigger, prerequisites, versioned input, authority ceiling, module file, failure route, and consumer validate. A module name alone does not admit execution.
5. Load the selected runtime module before module execution; execute `family-local` rows under this controller only.
6. Resolve eligible tickets through grilling, domain modeling, prototype, two-axis deep-decision review, and human disposition; every non-grill Review request carries the ticket's `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, and consumer. Non-grill decisions enter the same review path from an unresolved candidate.
7. Recalculate the frontier after each disposition.
8. Synchronize Workflow Evolution and Structure and Authority views.
9. Compile `mind_cleared` or a candidate packet.
10. Resolve the exact packet digest through `design.packet.resolve` before Plan handoff.
11. Re-enter Stage 0, Design, and every active module after delegation, review, recovery, compaction, invalidation, or a scope or consumer change; revalidate current bindings before every consumer transition.

## Module loading

| Trigger | Required module |
|---|---|
| eligible human-owned decision | `successor-v0/modules/grilling.md` |
| frozen grill question/options before a human answer or completed grill answer round | `successor-v0/modules/deep-decision-review.md` and `successor-v0/modules/review.md` with `review_kind=grill` |
| admitted non-code Design decision | `successor-v0/modules/deep-decision-review.md` and `successor-v0/modules/review.md` with `review_kind=implementation_logic` |
| vocabulary, concept-boundary, relation, ownership, or rule dispute | `successor-v0/modules/domain-modeling.md` |
| bounded empirical uncertainty not settled by inspection or deterministic analysis | `successor-v0/modules/prototype.md` |
| bounded evidence request before a Design decision | `successor-v0/modules/research.md` |
| consequential workflow or authority decision spanning operations, branches, sessions, artifacts, or actors | `successor-v0/modules/two-layer-development-planning.md` |
| admitted structural-thinking or unslop need | `successor-v0/modules/stage-0-think.md` |
| human resolves the exact packet digest for Plan handoff | `design.packet.resolve` |
| admitted optimized Design task | `successor-v0/modules/optimized-preworkflow.md` before the task's first task-specific operation |

Every answered grill round requires independent Standards and Specification findings against the same frozen digest. Correctness is not a default Design axis; a runtime or instructional implementation declaration with code-bearing artifacts derives a separate Correctness assignment through `review.md`. Roadmap-checkbox closure remains owned by `review.md` and is outside this Design decision-review binding.

## Ticket contract

Each ticket binds one question, owner, prerequisites, evidence, options, human answer, findings, resolution, dependents, exclusions, reopen condition, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, consumer, and status.

Ticket states:

`open`, `blocked`, `answered_unreviewed`, `reviewed_unresolved`, `human_resolved`, `human_excluded`, `reopened`.

Human answers to grill tickets invoke `successor-v0/modules/deep-decision-review.md` with `review_kind=grill` before resolution. Non-grill Design decisions use `implementation_logic`. A prototype records its question, assumption, method, result, limitations, affected tickets, and `non_production: true`.

## State machine

```text
entry -> frontier_open
frontier_open -> research_requested
research_requested -> research_evidence | research_no_match | research_blocked
research_blocked -> research_requested | clarification_required
research_evidence -> frontier_recalculated | research_invalidated
research_no_match -> frontier_recalculated | clarification_required
research_invalidated -> research_successor_bound | clarification_required
research_successor_bound -> research_requested
frontier_open -> grill_round
frontier_open -> decision_candidate
frontier_open -> prototype_requested
prototype_requested -> prototype_running | prototype_blocked
prototype_blocked -> prototype_requested | clarification_required
prototype_running -> prototype_evidence | prototype_blocked | prototype_invalidated
prototype_evidence -> frontier_recalculated | human_disposition | prototype_invalidated
prototype_invalidated -> prototype_successor_bound | clarification_required
prototype_successor_bound -> prototype_requested
human_disposition -> frontier_recalculated
grill_round -> answered_unreviewed
answered_unreviewed -> reviewed_unresolved
decision_candidate -> decision_review_input
decision_review_input -> reviewed_unresolved
reviewed_unresolved -> human_disposition | human_resolved | human_excluded | blocked
human_resolved | human_excluded -> frontier_recalculated
frontier_recalculated -> grill_round | packet_candidate | mind_cleared
packet_candidate -> packet_resolution | packet_reopened
packet_resolution -> design_packet_approved | packet_reopened | clarification_required
packet_reopened -> frontier_open
```

## Module registry

| Module | Versioned input | Versioned output | Actor/authority ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | Preworkflow roles; read, decompose, review, and apply plan corrections only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact current Design or Recovery task |
| `design.frontier.initialize` | `design.entry.v0.1` with current artifact-touch classification, convention-packet digest, maintenance-map digest when `brownfield`, review declarations, and authority sources | `design.frontier.v0.1` plus zero or more `design.decision-candidate.v0.1` records for non-grill decisions | Design controller; candidate frontier state only | `family-local` | `design_clarification` | grilling, decision-review input, or packet compiler |
| `grilling` | `design.frontier.v0.1` | `design.grill-round.v0.1` | Griller; facilitation only | `successor-v0/modules/grilling.md` | `clarification_required` or `frontier_blocked` | deep-decision review, frontier calculator, or packet compiler |
| `design.decision-review-input.compile` | `design.decision-candidate.v0.1` plus `design.frontier.v0.1` | `design.decision-review-input.v0.1` carrying `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, producer, consumer, and the frozen decision packet | Design controller; freeze unresolved non-code decision content only | `family-local` | `review_not_admitted` | deep-decision review |
| `deep-decision-review` | `design.grill-round.v0.1` or `design.decision-review-input.v0.1` | `design.review-round.v0.1` | Reviewer; Standards and Specification findings only | `successor-v0/modules/deep-decision-review.md` | `review_not_admitted` or `review_axis_blocked` | human disposition |
| `domain-modeling` | `design.domain-input.v0.1` | `design.domain-model.v0.1` | Domain modeler; candidate vocabulary and relations only | `successor-v0/modules/domain-modeling.md` | `domain_gap` or `domain_authority_conflict` | grilling, paired-view planning, or packet compiler after human resolution |
| `prototype` | `design.prototype-question.v0.1` | `design.prototype-evidence.v0.1` | Prototyper; bounded non-production evidence only | `successor-v0/modules/prototype.md` | `prototype_blocked` or `prototype_invalidated` | frontier recalculation, grilling, or human decision resolution |
| `research` | `research.request.v0.1` with Design producer and consumer | `research.evidence.v0.1` | Researcher; sourced evidence only | `successor-v0/modules/research.md` | `research_blocked`, `research_invalidated`, or `research_no_match` | Design decision candidate, Route, Write, or Handoff |
| `two-layer-development-planning` | `design.decision-resolution.v0.1` | `design.paired-view-binding.v0.1` | Design planner; candidate human-facing design structure only | `successor-v0/modules/two-layer-development-planning.md` | `cross_view_blocked` | packet compiler, then Plan after human packet resolution |
| `stage-0.think` | `think.input.v0.1` | `think.brief.v0.1` | Structural thinker; candidate representation only | `successor-v0/modules/stage-0-think.md` | `structural_gap`, `structural_authority_conflict`, or `structural_invalidated` | grilling, review, or `stage-0.write` |
| `design.packet.compile` | `design.frontier.v0.1` plus `design.paired-view-binding.v0.1` when applicable | `design.packet.v0.1` or `mind_cleared.v0.1` | Design controller; candidate packet or no-artifact terminal only | `family-local` | `design_incomplete` | `design.packet.resolve` for `design.packet.v0.1`; Design terminal and Route requester for `mind_cleared.v0.1` |
| `design.packet.resolve` | `design.packet.v0.1` plus `design.packet-answer.v0.1` | successor `design.packet.v0.1` with `status=design_packet_approved` and the exact human resolution digest | Exact human packet resolution only | `family-local` | `packet_not_approved` or clarification | `handoff` |
| `design.packet.reopen` | `design.packet.v0.1` plus `design.affected-ticket-set.v0.1` | successor `design.frontier.v0.1` | Design controller; affected-node invalidation only | `family-local` | `reopen_blocked` | frontier initializer |
| `design.decision.resolve` | `design.decision-review-input.v0.1` plus `design.review-round.v0.1` plus `design.human-answer.v0.1` | `design.decision-resolution.v0.1` plus successor `design.frontier.v0.1` | Exact human decision disposition only | `family-local` | `human_gate_pending` or `frontier_blocked` | two-layer planning, frontier recalculation, or packet compiler |
| `handoff` | `runtime.boundary-input.v0.1` containing successor `design.packet.v0.1` with `status=design_packet_approved`, paired-view binding when applicable, and the current Route intake/convention/maintenance digests | `runtime.handoff.v0.1` carrying `design.plan-handoff.v0.1` with those lineage bindings | Handoff actor; context transfer only | `successor-v0/modules/handoff-and-boundaries.md` | `packet_not_approved`, `handoff_blocked`, or `boundary_revalidation_required` | Plan `to-spec` |

## Invariants

- Every dependent ticket waits for resolved or explicitly excluded predecessors.
- Packet approval does not replace ticket resolution.
- Only `design.packet.resolve` can produce an approved packet for Plan handoff.
- `mind_cleared.v0.1` bypasses packet resolution and returns directly as the no-artifact Design terminal.
- Accepted packets are immutable; changes create successor lineage.
- Cross-view mismatch blocks packet compilation.
- Research evidence remains separate from Design meaning and cannot resolve a human decision; a same-seam change invalidates the evidence and reopens the affected ticket through a successor research packet.
- Non-grill decision candidates remain unresolved until the deep review result and exact human disposition produce `design.decision-resolution.v0.1`.
- Design does not emit implementation specifications, tickets, code, or execution authorization.

## Recovery

- Missing meaning or ownership returns clarification.
- Failed review returns the answer to human disposition.
- Invalidated packets reopen only affected frontier tickets and dependents.
- A stale or invalidated Research or Prototype result never advances the frontier; the affected ticket re-enters with a current successor scope and bounded retry budget.
- A blocked Research or Prototype result retries only through its bounded successor request while retry budget remains; otherwise it returns `clarification_required` and never advances the frontier.
- A structural Think result with `status=invalidated` and `failure_route=structural_invalidated` re-enters Stage 0 before any Design consumer and never advances the frontier.

## Terminals

`mind_cleared`, `design_packet_approved`, `clarification_required`, and `design_reopened` are the only Design terminals.

