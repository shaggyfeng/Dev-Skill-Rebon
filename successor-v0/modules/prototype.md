# Prototype

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | bounded empirical uncertainty cannot be settled by repository inspection or deterministic analysis |
| Input | `design.prototype-question.v0.1`: `task_id`, `scope_digest`, one question, assumption, success/falsification/inconclusive criteria, budget, mutation authority and write scope, affected tickets, producer, consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `prototype_blocked` or `prototype_invalidated` failure. |
| Output | `design.prototype-evidence.v0.1`: question, assumption, branch, method, observations, `result_class` (`supported`, `falsified`, or `inconclusive`), limitations, reproduction instruction, affected tickets, `non_production: true`, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, envelope `status`, consumer |
| Authority | bounded evidence only |
| Failure | `prototype_blocked` or `prototype_invalidated` |
| Consumer | frontier recalculation, grilling, or human decision resolution |

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

1. Reject input without one falsifiable question and explicit result criteria.
2. Select a logic branch for state, transition, data-shape, or interface behavior; select a UI branch for layout, hierarchy, or interaction-form comparison. Apply the branch defaults below unless the falsifiable question and result criteria justify another shape.
3. Use project-native tooling and the smallest isolated artifact inside the admitted write scope. Give the experiment one run instruction and keep the artifact visibly throwaway.
4. Avoid production persistence or mutation unless persistence is the question and a disposable target is authorized.
5. Surface all state relevant to the question and execute only cases required by the result criteria.
6. Record observations separately from interpretation and classify the semantic result as `result_class=supported`, `result_class=falsified`, or `result_class=inconclusive`; keep envelope `status` separate and restricted to the global status values.
7. Before returning or allowing a consumer to use the evidence, revalidate the assumption, source, environment, and declared seam digest. A changed premise emits `prototype_invalidated` and a successor question binding; stale evidence cannot advance as current.
8. Return limitations, untested cases, reproduction instruction, and affected tickets.

## Branch defaults

Logic prototypes default to a portable logic core, a thin hand-driven shell, the full state relevant to the question, and one run instruction. Keep the answer-bearing logic pure where possible; terminal or UI control flow remains a disposable shell.

UI prototypes default to the existing host surface, three radically different structural variants and no more than five, a stable visible switcher, no production mutation, explicit winner capture, and cleanup after the evidence is recorded. Use a shareable visible control when the host supports it. A different number or shape requires the question and result criteria to justify it.

Capture observations separately from interpretation, retain the relevant state after each action or variant switch, and remove temporary artifacts after the decision unless the evidence is explicitly retained as a separate primary source. Do not add framework-specific examples, polish, persistence, or production wiring.

## State machine

```text
prototype_requested -> prototype_running | prototype_blocked
prototype_running -> prototype_evidence | prototype_blocked | prototype_invalidated
prototype_evidence -> prototype_invalidated | frontier_recalculated | human_disposition
prototype_invalidated -> successor_question_bound | prototype_blocked
successor_question_bound -> prototype_requested
```

## Invariants

- One prototype answers one question.
- Prototype and production artifacts remain visibly separate.
- Logic and UI defaults remain compact and portable; alternatives require a falsifiable question and result-criteria justification.
- Prototype evidence and cleanup remain separate from production implementation; a prototype is never promoted directly.
- Untested behavior is not evidence; inconclusive remains inconclusive.
- A premise, source, environment, or declared seam change invalidates active or returned evidence and requires a successor question binding before any consumer uses it.
- Prototype evidence cannot resolve normative consequences or authorize implementation.

## Recovery

Ambiguous questions return `prototype_blocked`; unauthorized writes require authorization or a non-mutating method; exhausted budgets return `result_class=inconclusive`; invalidated premises return `prototype_invalidated` with a successor question binding and reopen affected tickets.

## Completion

Complete only when the method is reproducible, result and limitations are explicit, non-production status is bound, and one consumer is named.
