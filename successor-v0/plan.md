# Plan

## Admission

| Field | Value |
|---|---|
| `family_id` | `plan` |
| `stage` | `2` |
| `input` | approved Design packet with Route artifact-touch classification, convention-packet digest, maintenance-map digest when `brownfield`, repository snapshot, constraints, and current environment |
| `output` | approved specification, ticket set, and Work handoff |
| `authority` | structure, specification, ticket decomposition, and handoff only |
| `consumer` | Work or Design reopen |

Plan cannot change Design meaning. A semantic, scope, authority, normative, vocabulary, or empirical change reopens Design.

## Decision table

| Condition | Module | Result |
|---|---|---|
| admitted task in either optimized mode | `optimized.preworkflow` | current applied plan before task-specific work |
| admitted task in `parallel-normal` | family-local bounded dispatch | reader, writer, reviewer, verifier, or integrator output without optimized preworkflow |
| no architecture shaping required | `plan.architecture.classify` | `not_needed` |
| new solution structure required | `codebase-design` | proposal |
| architecture proposal requires human resolution | `plan.architecture.resolve` | architecture resolution |
| existing structure requires survey | `improve-codebase-architecture` | survey |
| bounded evidence required before architecture or specification | `research` | sourced evidence packet |
| survey remains unresolved | `plan.architecture.classify` | clarification or unresolved |
| proposal changes Design meaning | `plan.invalidate` | Design reopen |
| consequential Plan workflow or authority transfer requires paired views | `two-layer-development-planning` | Plan paired-view binding |
| architecture disposition and consequential Plan choices resolved | `to-spec` | candidate specification |
| specification candidate requires human resolution | `plan.specification.resolve` | specification resolution |
| exact specification digest approved | `to-tickets` | candidate ticket DAG |
| specification choices unresolved | `plan.specification.resolve` | human resolution |
| ticket graph incomplete | `to-tickets` | repartition |
| ticket set requires human resolution | `plan.ticket-set.resolve` | ticket-set resolution |
| approved ticket set requires a Work-start decision | `plan.work-start.compile` | Work-start candidate |
| approved ticket set and Work start authorized | `handoff` | Work handoff |
| Work start not authorized | `plan.terminal.compile` | hold |
| Work start requires human authorization | `plan.work-start.resolve` | Work-start resolution |
| held Work-start packet requires fresh environment or profile validation | `plan.held.revalidate` | revalidated handoff, hold, or `plan.invalidate` |

## Operation

1. Validate the Design handoff and repository snapshot.
2. In either optimized mode, load `successor-v0/modules/optimized-preworkflow.md` and complete it for every admitted Plan task before that task's first task-specific operation. Reuse is valid only for unchanged `{host, mode, family, task_id, scope_digest, next_consumer}`.
3. Emit the initial Plan graph and classify architecture; run one bounded survey when selected.
4. When bounded evidence is required before architecture, specification, ticket, or Work-start decisions, invoke `research` with producer `Plan`, the exact consuming node, and the current mutable seams; when constructing `research.request.v0.1`, bind the current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first Research content read and carry `read_plan_digest`, `coverage_digest`, and remaining frontier in the request/result. An unusable binding follows `research_blocked` or `research_invalidated` and cannot advance the Plan node. Consume the current evidence packet or typed no-match result; a `research_invalidated` result emits a successor research request bound to the new seam digest and reopens the affected Plan node against that successor.
5. Route architecture surveys to `plan.architecture.classify`; when a proposal is required, route it through `codebase-design` before human resolution.
6. Resolve architecture inside accepted Design meaning.
7. Compile the specification and bind every requirement to observable acceptance and a consumer.
8. Atomize the ticket DAG.
9. Resolve specification and ticket set through their distinct tagged resolver rows; compile the Work-start candidate from the approved ticket set, then resolve it through the Work-start resolver; never combine their inputs or approvals.
10. Emit a Work handoff, hold, revision, rejection, or Design reopen.
11. Re-enter Stage 0 and Plan before each consumer transition. A held-binding change never updates the held packet in place: when `held_binding_changed` and the dependency graph has an affected Plan node, invoke `plan.invalidate`, invalidate the held packet, resume pointer, minimal affected roots, and dependent descendants while preserving predecessor and proven-unaffected approved nodes, then pass the successor frontier to `plan.resume`.

