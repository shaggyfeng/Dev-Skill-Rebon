# Stage 0 / Route

## Admission

| Field | Value |
|---|---|
| `family_id` | `route` |
| `stage` | `0` |
| `input` | current request, active-stage state, admitted profile, optional current table/projection/registry state, optional lifecycle sidecar |
| `output` | one Route terminal, including an overlay return when selected |
| `authority` | classification, bounded attention, and handoff only |
| `consumer` | active family, overlay consumer, clarification, or `live_fallback` |

Run this family for every engineering request.

## Trigger table

| Condition | Module | Result |
|---|---|---|
| Explicit stage request and prerequisites valid | `route.vibe` | requested stage candidate |
| Applicable family signal from `SKILL.md` and entry prerequisites valid | `route.vibe` | family stage candidate |
| Design grilling request or `design.grill-round.v0.1` | `deep-decision-review` | `review_context=grill_infrastructure`, `review_kind=grill` |
| Roadmap checklist closure | `stage-0.review` | `review_kind=checklist_close` |
| Merge-PR review | `stage-0.review` | `review_kind=checklist_close` |
| Runtime/instructional implementation review | `stage-0.review` | `review_kind=implementation_logic` |
| Other explicit review, audit, or specification check | `stage-0.review` | classify as `implementation_logic` or `checklist_close`, otherwise `clarification_required` |
| Explicit request to keep review active | `stage-0.review` | persistent review request |
| Explicit request to disable active review | `stage-0.review` | persistent review disable request |
| Explicit unslop or structural-thinking request, or admitted structural-slop finding | `stage-0.think` | structural brief |
| Explicit diagnosis request with a valid Work entry and bounded symptom | `work.diagnose` | diagnosis result |
| Explicit prototype request with a valid Design frontier and one bounded question | `prototype` | prototype evidence |
| Semantic rewrite after meaning is bound | `stage-0.write` | presentation candidate |
| A report, user-facing message, or agent-facing document without a bound packet | `stage-0.write` | presentation candidate |
| Eligible lifecycle mutation sidecar | `stage-0.attention-table-maintenance` | validated table replacement |
| Valid table replacement | `stage-0.vibe-projection.compile` | projection replacement and frozen return binding |
| Applicable unsuppressed failure signal | `route.attention` | bounded findings |
| Active stage and no applicable signal | `route.vibe` | active-stage continuation |
| No active stage, no direct overlay, and no eligible family signal | `route.intake` | bounded classification or clarification |
| Invalid supplied table, registry, or projection | `live_fallback` | preserve last valid live router |

All compatible non-review overlays run in dependency order. At most one typed review overlay is admitted per operation. Success signals do not activate Attention. Attention requires an applicable failure signal and a valid registry composition.

Review trigger precedence is exclusive for one operation: `design.grill-round.v0.1` or explicit grilling selects `deep-decision-review` with `review_kind=grill`; roadmap-checklist and merge-PR intent select `stage-0.review` with `review_kind=checklist_close`; runtime/instructional implementation selects `stage-0.review` with `review_kind=implementation_logic`; another explicit review request must classify as `implementation_logic` or `checklist_close` before dispatch, otherwise return `clarification_required`. A specialized review trigger suppresses the generic review trigger for that operation.


## Operation

