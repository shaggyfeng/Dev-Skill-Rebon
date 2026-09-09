# Two-Layer Development Planning

## Trigger and boundary

Run when a consequential Design or Plan workflow spans multiple operations, branches, sessions, artifacts, or actors. A user naming the module is sufficient. It creates a candidate planning representation; it does not authorize work or make semantic decisions.

## Paired-view structure

| View | Required content |
|---|---|
| Workflow Evolution | Entry conditions, operations, branches, recovery, terminals, consumers, and truthful status |
| Structure and Authority | Step, semantic input, representation, source, operation actor, output, validator or decision actor, next consumer, authority gained, and authority not gained |

The views share the same consequential steps. A helper appears only when it affects a public seam, authority boundary, handoff, or independently testable outcome. The paired view is planning material, not a replacement for accepted decisions, specifications, tests, or project state.

The caller supplies any durable planning location. This module returns the paired candidate and does not create a planning record on its own.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Design or Plan needs a consequential paired representation | [Design](../design.md) or [Plan](../plan.md) | Paired candidate to the owning stage |
| A paired-view result contains a semantic choice | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition to the owning stage |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Consequential workflow span is bounded | `paired_view_started` | Matched Workflow Evolution and Structure and Authority views |
| Views are consistent or expose a mismatch | `paired_view_returned` | Declared owning-stage or semantic return |

## Operation

1. At `paired_view_started`, build the smallest Workflow Evolution view: entries, operations, branches, recovery, terminals, and consumers.
2. For each consequential step, add a Structure and Authority view: input, representation, source, actor, output, validator or decision actor, consumer, authority gained, and authority not gained.
3. Exclude helpers that affect no public seam, authority boundary, handoff, or independently testable outcome.
4. Check that every consequential workflow edge has a matching structure row and every row has a usable consumer.
5. Return mismatches to the owner. Use the Review and Decision reference for any semantic choice, then return at `paired_view_returned`.

## Returns

| Result | Consumer |
|---|---|
| Consistent paired view | [Design](../design.md) or [Plan](../plan.md) |
| Cross-view mismatch | Owning [Design](../design.md) or [Plan](../plan.md) item |
| Semantic choice | [Review](review.md), then [Decision](decision.md) |