## Parallel-normal dispatch

`parallel-normal` does not load or run optimized preworkflow. It may dispatch bounded reader, writer, reviewer, verifier, and integrator tasks when usable subagents exist.

| Role | Authority and output |
|---|---|
| Reader | Read-only bounded evidence packet with source pointers and unresolved gaps. |
| Writer | One bounded architecture, specification, ticket, or handoff candidate; no approval authority. |
| Reviewer | Findings only against one frozen candidate digest and declared Plan contract. |
| Verifier | Deterministic structure, coverage, graph, digest, and binding results; no mutation. |
| Integrator | One reconciled candidate from compatible reviewed outputs; no new Design meaning or approval. |

Each task binds one outcome, exact input scope, finite read/tool/output budget, versioned output, authority ceiling, failure route, and consumer. Dependency-independent read-only tasks may run together. Same-artifact writers and dependent tasks serialize. Worker completion status never substitutes for the required output or validation.

## Ticket contract

Every ticket binds exact scope, transformation, dependencies, blockers, write ownership, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, verification, recovery, resume pointer, and next consumer. The graph is acyclic. A Work handoff contains only approved scope and derived fields.

## State machine

```text
design_handoff -> research_requested | architecture_classified | clarification_required | design_reopened
research_requested -> research_evidence | research_no_match | plan_revision | design_reopened
research_evidence -> architecture_classified | architecture_resolution | specification_candidate | ticket_set_candidate | work_start_candidate | research_invalidated
research_no_match -> architecture_classified | architecture_resolution | specification_candidate | ticket_set_candidate | work_start_candidate | plan_revision | design_reopened
research_invalidated -> research_successor_bound | plan_revision | design_reopened
research_successor_bound -> research_requested
architecture_classified -> research_requested | architecture_resolution | specification_candidate
architecture_resolution -> architecture_resolved | plan_revision | design_reopened
architecture_resolved -> specification_candidate
specification_candidate -> research_requested | specification_resolution | plan_revision | plan_rejected
specification_resolution -> specification_approved | plan_revision | plan_rejected | design_reopened
specification_approved -> ticket_set_candidate
ticket_set_candidate -> research_requested | ticket_set_resolution | repartition | design_reopened
ticket_set_resolution -> ticket_set_approved | plan_revision | design_reopened
ticket_set_approved -> work_start_candidate
work_start_candidate -> research_requested | work_start_resolution
work_start_resolution -> work_held | work_handoff_candidate | plan_revision | design_reopened
work_held -> held_revalidation
held_revalidation -> work_held | work_handoff_candidate | plan_invalidation | design_reopened
plan_invalidation -> plan_resume | design_reopened
plan_resume -> work_held | work_handoff_candidate | plan_revision | design_reopened
work_handoff_candidate -> work_handoff_admitted | plan_revision | design_reopened
```

## Module registry

The Decision table, Operation, and state transitions own trigger selection. A `family-local` module file value means the Plan controller owns the operation.