1. Freeze the operation scope as the first ordered action: bind `operation_id`, task scope, current stage, request, table/projection/registry state, optional lifecycle sidecar, and admitted profile, and record the frozen-scope digest; the route-spec state is read once, at this freeze. All later routing consumes the frozen scope without refreezing.
2. In an optimized mode, validate and load the `optimized.preworkflow` row and complete it before the Route task's first task-specific operation. A separately dispatched overlay has its own `task_id` and preworkflow.
3. Evaluate every registry trigger.
4. Validate the selected row's prerequisites, versioned input, authority ceiling, module file, failure route, and consumer. A module name alone does not admit execution.
5. Load the selected runtime module after admission and before module execution; execute `family-local` rows under this controller only.
6. If supplied table, registry, or projection state is invalid, preserve the last valid live router and return `live_fallback`.
7. When no direct overlay, no active stage, and no family signal exists, run `route.intake` once to classify the bounded engineering intent from the request and current repository authority. If the result is a greenfield decision or value gap requiring untangling, compile `design.decision-review-input.v0.1` with `review_context=intake`, load `successor-v0/modules/deep-decision-review.md`, and return its findings to Route intake or the human before a Design entry. Retain the `route.intake-result.v0.1` and `design.review-round.v0.1`; only Route may construct a Design entry after both current typed results validate. The reviewer cannot emit that handoff; unresolved intent returns clarification.
8. Apply an eligible lifecycle table replacement, then projection compilation, before returning to its frozen lifecycle consumer.
9. For `work.diagnose` and `prototype`, validate the Work or Design prerequisite before loading the module; if absent, do not activate the module and return `clarification_required`. Compose all other compatible explicit overlays in dependency order; unresolved consumer conflicts return clarification.
10. Evaluate the current-stage signal. When `route.attention` is applicable, perform exactly one ordered Attention composition against one frozen scope:

    ```text
    scope <- freeze_scope(input, durable_state)
    matches <- match_current_rules(scope, input)
    failures <- suppress_by_well_typed_exclusion_relations(matches)
    impact <- controller_derive_impact(failures)
    admitted <- allocate_budget(rank(impact))
    findings <- run_acyclic_registry_composition(admitted)
    ```

    Pass the frozen scope digest, the freeze-time and match-time table digests with the structured route-spec-digest pair, matched rule IDs, ranked budget, and findings to the Route compiler. Do not rematch against mutable scope or run a second Attention composition in the same operation.
11. Compile exactly one Route terminal from the stage candidate, overlay results, and Attention findings.
12. Re-enter this controller and every active module after delegation, review, recovery, compaction, invalidation, or a scope or consumer change; revalidate current bindings before returning to a family or consumer.

## Module loading

| Module | Load binding |
|---|---|
| `route.mode-activation` | Use `mode-gate.md` during admission before Route task work. |
| `stage-0.think` | Load `successor-v0/modules/stage-0-think.md` only after its explicit structural trigger or admitted structural-slop finding and `think.input.v0.1` validate. |
| `stage-0.write` | Load `successor-v0/modules/stage-0-write.md` only after bound meaning, stable semantic IDs, authority labels, consumer, and `write.input.v0.1` validate; the unbound-output trigger row admits with only the envelope, authority, and consumer validated, and the packet fields declared absent. |
| `markdown-tables-and-diagrams` | Load `successor-v0/modules/markdown-tables-and-diagrams.md` from `stage-0.write` only when a Markdown table or Mermaid diagram is selected or repaired. |
| `talk-like-king` | Load `successor-v0/modules/talk-like-king.md` when the output is long human-facing prose — a README, documentation, essays, announcements, any long-form writing for human readers — or user chat; run its voice acquisition before drafting. |
| `stage-0.review` | Load `successor-v0/modules/review.md` only after a typed `review.request.v0.1` and its frozen subject, profile, producer, and consumer validate; apply the `review_kind` topology for implementation, checklist, and merge-PR review. |
| `work.diagnose` | Load `successor-v0/modules/diagnosing-bugs.md` only after a valid Work entry, frozen symptom, bounded environment, and `work.diagnosis-request.v0.1` validate. |
| `prototype` | Load `successor-v0/modules/prototype.md` only after a valid Design frontier, one falsifiable question, and `design.prototype-question.v0.1` validate. |
| `optimized.preworkflow` | Load `successor-v0/modules/optimized-preworkflow.md` only in an admitted optimized mode and complete it before the Route task's first task-specific operation. |
| `deep-decision-review` | Load `successor-v0/modules/deep-decision-review.md` for grilling, Route-intake untangling, or an admitted Design decision only after `design.grill-round.v0.1` or `design.decision-review-input.v0.1` and its `review_context` validate; return findings only to its bound consumer. |

