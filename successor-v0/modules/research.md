# Research

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | explicit research or family evidence trigger; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | explicit research request or an originating family requests bounded evidence before deciding, planning, implementing, or teaching |
| Input | `research.request.v0.1`: `task_id`, `scope_digest`, original question wording, intended outcome, smallest useful increment, deliverable, evidence need, constraints, completion rule, mutable seams, source boundary, current `runtime.read-plan.v0.1`, coverage accumulator, remaining read frontier when sequential, read budgets, finite `retry_budget`, producer, and consumer; the request carries the predecessor result digest when it is a successor after invalidation |
| Output | `research.evidence.v0.1`: original question, additive lexical uplift and sources, professional positioning, source candidates and provenance, anchor candidates and non-compensatory checks, structural coverage, capability check, claims, citations, rejected candidates, limitations, no-match result, seam digest, `read_plan_digest`, `coverage_accumulator_digest`, remaining read frontier when sequential, remaining `retry_budget`, status, and consumer |
| Authority | evidence and citation findings only |
| Failure | `research_blocked`, `research_invalidated`, or `research_no_match` |
| Consumer | Route, Design, Plan, Work, Release, Teaching, Write on request, or Handoff |

## Operation

1. Bind the bounded question in the user's original wording, intended outcome, smallest useful increment, deliverable, evidence need, constraints, completion rule, and every mutable seam the result reads or gates. Before any source, artifact, or document content read, compile or validate the current `runtime.read-plan.v0.1` with deterministic source discovery, size estimates, declared coverage units, read budgets, coverage accumulator, ordered remaining frontier, synthesis consumer, and finite nonnegative `retry_budget`. A missing bound question or seam returns `research_blocked`; a source boundary that is unmeasured or uncertain is not admitted.
2. Perform additive Lexical Uplift: preserve the original wording, add sourced term relations, and retain unresolved ambiguity instead of replacing the user's meaning.
3. Perform Professional Positioning through task-relevant discipline, standard, bibliography, primary-record, historical, or applied-practice routes. Positioning explains where evidence belongs; it does not decide the task.
4. Select sources by task relevance, provenance, version, classification, and lineage. Prefer primary or first-party sources; when a primary source exists, a secondary summary is not evidence. Verify every citation against the source that owns the claim. Read only admitted coverage units; parallel modes persist each evidence fragment, while sequential modes persist each fragment, `runtime.read-frontier.v0.1`, and `runtime.handoff.v0.1` before the next unit.
5. Evaluate each Professional Anchor candidate through non-compensatory gates: positioning continuity, outcome and application fit, mandatory coverage, prohibited conflicts, source lineage, and limitations. Relevance precedes structural sufficiency; lexical similarity alone cannot select an anchor.
6. Classify structural coverage for each required element as exactly one of `exact`, `narrowed_compatible`, `supporting_only`, `absent`, `conflicting`, `unverified`, or `not_applicable`. Partial or supporting coverage is not complete.
7. Check tools, permissions, time, and compute separately from semantic source and anchor selection. Easier execution cannot select the wrong evidence or anchor.
8. Serialize the result against the declared mutable seams. If a same-seam mutation occurs before consumption, invalidate the current packet, preserve it as stale, emit `research_invalidated`, and compile a successor `research.request.v0.1` bound to the new seam digest before any family consumer can continue.
9. Return claims with claim-level citations, rejected and unresolved candidates, limitations, and a valid `no_match` result when no source or anchor satisfies the declared gates. Research does not decide, plan, execute, publish, or approve.
10. Render a report only when the consumer requests it through `stage-0.write`; the evidence packet remains the authoritative result.

## State machine

```text
research_requested -> question_bound | research_blocked
question_bound -> read_plan_bound | same_seam_changed
read_plan_bound -> lexical_uplifted | same_seam_changed
lexical_uplifted -> professionally_positioned | same_seam_changed
professionally_positioned -> sources_selected | research_no_match | same_seam_changed
sources_selected -> citation_verified | citation_verification_failed | research_no_match | same_seam_changed
citation_verified -> anchors_validated | research_no_match | same_seam_changed
citation_verification_failed -> sources_selected | research_blocked
anchors_validated -> coverage_classified | research_no_match | same_seam_changed
coverage_classified -> capability_checked | research_no_match | same_seam_changed
capability_checked -> evidence_ready | research_no_match | same_seam_changed
evidence_ready -> evidence_return | same_seam_changed
research_no_match -> no_match_return | same_seam_changed
evidence_return -> same_seam_changed
no_match_return -> same_seam_changed
same_seam_changed -> research_invalidated
research_invalidated -> successor_question_bound | research_blocked
successor_question_bound -> question_bound
```

## Invariants

- Original wording is preserved; normalization and Lexical Uplift are additive and every uplift edge is sourced.
- No source or document content is read outside a current `runtime.read-plan.v0.1`; every fragment, frontier, and result preserves the plan and coverage digests.
- Every material claim has a citation to its owning source; primary sources outrank secondary summaries when available.
- Professional Positioning and Professional Anchor selection use task relevance and structural sufficiency, not lexical similarity alone.
- Anchor gates are non-compensatory; a failure in relevance, coverage, conflict, lineage, or limitations cannot be offset by easier execution.
- Structural coverage uses the seven declared classifications; partial, supporting-only, absent, conflicting, or unverified coverage is never complete.
- Capability checks never change the semantic result.
- Research has no decision, design, planning, implementation, publication, or approval authority.
- Every research retry, reopen, or invalidation carries a finite nonnegative `retry_budget`; decrement and persist it before retry, and route zero to `research_blocked` or `research_invalidated` without another read.
- A same-seam change before consumption emits `research_invalidated`, preserves the prior packet as stale, and requires a successor request and packet against the new seam digest; no family may consume the old result.
- No-match is a valid evidence result when the declared source and anchor gates reject every candidate.
- A report is an optional rendering of the evidence packet and cannot replace it.

## Recovery

- Missing question, completion rule, source boundary, mutable seam, or consumer returns `research_blocked`.
- Unverified or conflicting citation enters `citation_verification_failed`, invalidates the affected claim, and returns the packet to source selection.
- A rejected anchor remains rejected with its reason; do not substitute a lexically similar candidate.
- Same-seam mutation before consumption returns `research_invalidated`, preserves the stale packet, and requires a successor packet against the new seam digest before the bound consumer re-enters.
- No source or anchor satisfies the gates: return `research_no_match` with rejected candidates and limitations.
- Missing capability evidence remains separate from semantic no-match and returns `research_blocked` when the task requires the missing capability.
- Every research retry, reopen, or invalidation carries a finite nonnegative `retry_budget`; decrement and persist it before retry, and route zero to `research_blocked` or `research_invalidated` without another read.

## Terminals

`evidence_return`, `no_match_return`, `research_blocked`, and `research_invalidated` are the only Research result states returned to a consumer; `research_no_match` is preserved as the typed no-match disposition inside `no_match_return`.

## Completion

Complete only when the bounded question, additive terminology, professional positioning, source lineage, anchor checks, seven-way coverage classification, separate capability check, claim citations, rejected candidates, limitations, no-match state when applicable, current seam digest, current read-plan and coverage digests, remaining `retry_budget`, and one consumer are present; no decision authority is implied.