| Module | Versioned input | Versioned output | Actor/authority ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | Read, decompose, review, and apply plan corrections only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | Exact current Plan or Recovery task |
| `plan.entry.validate` | `design.plan-handoff.v0.1` | `plan.entry.v0.1` plus initial `plan.graph.v0.1` | Admission binding only | `family-local` | `plan_entry_blocked`, clarification, or Design reopen | Architecture classifier |
| `plan.architecture.classify` | `plan.entry.v0.1` plus `plan.repository-evidence.v0.1` plus current `plan.graph.v0.1` | `plan.architecture-disposition.v0.1` plus successor `plan.graph.v0.1` | Classification only | `family-local` | Survey, clarification, unresolved, or Design reopen | Architecture producer or `to-spec` |
| `codebase-design` | `plan.codebase-design-input.v0.1` plus current `plan.graph.v0.1` | `plan.architecture-proposal.v0.1` plus successor `plan.graph.v0.1` | Proposal inside accepted Design meaning only | `successor-v0/modules/codebase-design.md` | `architecture_evidence_required` or `design_reopened` | `plan.architecture.resolve` or paired-view planning; `to-spec` after architecture resolution |
| `improve-codebase-architecture` | `plan.architecture-survey-input.v0.1` | `plan.architecture-survey.v0.1`, plus optional `plan.selected-candidate.v0.1` on the human's pick | Read-only survey; selection compilation on the human's pick | `successor-v0/modules/improve-codebase-architecture.md` | `architecture_survey_unresolved` or `design_reopened` | Architecture classifier or `codebase-design`; the selected-candidate result returns to the architecture classifier for Design-entry construction by Route |
| `domain-modeling` | `design.domain-input.v0.1` with the survey's crystallizing term | `design.domain-model.v0.1` | Candidate vocabulary and relations only; durable writes after human disposition | `successor-v0/modules/domain-modeling.md` | `domain_gap` or `domain_authority_conflict` | Plan architecture classifier |
| `research` | `research.request.v0.1` with Plan producer and consumer | `research.evidence.v0.1` | Researcher; sourced evidence only | `successor-v0/modules/research.md` | `research_blocked`, `research_invalidated`, or `research_no_match` | architecture, specification, ticket, or Work-start candidate |
| `plan.architecture.resolve` | `plan.architecture-proposal.v0.1` plus `plan.human-answer.v0.1` plus current `plan.graph.v0.1` | `plan.architecture-resolution.v0.1` plus successor `plan.graph.v0.1` | Exact human architecture resolution only | `family-local` | Plan revision or Design reopen | Paired-view binding or `to-spec` |
| `two-layer-development-planning` | current `plan.graph.v0.1` | `plan.paired-view-binding.v0.1` plus successor `plan.graph.v0.1` | Human-facing candidate Plan structure only | `successor-v0/modules/two-layer-development-planning.md` | `cross_view_blocked` | `to-spec` |
| `to-spec` | `plan.specification-input.v0.1` plus current `plan.graph.v0.1` | `plan.specification.v0.1` plus successor `plan.graph.v0.1` | Candidate specification until exact human approval | `successor-v0/modules/to-spec.md` | `plan_revision` or `design_reopened` | Human specification resolution, then `to-tickets` |
| `to-tickets` | `plan.ticket-generation-input.v0.1` containing `plan.specification.v0.1` with `status=specification_approved` and complete Review declarations plus current `plan.graph.v0.1` | `plan.ticket-set.v0.1` with `status=ticket_set_candidate`, every ticket's complete Review declaration set, and successor `plan.graph.v0.1` | Candidate Work decomposition only | `successor-v0/modules/to-tickets.md` | `ticket_repartition` or `plan_revision` | `plan.ticket-set.resolve` |
| `plan.specification.resolve` | `plan.specification.v0.1` with `status=specification_candidate` plus `plan.specification-answer.v0.1` plus current `plan.graph.v0.1` | successor `plan.specification.v0.1` with `status=specification_approved` and `plan.specification-resolution.v0.1` plus successor `plan.graph.v0.1` | Exact human specification decision only | `family-local` | Plan revision, rejection, or clarification | `to-tickets` or Plan terminal compiler |
| `plan.ticket-set.resolve` | `plan.ticket-set.v0.1` with `status=ticket_set_candidate` and complete Review declarations on every ticket plus `plan.ticket-set-answer.v0.1` plus current `plan.graph.v0.1` | successor `plan.ticket-set.v0.1` with `status=ticket_set_approved`, the same complete Review declarations on every ticket, and `plan.ticket-set-resolution.v0.1` plus successor `plan.graph.v0.1` | Exact human ticket-set decision only | `family-local` | Repartition, Plan revision, or clarification | `plan.work-start.compile` for the approved branch or Plan terminal compiler for non-approved terminals |
| `plan.work-start.compile` | `plan.ticket-set.v0.1` with `status=ticket_set_approved`, complete Review declarations on every ticket, plus current `plan.graph.v0.1`, `plan.environment-snapshot.v0.1`, and `development-execution-profile.v0.1` | `plan.work-start-candidate.v0.1` with approved ticket-set digest, preserved complete Review declarations, environment/profile bindings, exclusions, authority, resume pointer, and consumer | Candidate compilation only | `family-local` | Hold, Plan revision, or clarification | `plan.work-start.resolve` |
| `plan.work-start.resolve` | `plan.ticket-set.v0.1` with `status=ticket_set_approved`, complete Review declarations on every ticket plus `plan.work-start-candidate.v0.1` and `plan.work-start-answer.v0.1` plus current `plan.graph.v0.1` | `plan.work-start-resolution.v0.1` with `status=work_start_authorized` or `work_start_held`, preserved complete Review declarations, and successor `plan.graph.v0.1`; the held branch also emits `plan.held-packet.v0.1` preserving environment/profile bindings and `plan.resume-pointer.v0.1` | Exact human Work-start authorization only | `family-local` | Hold, Plan revision, or clarification | Handoff, held revalidation, or Plan terminal compiler |
| `handoff` | `runtime.boundary-input.v0.1` containing `plan.specification.v0.1` with `status=specification_approved`, `plan.ticket-set.v0.1` with `status=ticket_set_approved`, `plan.work-start-resolution.v0.1` with `status=work_start_authorized`, each slice's `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs, exact frozen subject, and the Route artifact-touch/convention/maintenance digests | `runtime.handoff.v0.1` binding the immutable `plan.work-handoff.v0.1` with those lineage bindings | Context transfer only; no approval or Work authority | `successor-v0/modules/handoff-and-boundaries.md` | `handoff_blocked` or `boundary_revalidation_required` | Work entry |
| `plan.held.revalidate` | `plan.held-packet.v0.1` plus `plan.environment-snapshot.v0.1` plus `development-execution-profile.v0.1` plus `plan.work-start-request.v0.1` plus current `plan.graph.v0.1` | `plan.held-revalidation.v0.1` plus optional `plan.work-handoff.v0.1` plus successor `plan.graph.v0.1` | Revalidation and exact newly resolved Work scope only | `family-local` | `plan.invalidate`, Plan revision, or Design reopen | Handoff, `plan.invalidate`, or terminal compiler |
| `plan.invalidate` | `plan.change-event.v0.1` plus `plan.dependency-graph.v0.1` plus current `plan.graph.v0.1` | `plan.invalidation.v0.1` plus successor `plan.graph.v0.1` | Revocation and recovery routing only | `family-local` | Broader reopen | Plan recovery or Design reopen |
| `plan.resume` | current `plan.family-packet.v0.1` plus its `plan.resume-pointer.v0.1` and current `plan.graph.v0.1` | `plan.resume-operation.v0.1` preserving the packet and graph digests | Route only | `family-local` | Broader invalidation or Design reopen | Exact affected Plan module |
| `plan.terminal.compile` | latest `plan.graph.v0.1` from the Plan controller plus the applicable approved specification, approved ticket-set, and Work-start resolution outputs plus `plan.paired-view-binding.v0.1` when applicable plus optional `plan.work-handoff.v0.1` | `plan.family-packet.v0.1` preserving the current `plan.resume-pointer.v0.1` | Terminal-specific only | `family-local` | Affected Plan step or Design reopen | Work, Design, clarification, hold, or exit |

