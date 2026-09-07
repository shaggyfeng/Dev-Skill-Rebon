# Ticket Compilation

## Trigger and boundary

Run from an accepted specification. A user naming the module is sufficient. It decomposes approved requirements into candidate Work slices; it does not redefine the specification or authorize Work.

## Ticket structure

| Field | Content |
|---|---|
| Title and outcome | Short name and one independently observable end-to-end result |
| Requirement coverage | Specification requirements and acceptance conditions the slice fulfills |
| Scope | Transformation, inputs, exclusions, read/write ownership, and affected seam |
| Dependencies | Only true blockers; no cycles or hidden shared-state conflicts |
| Completion | Verification, recovery path, and exact next consumer |

A vertical slice crosses the layers needed for one observable result and is demoable or verifiable alone. A wide mechanical change uses expand, migrate, contract: add the compatible form, move callers in independently valid batches, then remove the old form. The caller chooses any durable tracker or file location; this module returns the candidate set only.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| An accepted specification must become Work slices | [Plan](../plan.md) | Candidate ticket set to Plan approval and Work handoff |
| A ticket must define an executable Work slice | [Implementation and TDD](implementation-and-tdd.md) | Objective, scope, accepted seam, behavior test, verification, and consumer fields |
| A ticket set conflicts with the requirement contract | [To-Spec](to-spec.md) or [Design](../design.md) | Corrected specification or Design meaning |
| A partition or priority choice is semantic | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition for the ticket set |

## Operation

1. Partition requirements into independently verifiable vertical slices with one observable outcome each.
2. Put enabling work before dependent behavior. Use expand, migrate, contract for broad mechanical change; add an integration slice when intermediate batches cannot remain valid.
3. Derive dependency edges from real prerequisites. Reject cycles, overlapping write ownership, and hidden shared-state conflicts.
4. Bind every requirement and acceptance condition to at least one slice, with transformation, dependencies, read/write ownership, review surface, verification, recovery, and consumer.
5. Use the Review and Decision reference for any semantic partition or priority choice.

## Returns

| Result | Consumer |
|---|---|
| Candidate ticket set | [Plan](../plan.md) approval and [Work](../work.md) handoff |
| Incomplete or oversized slice | This module for repartition |
| Specification conflict | [To-Spec](to-spec.md) or [Design](../design.md) |
| Semantic choice | [Review](review.md), then [Decision](decision.md) |