`stage-0.think` and `stage-0.write` procedures are owned only by their module files. This controller owns their trigger composition, lifecycle order, and Route terminals.

## Attention

`route.attention` receives a frozen scope, the match-time table digest, the freeze-time table digest, matched rule IDs, and a finite resource budget. It returns ranked findings and one registry composition. It cannot edit ordinary project artifacts or replace the active family terminal. A lifecycle sidecar binds `current_table_path`, `projection_path`, and `frozen_return_consumer`; the two paths must be exact and disjoint.

### Route spec

The route spec is the normative attention text of this file; its digest is the SHA-256 of `stage-0-route.md`. It owns:

- the closed operator set: exact-value for scalar dimensions, subset-or-equal for set-valued dimensions (the empty set is degenerate), and segment-prefix for path-like dimensions (the empty or root prefix is degenerate); a degenerate declaration is malformed;
- the name-to-operator-to-datum assignment: no names are assigned at release; the first governed amendment batch-assigns the de facto dimension-name space, later amendments admit names case by case, and every assigned name denotes one frozen-scope datum;
- the total order over assigned names: assignment order — the first assigned name holds position one and each later assignment appends the next position; a rename's successor inherits the predecessor's position unless the amendment re-adjudicates explicitly;
- the attention-composable registry subset and its dependency order: `work.diagnose`, then `stage-0.think`; the finding-to-module mapping is spec-declared, so mutable-table rule IDs never select executing modules;
- the rename transition format: the amendment states the alias window; the old name stays resolvable and deprecated-but-assigned rows match normally inside the window; after expiry the old name is unassigned and rejected at the write path.

A governed spec amendment is a change to this spec recorded through the workflow's decision process stating the prior and new spec digests; every such amendment changes the route-spec digest.

### Ordered chain

1. Freeze: the controller freezes the scope as the first ordered action. The route-spec state is read once per operation, at the freeze; amendments apply between operations, never mid-operation.
2. Match: against the match-time table under the deterministic applicability predicate — a rule is applicable iff it declares at least one scope dimension, every declared dimension is present in the frozen scope, and every declared value equals the frozen value under that dimension's bound match semantics. Rules are evaluated independently; no ordering, ranking, or filtering at applicability. A rule declaring no dimensions is never applicable. A declared dimension missing from the frozen scope excludes only that rule — never route-level failure or altered routing. Undeclared scope dimensions are inert. The match returns the complete unordered set of applicable rules, failure and success patterns alike. The executor introduces no applicability judgment and no interpretation outside the bound semantics; identical scope, table, and bound semantics yielding divergent match sets is a defect, not discretion.
3. Suppress: an applicable success rule suppresses an applicable failure rule only through the explicit exclusion relation the success rule declares — minimum content: the excluded failure rule's rule ID. The relation is well-typed only when both rules' derived subjects are the same vocabulary name. The derived subject is the order-minimum, under the route spec's total order over assigned vocabulary names, of the rule's declared name set; the declared list is a set and declaration order carries no semantics. Relations invalidated after writing apply silently non-applied.
4. Impact: each post-suppression failure's impact is the deterministic aggregate of its satisfied binary factor flags — authority reach, dependency fanout, reversibility, persistence, execution proximity — declared in the failure rule's declaration; the write path validates flag declarations at write time. The derivation is a pure function of inputs the operation already holds; no new reads, no judgment; scope breadth alone is never impact.
5. Budget: rank failures by impact descending; ties break by subject vocabulary name per the spec's total order, then rule ID. Admit findings up to the resource budget the host profile declares in the admitted input; record below-cutoff failures as budgeted-out IDs and discard them from the findings artifact.
6. Compose: map the admitted set to canonical registry module IDs from the spec-declared attention-composable subset and its dependency order — one pass in declared order, no recursion, no re-entry; validate each row before execution; a failing row contributes its typed failure-route result to the findings bundle and the composition continues; the composition's terminal consumer is the Route compiler, and row consumers are honored at the compiler's routing step; the findings artifact carries each executed module's result plus the freeze-time witness and the match-time digests whenever the findings path is taken.

