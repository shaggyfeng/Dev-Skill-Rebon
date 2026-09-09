# Codebase Design

## Trigger and boundary

Run for a new interface, seam, or architecture candidate in Plan. A user naming Codebase Design is sufficient. It produces a candidate architecture inside accepted Design meaning; it does not silently change that meaning.

## Design vocabulary

| Term | Definition |
|---|---|
| Module | A unit with an interface and implementation, at any scale |
| Interface | Everything a caller must know: operations, inputs, outputs, invariants, ordering, errors, configuration, and performance |
| Implementation | What remains inside the module after its interface is known |
| Depth | Capability available per unit of interface a caller learns |
| Deep module | Much behavior hidden behind a small interface |
| Shallow module | An interface nearly as complex as the implementation |
| Seam | A place where behavior can vary without editing at that place |
| Adapter | A concrete implementation of an interface at a seam |
| Leverage | Capability callers gain from depth |
| Locality | Change, bugs, knowledge, and verification concentrated in one place |

## Candidate structure

State the required behavior, chosen module, interface, hidden implementation responsibility, dependency classification, seam and adapters when justified, observable test surface, constraints, and trade-offs. A Design-It-Twice comparison gives each independent candidate the same structure before Review and Decision compare depth, locality, and seam placement.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Plan needs a new architecture or seam | [Plan](../plan.md) | Accepted Design meaning and a candidate architecture consumer |
| A candidate needs a requirement contract | [To-Spec](to-spec.md) | Candidate architecture for specification compilation |
| A consequential plan needs a paired view | [Two-Layer Development Planning](two-layer-development-planning.md) | Candidate architecture for the paired planning view |
| Evidence about the existing architecture is missing | [Improve Codebase Architecture](improve-codebase-architecture.md) or [Research](research.md) | Survey or evidence to complete the candidate |
| A candidate comparison or interface choice is semantic | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition for the candidate |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Plan supplies accepted Design meaning and a bounded architecture concern | `architecture_started` | Candidate behavior, ownership, interfaces, seams, and constraints |
| A candidate structure or evidence gap is complete | `architecture_returned` | Declared specification, paired-view, evidence, Design, or semantic return |

## Operation

1. At `architecture_started`, map required behavior, callers, dependencies, state, effects, tests, and accepted constraints.
2. Classify each dependency path:

| Path | Rule |
|---|---|
| In-process | Deepen and merge; no external seam. |
| Local substitutable | Use the justified local stand-in seam. |
| Remote but owned | Use a port and production/test transport adapters. |
| True external | Use a port, production adapter, and test mock. |

3. Assign cohesive behavior and state ownership. Define small, deep interfaces with operations, inputs, outputs, invariants, ordering, errors, configuration, and performance constraints.
4. Add a seam only where behavior must vary. Prefer observable outcomes over hidden effects. Use the deletion test: a useful module redistributes meaningful complexity when removed.
5. For explicit Design-It-Twice work, create independent candidates with distinct constraints, then use the Review and Decision reference for comparison and return at `architecture_returned`.

## Returns

| Result | Consumer |
|---|---|
| Architecture candidate | [To-Spec](to-spec.md) or [Two-Layer Development Planning](two-layer-development-planning.md) |
| Missing evidence | [Improve Codebase Architecture](improve-codebase-architecture.md) or [Research](research.md) |
| Changed Design meaning | [Design](../design.md) |
| Semantic interface choice | [Review](review.md), then [Decision](decision.md) |