## Invariants

- Research evidence is a Plan input only; it cannot alter accepted Design meaning or authorize a Work handoff, and same-seam invalidation returns to the affected Plan node with a successor packet.
- Architecture, specification, ticket-set, and Work-start resolutions remain distinct.
- The ticket graph is acyclic and every ticket has a consumer, verification, and recovery route.
- `plan.entry.validate` emits the initial graph; the Plan controller emits a successor graph after every graph-changing transition, and every downstream consumer uses the latest graph digest.
- A Work handoff contains only approved scope and derived fields.
- Work-start compilation produces the only candidate consumed by Work-start resolution; a held resolution produces the held packet and resume pointer required by revalidation.
- Held-binding changes use `plan.invalidate -> plan.resume`; invalidation is limited to the held packet, resume pointer, minimal affected Plan roots, and dependent descendants, while predecessor and proven-unaffected approved nodes remain intact.

## Recovery

- Changed accepted source invalidates affected future dispatch.
- Preserve proven-unaffected nodes only when dependency analysis proves independence.
- Missing exact binding returns typed recovery.
- A stale or invalidated Research result never advances architecture, specification, ticket, or Work-start state; it re-enters through the affected node with a bounded retry budget.
- When a held binding changes and an affected Plan node exists, `plan.invalidate` creates a successor graph for the minimal affected roots and descendants; `plan.resume` consumes it, and the held packet is never updated in place.
- No specification, ticket, or Work handoff may bypass human resolution required by its contract.

## Terminals

`work_handoff_admitted`, `work_held`, `plan_revision`, `plan_rejected`, `design_reopened`, and `clarification_required` are the only Plan terminals.

