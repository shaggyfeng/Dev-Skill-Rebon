# Deep decision review

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, consumer, and execution profile admitted |
| Trigger | frozen grill question/options before a human answer, completed grill answer round, Route intake greenfield decision or value gap, admitted non-code Design decision, or explicitly activated persistent session review |
| Input | `design.grill-round.v0.1` with `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, and `review_phase=pre_answer` or `post_answer`, or `design.decision-review-input.v0.1` with `review_context=intake`, `design_decision`, or `session_mode`, `review_kind=implementation_logic`, `review_state=one_shot` or `persistent_active` as admitted, frozen digest, exact decision content, `review_surface`, declared `artifact_kinds`, `family_context`, authority sources, producer, consumer, and admitted execution mode; the current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget are bound by reference to `SKILL.md` |
| Output | `design.review-round.v0.1`: review context, review kind, review state, review phase, dispatch owner, persisted reviewer assignment set with stable assignment IDs, assigned axes, reviewer identity, result digest, and status, impact-first finding, independent Standards and Specification findings, `per_option_findings` with exactly one `{option_id, whats_wrong, why_it_matters, failure_scenario, bottom_line_fix}` tuple for every frozen option ID and no extras, candidate fixes/options, remaining gaps, ownership concerns, required corrections, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, consumer |
| Authority | findings only |
| Failure | `review_not_admitted` or `review_axis_blocked` |
| Consumer | Route intake, griller, Design, current family, or human disposition |

## Dispatch

| Review surface | Parallel modes | Sequential modes |
|---|---|---|
| Grilling: `grill_infrastructure` | one bounded `verification` reviewer subagent with Standards + Specification; persist one result | one current-context reviewer pass with Standards + Specification |
| Design or intake decision review: `implementation_logic` | two bounded `verification` reviewer subagents: one Standards and one Specification; persist both results | two current-context reviewer passes: one Standards and one Specification |
| Checklist closure or merge PR: `checklist_close` | owned by `review.md`, not this module | owned by `review.md`, not this module |

Each reviewer receives the frozen digest, context, phase, exact subject, assigned axes, output contract, read/tool budget, and consumer. In a parallel mode, a missing or unavailable reviewer subagent returns `review_not_admitted`; in a sequential mode, the assigned reviewer pass runs in the current context.

## Context

| Context | Trigger | Required behavior | Consumer |
|---|---|---|---|
| `grill_infrastructure` | every admitted grilling round | review the frozen question/options before the human answer and the exact answer packet afterward; return findings, candidate fixes/options, remaining gaps, expansion, and ownership | griller and human disposition |
| `intake` | Route intake identifies a greenfield decision or value gap requiring untangling | review the frozen subject and decision options; return findings only to Route intake or the human; Route may construct a Design entry after review | Route intake and human |
| `design_decision` | admitted non-persistent non-code Design decision | review the frozen decision packet with `review_state=one_shot`; return findings only | Design and human disposition |
| `session_mode` | `review.md` persistent review is explicitly activated and the subject is admitted | review the frozen consequential subject with the active persistent profile; `review.md` owns activation, confirmation, carry, and disable transitions | current family and human |

`grill_infrastructure` is always active during grilling: it covers grill rounds' pre-answer and post-answer packets only, and every other review kind keeps its own context and timing. It does not answer, facilitate, choose the next route, edit, approve, or resolve the human decision.
`session_mode` governs non-grill review only; during grill rounds the unconditional deep review is the only review layer. It does not activate or disable persistence; it consumes only the active `review.md` state and returns findings.

## Session activation

`session_mode` activates only when one of these predicates is true:

| Predicate | Transition |
|---|---|
| exact user phrase such as `review skill`, `deep review`, `adversarial review`, or `red-team this decision` | activate through `review.md` |
| deterministic offer naming session review followed by affirmative user acceptance | `activation_pending` → activate through `review.md` |
| model inference from topic importance, risk, or reviewer need | reject; remain inactive |

An offer without affirmative acceptance remains `activation_pending` and does not create persistent review. `grill_infrastructure` remains active regardless of session-mode state.

## Grill binding representation

The grilling-specific review relation is phase-scoped; the other review contexts retain their existing contract:

```text
grill_request
 -> pre_answer_pending | post_answer_pending | review_not_admitted