### Vocabulary and table rules

Table rules reference vocabulary names only. The route spec owns the closed operator set, canonical definitions with degenerate-value behavior, the name-to-operator-to-datum assignment, and the total order over assigned names. New or renamed names enter only by governed spec amendments; the assigned-name set is monotone within a rename window and shrinks only at window expiry — until the first rename it only grows; the write path rejects a rule whose dimension name is not assigned, never defaults, and validates each declared exclusion relation at write time: both derived subjects are the same vocabulary name, and the referenced failure rule exists in the table. A row the controller cannot match — malformed declaration, degenerate value, expired unassigned name — is never applicable: excluded like any non-matching rule, with no quarantine, flag, or table annotation; deprecated-but-assigned rows match normally through their alias window.

### Record and drift

The match record is the dispatched input instance plus its Route decision-record mirror. It carries exactly two table digests — the freeze-time digest as drift witness and the match-time digest as match-input attestation — with the route-spec digest as a structured pair inside the match-time attestation slot; no third digest slot exists; no spec content is retained. The `route.attention-input.v0.1` and `route.attention-findings.v0.1` contracts are amended in place: `matched failure patterns` carries the as-matched pre-suppression matched rule IDs, failure and success patterns alike, and the freeze-time table digest is an added field; the bound meanings supersede the prose field names. On a readable match-time table whose digest differs from the freeze-time digest, proceed: match against the match-time table; never abort, re-freeze, mutate the table, or begin a new ordered cycle within the operation; the activation gate is evaluated against the match-time table, and freeze-time applicability and suppression status is unrecoverable under digest-only attestation — that unrecoverability is recorded alongside the drift witness. Drift adjudication is advisory — no controller gate, no new terminal, no in-line consumer obligation; post-hoc adjudication is by the human through the durable evidence chain. This disposition governs drift discovered inside the operation's own match step; the regenerate-against-current-digest recovery doctrine is unchanged for stale bases outside the operation, and a post-audit re-issue is a new operation. The Route decision record binds matched rule IDs (as-matched, pre-suppression), suppression relations, satisfied-factor impact records, budgeted-out IDs, budget state, and the digests, written at their production stages, never back-filled.

### Empty and invalid paths

Empty match set: the ordered chain completes over empty collections; no findings artifact, no typed indicator, no attention-derived compiler input; ordinary terminal path; the Route decision record carries the explicit empty list. All-suppressed, success-only, and all-budgeted-out operations invoke no module and land on the ordinary terminal; the Route decision record carries their outcome. A no-signal operation carries no match-stage entry; a triggered-but-empty one carries the explicit empty list. Supplied table state failing validation routes to `live_fallback` with the last valid live router preserved. Unavailable or unreadable optional state skips the composition for the operation; ordinary routing continues and the Route decision record carries an invalidity marker. Supplied means present in the operation's declared state at admission; unavailable means absent or unreadable at match time.

### Trigger discharge and terminals

The trigger row "Applicable unsuppressed failure signal" is admitted at signal level; the composition's match and suppression outcome verifies "applicable unsuppressed". The module is invoked only when the admitted set is non-empty. The compiler resolves exactly one terminal by fixed precedence: `live_fallback` for any invalid supplied state; `clarification_required` for missing prerequisites or conflicting consumers; `overlay_return` for valid typed overlay results; `attention_findings` only when a findings artifact exists; `stage_handoff` for an explicit stage request with validated prerequisites; `active_stage_continuation` as the default. An overlay's subject was frozen at admission, so its results return before anything else claims the operation; a findings artifact outranks a stage handoff, which is honored only when nothing else claims the operation. Every terminal carries the Route decision record.

## Review overlay

