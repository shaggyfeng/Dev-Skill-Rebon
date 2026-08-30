# Ticket compilation

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | exact `plan.specification.v0.1` digest has `status=specification_approved` |
| Input | `plan.ticket-generation-input.v0.1`: `task_id`, `scope_digest`, `plan.specification.v0.1` with `status=specification_approved`, dependency graph, repository snapshot, execution profile, producer, consumer |
| Output | `plan.ticket-set.v0.1`: stable ticket IDs, requirement coverage, objectives, transformations, inputs/exclusions, dependencies/blockers, read/write sets, review surface, acceptance/verification, recovery, resume pointer, status, consumer |
| Authority | candidate Work decomposition only |
| Failure | `ticket_repartition` or `plan_revision` |
| Consumer | `plan.ticket-set.resolve`; its approved branch continues through `plan.work-start.compile` then `plan.work-start.resolve` |

## Operation

1. Partition requirements into independently verifiable vertical slices with one observable outcome per ticket.
2. Place enabling work before dependent behavior.
3. Use expand → migrate → contract for wide mechanical changes; keep migrate batches independently valid when possible.
4. Add an integration ticket when intermediate batches cannot remain valid independently.
5. Derive dependency edges from actual prerequisites and reject cycles, overlapping write ownership, and hidden shared-state conflicts.
6. Bind every specification requirement and acceptance ID to at least one ticket.
7. Bind exact scope, transformation, dependencies, blockers, read/write ownership, review surface, verification, recovery, resume pointer, and consumer for every ticket.
8. Resolve ticket-set approval separately from Work-start authorization.

## Invariants

- Tickets cannot redefine the specification.
- The graph is acyclic and every ticket fits one bounded Work slice.
- No requirement, acceptance criterion, or write set is unowned.

## Recovery

- Oversized tickets split; incomplete vertical behavior returns `ticket_repartition`.
- Specification conflict returns `plan_revision`; changed Design meaning returns `design_reopened`.
- Retry requires a changed partition, dependency, or governing specification.

## Completion

Complete only when requirement coverage is exhaustive, the DAG and frontier validate, every slice contract is complete, and one Plan consumer is named.
