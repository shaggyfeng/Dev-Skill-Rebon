# Two-layer development planning

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | consequential Design or Plan workflow/authority structure spans multiple operations, branches, sessions, artifacts, or actors |
| Input | `design.decision-resolution.v0.1` or `plan.graph.v0.1`, carrying resolved/unresolved bindings, constraints, owners, workflow decisions, producer, and consumer |
| Output | matching `design.paired-view-binding.v0.1` or `plan.paired-view-binding.v0.1`: Workflow Evolution View, Structure and Authority View, shared IDs, consistency result, unresolved transfers/conflicts, status, consumer |
| Authority | candidate human-facing design structure only |
| Failure | `cross_view_blocked` |
| Consumer | Design packet compiler or Plan specification compiler, matching the input variant |

## Operation

1. Select the Design or Plan contract variant from the admitted input; never convert one variant into the other implicitly.
2. Build the smallest complete Workflow Evolution View with entries, operations, branches, retries, checkpoints, recovery, terminals, consumers, and truthful status.
3. For every consequential step or transfer, add a Structure and Authority row with stable ID/status, semantic input, representation, source, operation actor, output, validator/decision actor, consumer, authority gained, and authority not gained.
4. Exclude helpers that affect no public seam, authority boundary, handoff, or independently testable outcome.
5. Validate every workflow edge against one structure row and detect missing inputs, undefined outputs, cycles, wrong actors, unjustified authority, and unusable consumer inputs.
6. Apply accepted decisions and review corrections to both views in the same revision.
7. Block the matching packet or specification compiler on any cross-view mismatch.

## Invariants

- Shared steps and transitions use identical stable IDs in both views.
- Every branch reaches a consumer, typed block, recovery route, human gate, or terminal.
- Every consequential arrow transfers one named bounded structure.
- Proposal, validation, confirmation, acceptance, publication, planning approval, mutation, and execution authorization remain distinct.
- Candidate, approved-unimplemented, implemented, and historical states remain distinct.

## Recovery

- Cross-view mismatch returns `cross_view_blocked`; missing consumer or authority reopens the owning Design or Plan item.
- A cycle reports its minimal members and reopens their owners.
- Changed accepted input creates successor lineage and invalidates only affected nodes.

## Completion

Complete only when all consequential workflow elements have matching structure-and-authority rows, shared IDs and transitions validate, authority effects are explicit, and one consumer matching the admitted Design or Plan variant is bound.