`stage-0.review` receives a frozen subject, review profile, candidate digest, producer contract, and consumer contract. It returns findings, or a checklist verdict for `review_kind=checklist_close`. Use `successor-v0/modules/review.md`.

## Decision table

| State | Trigger | Transition |
|---|---|---|
| `initializing` | valid family signal and prerequisites | `route_compilation` |
| `initializing` | no eligible family signal | `intake_classification` |
| `steady` | active stage and no signal | `active_stage_continuation` |
| `direct_entry` | explicit stage, review, unslop, diagnosis, or prototype request | named overlay or stage validation |
| `review_overlay` | typed review request admitted | `overlay_return` |
| `review_overlay` | review kind, assignment, or consumer missing or invalid | `clarification_required` |
| `attention_requested` | applicable unsuppressed failure | `attention_running` |
| `attention_running` | bounded composition complete | `route_compilation` |
| `route_compilation` | one legal consumer | named handoff |
| `route_compilation` | missing prerequisite or conflicting consumer | `clarification_required` |
| `intake_classification` | one eligible family signal | `route_compilation` |
| `intake_classification` | greenfield decision or value gap requires untangling | `intake_deep_review` |
| `intake_deep_review` | `route.intake-result.v0.1` and `design.review-round.v0.1` validate | `route_compilation` |
| `intake_deep_review` | findings returned to Route intake or human but a typed result is missing or stale | `clarification_required` |
| `intake_deep_review` | `review_not_admitted` or `review_axis_blocked` | `clarification_required` |
| `intake_classification` | unresolved or unsupported engineering intent | `clarification_required` |
| `review_persistent_requested` | explicit persistent-review request | confirmation or `clarification_required` |
| `review_persistent_active` | confirmed profile inside frozen scope | carried review overlay |
| `review_persistent_disable_requested` | explicit disable request | confirmation or `clarification_required` |
| `review_persistent_inactive` | confirmed disable | `overlay_return` |
| any | invalid supplied table, registry, or projection | `live_fallback` |
| any | stale or skipped scope freeze on re-entry | re-freeze on the operation's current input; carried Attention state bound to the stale scope is invalidated, never consumed |

## Module registry

