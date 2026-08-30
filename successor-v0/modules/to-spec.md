# Specification compilation

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | architecture disposition and consequential Plan choices are resolved |
| Input | `plan.specification-input.v0.1`: `task_id`, `scope_digest`, accepted Design IDs, either current `plan.architecture-resolution.v0.1` or `plan.architecture-disposition.v0.1` with `status=not_needed`, paired-view binding, constraints, authority, interfaces/states/test seams, producer, consumer |
| Output | `plan.specification.v0.1`: purpose/scope, actors/outcomes, requirement IDs, interface/state/error/recovery contracts, acceptance mappings, verification, non-functional constraints, exclusions/gates, status, consumer |
| Authority | candidate specification until exact human approval |
| Failure | `plan_revision` or `design_reopened` |
| Consumer | `plan.specification.resolve`, then `to-tickets` |

## Operation

1. Validate exactly one architecture branch: current architecture resolution, or `not_needed` from `plan.architecture.classify`.
2. Trace every accepted Design ID into one or more specification requirements.
3. Define behavior through observable inputs, transitions, outputs, failures, and recovery.
4. Bind ownership, interfaces, dependencies, authority, and non-functional constraints.
5. Assign stable requirement and acceptance IDs.
6. Map every requirement to observable acceptance, verification seam, and consumer.
7. Mark conditional fields `applicable` or `not_applicable` with evidence.
8. Return unresolved meaning, scope, authority, vocabulary, or empirical choices upstream instead of inventing them.
9. Validate structure, coverage, currency, consistency, and authority.

## Invariants

- Plan does not reinterpret Design or present an unresolved requirement as resolved.
- `not_needed` is accepted only from the current architecture-classification output; every other architecture path requires a current resolution.
- Implementation details require a specification purpose.
- Specification approval, ticket approval, and Work-start authorization remain separate.

## Recovery

- Mechanical omission returns `plan_revision` to this module.
- Architecture gaps route to architecture resolution.
- Meaning, scope, authority, vocabulary, normative, or empirical gaps return `design_reopened`.

## Completion

Complete only when every Design ID is specified, explicitly excluded, or reopened, and every requirement has acceptance, verification, owner, authority, and consumer.
