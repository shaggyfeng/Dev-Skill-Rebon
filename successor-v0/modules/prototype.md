# Prototype

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | bounded empirical uncertainty cannot be settled by repository inspection or deterministic analysis |
| Input | `design.prototype-question.v0.1`: `task_id`, `scope_digest`, one question, assumption, success/falsification/inconclusive criteria, budget, mutation authority and write scope, affected tickets, producer, consumer |
| Output | `design.prototype-evidence.v0.1`: question, assumption, branch, method, observations, classified result, limitations, reproduction instruction, affected tickets, `non_production: true`, status, consumer |
| Authority | bounded evidence only |
| Failure | `prototype_blocked` |
| Consumer | frontier recalculation, grilling, or human decision resolution |

## Operation

1. Reject input without one falsifiable question and explicit result criteria.
2. Select a logic branch for state, transition, data-shape, or interface behavior; select a UI branch for layout, hierarchy, or interaction-form comparison.
3. Use project-native tooling and the smallest isolated artifact inside the admitted write scope.
4. Avoid production persistence or mutation unless persistence is the question and a disposable target is authorized.
5. Surface all state relevant to the question and execute only cases required by the result criteria.
6. Record observations separately from interpretation and classify the result as `supported`, `falsified`, or `inconclusive`.
7. Return limitations, untested cases, reproduction instruction, and affected tickets.

## Invariants

- One prototype answers one question.
- Prototype and production artifacts remain visibly separate.
- Untested behavior is not evidence; inconclusive remains inconclusive.
- Prototype evidence cannot resolve normative consequences or authorize implementation.

## Recovery

Ambiguous questions return `prototype_blocked`; unauthorized writes require authorization or a non-mutating method; exhausted budgets return `inconclusive`; invalidated premises reopen affected tickets.

## Completion

Complete only when the method is reproducible, result and limitations are explicit, non-production status is bound, and one consumer is named.
