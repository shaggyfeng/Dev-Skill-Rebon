# Prototype

## Trigger and boundary

Run a Prototype when a bounded empirical question cannot be answered by source evidence or deterministic analysis, especially when human experience or feedback is the missing information. Use available computer-use capability when it is better; this module remains the bounded fallback. A user naming Prototype is sufficient.

Prototype produces non-production evidence. It does not decide the result, mutate production, or authorize implementation.

## Experiment shapes

| Question | Shape | Placement and run rule |
|---|---|---|
| Logic, state, data, or behavior | Portable core with a thin disposable shell | Keep it beside the relevant code, marked as a prototype, with one run instruction and visible state after every action |
| UI, layout, or interaction | Structurally distinct variants in the existing host surface | Prefer an existing route; use a clearly temporary route only when no host exists; expose the active variant visibly |

The prototype has one question, assumption, success criterion, falsification criterion, inconclusive criterion, run instruction, observed result, limitation, and affected work. Keep persistence in memory unless persistence itself is the question. Validated decisions return to Design or Plan; the disposable artifact is not production code.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Source facts can answer the question without an experiment | [Research](research.md) | Evidence to the originating caller |
| Evidence creates a semantic choice | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition for the caller |
| Bounded evidence must answer a human-owned question | [Grilling](grilling.md), [Design](../design.md), or [Plan](../plan.md) | Evidence to the owning question or stage |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| One bounded empirical question and its criteria are known | `experiment_started` | Smallest disposable experiment and stated observation |
| Criteria produce an observed result | `evidence_returned` | Supported, falsified, or inconclusive evidence to the declared caller |

## Operation

1. At `experiment_started`, state one falsifiable question, assumption, success, falsification, and inconclusive criteria.
2. Select the smallest disposable logic or UI experiment that can answer it. Keep one run instruction and all relevant state visible.
3. For logic, prefer a portable core and thin disposable shell. For UI, compare structurally distinct variants in the existing host surface.
4. Observe only the cases required by the criteria. Keep observation separate from interpretation.
5. At `evidence_returned`, return `supported`, `falsified`, or `inconclusive`, plus limitations, reproduction, and affected work.
6. Use the Review and Decision reference for any choice created by the evidence.

## Returns

| Result | Consumer |
|---|---|
| Bounded evidence | [Grilling](grilling.md), [Design](../design.md), or [Plan](../plan.md) |
| Need for source facts instead | [Research](research.md) |
| Semantic choice | [Review](review.md), then [Decision](decision.md) |
| Invalid premise or unavailable experiment | Originating caller |