| Module | Versioned input | Versioned output | Actor/authority ceiling | Module file | Failure route | Consumer |
|---|---|---|---|---|---|---|
| `route.vibe` | `route.vibe-input.v0.1` | `route.vibe-result.v0.1` | Route controller; signal classification only | `family-local` | `route_classification_blocked` | overlays, Attention, or Route compiler |
| `route.attention` | `route.attention-input.v0.1` | `route.attention-findings.v0.1` | Route controller; evidence allocation and findings only | `family-local` | `attention_blocked` | Route compiler |
| `route.intake` | `route.intake-request.v0.1` with no active stage and no direct overlay | `route.intake-result.v0.1` | Route controller; bounded engineering classification only | `family-local` | `intake_clarification_required` | Route compiler or clarification |
| `route.mode-activation` | `route.mode-evidence.v0.1` | `development-execution-profile.v0.1` | Mode gate; mode classification only | `mode-gate.md` | `mode_not_admitted` | exact current Route task |
| `stage-0.think` | `think.input.v0.1` | `think.brief.v0.1` | Structural thinker; candidate representation only | `successor-v0/modules/stage-0-think.md` | `structural_gap` or `structural_authority_conflict` | active family, review overlay, or `stage-0.write` |
| `stage-0.write` | `write.input.v0.1` | `write.candidate.v0.1` | Writer; presentation only | `successor-v0/modules/stage-0-write.md` | `write_not_admitted` or `write_semantic_delta` | active family or review overlay |
| `markdown-tables-and-diagrams` | `render.input.v0.1` | `render.candidate.v0.1` | Renderer; presentation structure only | `successor-v0/modules/markdown-tables-and-diagrams.md` | `render_not_admitted` or `render_source_conflict` | `stage-0.write` or originating family |
| `stage-0.review` | `review.request.v0.1` | `review.result.v0.1` | Reviewer; findings or checklist verdict only | `successor-v0/modules/review.md` | `review_not_admitted` or `review_axis_blocked` | originating family, Route overlay/requester, or checklist owner |
| `work.diagnose` | `work.diagnosis-request.v0.1` | `work.diagnosis-result.v0.1` | Diagnosis module; reproduction, read-only inspection, and temporary instrumentation only | `successor-v0/modules/diagnosing-bugs.md` | `diagnosis_blocked` or `diagnosis_environment_required` | `work.execution`, Work recovery, or architecture survey |
| `prototype` | `design.prototype-question.v0.1` | `design.prototype-evidence.v0.1` | Prototype module; bounded non-production evidence only | `successor-v0/modules/prototype.md` | `prototype_blocked` | grilling or human decision resolution |
| `stage-0.attention-table-maintenance` | `attention.lifecycle-mutation-sidecar.v0.1` | `attention.table-replacement.v0.1` plus `attention.lifecycle-return-binding.v0.1` | Exact-path current-table writer only | `family-local` | `table_update_rejected` | projection compiler |
| `stage-0.vibe-projection.compile` | `attention.table-replacement.v0.1` plus `attention.lifecycle-return-binding.v0.1` | `attention.projection-replacement.v0.1` plus `attention.lifecycle-return-binding.v0.1` | Exact-path deterministic projection writer only | `family-local` | `projection_compile_blocked` | frozen lifecycle terminal consumer |
| `route.compile` | `route.compile-input.v0.1` | `route.terminal.v0.1` | Route controller; routing only | `family-local` | `intake_clarification_required` or `live_fallback` | named family, overlay consumer, clarification, or `live_fallback` |
| `optimized.preworkflow` | `optimized.preworkflow-input.v0.1` | `optimized.applied-plan.v0.1` | Preworkflow roles; read, decompose, review, and apply plan corrections only | `successor-v0/modules/optimized-preworkflow.md` | `preworkflow_not_admitted` | exact current Route or Recovery task |
| `deep-decision-review` | `design.grill-round.v0.1` or `design.decision-review-input.v0.1` with a valid `review_context` | `design.review-round.v0.1` | Reviewer; findings and candidate corrections only | `successor-v0/modules/deep-decision-review.md` | `review_not_admitted` or `review_axis_blocked` | bound Design, Route-intake, or human consumer; Route compiler only for validated intake results |

## Invariants

- Every active registry row satisfies the completion predicate in `SKILL.md`.
- Attention runs at most once per Route operation and cannot replace the family terminal.
- The Route controller freezes scope before matching, and the chain then applies explicit suppression, controller-derived impact, ranked budget allocation, and finite acyclic registry composition in that order.
- The match record carries exactly two table digests with the structured route-spec-digest pair; no third digest slot exists; the route-spec state is read once per operation at the scope freeze; Route decision-record values are written at their production stages, never back-filled.
- Table maintenance and projection compilation preserve the frozen lifecycle consumer and reproduce the sidecar's exact disjoint paths.
- `attention.lifecycle-mutation-sidecar.v0.1` binds `{origin_family, lifecycle_id, terminal_digest, frozen_return_consumer, current_table_path, projection_path, envelope_digest}`; missing or overlapping paths return `table_update_rejected`.
- Missing optional attention state does not block ordinary family routing.
- One-shot review never changes persistent review state; persistent state carries only inside its confirmed scope and ends on disable or scope terminal.

## Recovery

- Apply the `SKILL.md` re-entry contract before any family or overlay return.
- Reject invalid table mutations and preserve the current table.
- Preserve the prior projection when projection compilation fails and return a recovery result.
- Return unresolved stage intent to one human clarification.
- Preserve the last valid live router for invalid table, registry, or projection state and return `live_fallback`.

## Terminals

`active_stage_continuation`, `stage_handoff`, `overlay_return`, `attention_findings`, `clarification_required`, and `live_fallback` are the only Route terminals.

