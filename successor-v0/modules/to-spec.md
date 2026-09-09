# Specification Compilation

## Trigger and boundary

Run after accepted Design and required architecture results exist. A user naming the module is sufficient. It compiles a candidate specification; it does not reinterpret accepted Design or authorize Work.

## Specification structure

| Section | Content |
|---|---|
| Problem and scope | Human problem, intended outcome, constraints, and exclusions |
| Behavior | Actors, inputs, transitions, outputs, failures, and recovery |
| Design and interfaces | Ownership, interfaces, dependencies, authority, and applicable non-functional constraints |
| Acceptance and verification | Observable acceptance, verification seam, and consumer for every requirement |
| Open items | Meaning, scope, vocabulary, authority, or empirical questions returned upstream |

The caller chooses the project specification location. This module returns a candidate structure; it does not publish or choose a record location.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Accepted Design and architecture results are ready | [Plan](../plan.md) | Candidate specification to Plan |
| Candidate requirements must become Work slices | [To-Tickets](to-tickets.md) | Candidate specification as ticket input |
| Architecture detail is missing | [Codebase Design](codebase-design.md) or [Improve Codebase Architecture](improve-codebase-architecture.md) | Candidate architecture or survey to the specification |
| Requirement meaning is unresolved | [Design](../design.md) | Resolved meaning to the specification |
| A requirement choice is semantic | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition to the specification caller |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Accepted Design and needed architecture results exist | `specification_started` | Candidate requirement contract with seams and consumers |
| Candidate specification or a stated gap is complete | `specification_returned` | Declared ticket, architecture, Design, or semantic return |

## Operation

1. At `specification_started`, trace each accepted Design result to observable requirements.
2. Define inputs, transitions, outputs, failures, recovery, ownership, interfaces, dependencies, authority, and applicable non-functional constraints.
3. Give every requirement observable acceptance, a verification seam, and a consumer.
4. Mark inapplicable conditions explicitly. Return unresolved meaning, scope, authority, vocabulary, or empirical questions upstream.
5. Use the Review and Decision reference for any semantic requirement choice, then return the candidate or gap at `specification_returned`.

## Returns

| Result | Consumer |
|---|---|
| Candidate specification | [To-Tickets](to-tickets.md) |
| Architecture gap | [Codebase Design](codebase-design.md) or [Improve Codebase Architecture](improve-codebase-architecture.md) |
| Meaning or scope gap | [Design](../design.md) |
| Semantic choice | [Review](review.md), then [Decision](decision.md) |