pre_answer_pending
 -> pre_answer_findings | review_not_admitted
pre_answer_findings
 -> human_answer
post_answer_pending
 -> post_answer_findings | review_not_admitted
post_answer_findings
 -> gap_classification | human_disposition
```

| Phase | Frozen packet | Required review binding | Next consumer |
|---|---|---|---|
| `pre_answer` | exact questions, options, evidence, `answer=null`, frozen digest | `review_context=grill_infrastructure`, `review_kind=grill`, one Standards + Specification assignment | griller presentation |
| `post_answer` | exact questions, options, evidence, answers, prior review digest | same context/kind and one two-axis assignment against the same frozen surface | gap classification or human disposition |
| either phase | current read-plan and coverage bindings | persist the review result before aggregation; no answer, route choice, or disposition by the reviewer | bound Grilling consumer |

A missing phase packet, stale digest, unavailable required assignment, or unusable read binding follows the existing `review_not_admitted` or `review_axis_blocked` route.

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result. An unusable binding follows the existing `review_not_admitted` or `review_axis_blocked` failure.

1. Freeze the exact round or decision packet, scope, and authority sources.
2. Build the working context from authoritative files, not recalled chat history.
3. For `design.grill-round.v0.1`, require `review_context=grill_infrastructure`, set `review_kind=grill`, and bind one reviewer assignment with Standards + Specification. With `review_phase=pre_answer`, copy the exact questions, options, evidence, and `answer=null`. With `review_phase=post_answer`, copy the exact questions, options, evidence, and answers. For `design.decision-review-input.v0.1`, require `review_context=intake` for a Route-intake greenfield gap, `design_decision` for non-persistent review, or `session_mode` for persistent review; require `review_kind=implementation_logic`; for `session_mode`, confirm the active `review.md` state. Copy the exact subject, unresolved decision candidate, constraints, and decision authority, `review_surface`, declared `artifact_kinds`, and `family_context`. Bind `producer=route.intake` and `consumer=Route intake or human` for `intake`; bind `producer=Design` and `consumer=Design or human disposition` for `design_decision`; bind `producer=current family` and `consumer=current family or human disposition` for `session_mode`. Construct `review.request.v0.1` with `review_context`, the admitted review kind, frozen digest, context-specific producer and consumer, declared review surface/artifacts, and one grill assignment or the implementation topology required by the context. Code artifacts must carry a validated code-artifact declaration and use the conditional hard-code review rules owned by `stage-0.review`; this module never infers them.
4. For grilling, dispatch one bounded reviewer assignment with Standards + Specification. For a Design decision or intake review that is not grilling, dispatch the two implementation assignments defined by `review.md`. Persist each result before aggregation; no inline substitution is permitted in a parallel mode. `review.md` aggregates the supplied assignment identities and does not redispatch them.
5. State the direct future consequences of the question or decision first; do not perform a whole-project audit. For every frozen option ID emit exactly one `per_option_findings` tuple with `option_id`, `whats_wrong`, `why_it_matters`, `failure_scenario`, and `bottom_line_fix`; emit no tuple for an option not in the frozen packet and do not select or emit a disposition. For `post_answer`, also evaluate each exact answer against its frozen question and options.
6. Compile all assigned axis results into `design.review-round.v0.1`, including the review phase, direct future consequences, what closed, what remains unsupported, what expanded, who owns each unresolved decision, and candidate fixes/options.
7. Return the compiled result without choosing the next grill route, answering for the human, or resolving the decision.
8. On a clarification request, produce an append-only follow-up explanation; the follow-up cannot decide, edit, publish, or override the active workflow.

## State machine

```text
review_request -> parallel_subagent_pending | sequential_review_pending
parallel_subagent_pending -> pre_answer_pending | post_answer_pending | decision_pending | intake_pending | session_review_pending | review_not_admitted
sequential_review_pending -> pre_answer_pending | post_answer_pending | decision_pending | intake_pending | session_review_pending | review_not_admitted
pre_answer_pending -> pre_answer_findings | review_not_admitted
post_answer_pending -> post_answer_findings | review_not_admitted
decision_pending -> decision_findings | review_not_admitted
activation_pending -> session_review_active | session_review_inactive
session_review_active -> session_findings | session_review_inactive
intake_pending -> intake_findings | review_not_admitted | review_axis_blocked
```

`pre_answer_findings` is required before the frozen question/options reach the human. `post_answer_findings` is required before gap classification or human disposition. Every state binds the same current digest and both required axes.

## Invariants

- Correctness is not an axis for this module.
- Reviewer cannot facilitate, answer, edit, approve, publish, resolve tickets, mutate roadmap state, or change gates.
- `pre_answer` review must complete before the griller presents the frozen question/options; `post_answer` review must complete before gap classification or human disposition.
- Every grilling packet has `review_context=grill_infrastructure`; missing context is not treated as generic review.
- Non-persistent Design decision packets use `review_context=design_decision`; persistent packets use `session_mode` only after `review.md` activation.
- Route-intake packets use `review_context=intake`; intake review cannot record, decide, publish, enter Design, or emit a handoff and returns findings to Route intake or the human. Only Route may construct a Design entry after the current `route.intake-result.v0.1` and `design.review-round.v0.1` both validate.
- `session_mode` requires an active persistent state from `review.md`; it cannot create, carry, or disable that state.
- `session_mode` requires an explicit activation predicate or affirmative offer acceptance; model inference never activates it.
- In either parallel mode, grilling has one reviewer subagent with Standards + Specification; non-grilling Design/intake review has two reviewer subagents, one Standards and one Specification. The main agent cannot replace a required assignment with inline review.
- Checklist-close and merge-PR review are owned by `review.md` and have two reviewer assignments: Standards + Correctness and Specification-compliance + Correctness.
- Every parallel reviewer assignment is a subagent result persisted against the frozen digest before the main agent consumes it.
- `impact_first` precedes option findings and remains bounded to the decision's direct future consequences.
- Every presented option has `What's wrong`, `Why it matters`, `Failure scenario`, and `Bottom-line fix`; the reviewer does not select an option.
- All assigned axes inspect the same frozen surface; `not_applicable` is invalid for any required axis.
- Each admitted input variant has exactly one request-construction branch.
- A decision remains unresolved until the human-disposition consumer produces the resolution.
- Code artifacts route to `stage-0.review` as `review_kind=implementation_logic`, whose review runs the assume-wrong stance, mechanism findings, severity ordering, and conditional hard-code checks only from validated `review_surface` and `artifact_kinds`; the deep reviewer does not review code itself.
- For packet rendering, `stage-0-write.md` carries the structure-selection rules for block-level rendering — headings, lead-ins, outline indentation lists, and tables; open it before writing a grill packet when the block forms are not already in use.

## Recovery

Missing context, questions, options, activation predicate, or active persistent state return `review_not_admitted`; missing answer for `post_answer` returns `review_not_admitted`; stale digest invalidates the result; missing axis returns `review_axis_blocked`; reviewer mutation discards the result. In a parallel mode, missing reviewer-subagent capability or failed role binding returns `review_not_admitted`; do not execute the review inline. Sequential modes use the assigned current-context passes.

## Completion

Complete only when the assigned reviewer topology resolves—one two-axis assignment for `grill`, or two one-axis assignments for non-grill Design/intake review—`review_surface`, `artifact_kinds`, and `family_context` are current, `impact_first` is stated when options are present, `per_option_findings` contains exactly one complete five-field tuple per frozen option ID and no extras, each tuple contains `What's wrong`, `Why it matters`, `Failure scenario`, and `Bottom-line fix`, and every finding names a consumer.
