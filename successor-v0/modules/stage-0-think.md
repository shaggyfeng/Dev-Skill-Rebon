# Structural thinking

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | explicit structural-thinking/unslop request or admitted structural-slop finding |
| Input | `think.input.v0.1`: `task_id`, `scope_digest`, subject, reasoning need, claims, evidence, constraints, authority sources, known gaps, producer, consumer |
| Output | `think.brief.v0.1`: representation blocks with stable IDs, exposed claims and relations, gaps, authority boundaries, validation, status, consumer |
| Authority | candidate structural representation only |
| Failure | `structural_gap`, `structural_authority_conflict`, or `structural_invalidated` |
| Consumer | active family, `stage-0.review`, or `stage-0.write` |

## Representation selector

| Reasoning need | Minimum representation |
|---|---|
| sequence or algorithm | mechanism or pseudocode |
| hidden implication or quantifier | claim inventory and argument map |
| input/output shape | schema and interface contract |
| behavior over time | state machine and transition table |
| stable guarantee | contract and invariants |
| conditional outcomes | complete decision table |
| dependencies or ownership | dependency, authority, and interface maps |
| conflicting sources | conflict and synthesis maps |
| evidence sufficiency | evidence map, coverage map, and epistemic audit |
| failure handling | recovery map and validation checklist |
| persuasion or framing | rhetorical map plus evidence audit |

## Operation

1. Validate the frozen subject, reasoning need, authority sources, and consumer.
2. Select only the representations required by the selector; combine representations when one cannot expose every material relation.
3. Bind every claim and relation to its source or label it `inferred`, `unsupported`, `conflicting`, or `unknown`.
4. Expose circular reasoning, unfalsifiable claims, missing cases, undefined interfaces, authority gaps, and unusable consumer inputs.
5. Validate each representation against the declared reasoning need and output one bounded brief.

## Invariants

- Representation does not establish truth, acceptance, approval, implementation authority, or checklist closure.
- No unsupported relation becomes a fact.
- Every material claim retains a source or unresolved status.
- Rhetorical mapping activates only when framing or persuasion affects the subject.
- An oversized subject splits by independently consumable relation group.

## Recovery

- Missing meaning returns `structural_gap`; conflicting authority returns `structural_authority_conflict`.
- Insufficient evidence remains an explicit gap; oversized scope splits by independently consumable relation group.
- Changed scope or consumer returns envelope `status=invalidated` with `failure_route=structural_invalidated` to Stage 0 before retry.

## Completion

Complete only when the reasoning need is covered, every unresolved structural gap is explicit, representation-specific validation passes, bindings are current, and one consumer is named. Otherwise return the typed failure without advancing the family.
