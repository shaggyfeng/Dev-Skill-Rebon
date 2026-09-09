# Domain Modeling

## Trigger and boundary

Run when vocabulary, concept boundaries, relations, ownership, or rules block Design or Plan. A user naming Domain Modeling is sufficient. It owns candidate domain models and exact durable writes of human-accepted meaning; it does not decide meaning or own a generic record lifecycle.

## Files and formats

| Context shape | Glossary location | ADR location |
|---|---|---|
| `CONTEXT-MAP.md` exists | The mapped context's `CONTEXT.md` | Its context `docs/adr/`; root `docs/adr/` for system-wide decisions |
| Root `CONTEXT.md` exists | Root `CONTEXT.md` | Root `docs/adr/` |
| Neither exists | Keep candidates packet-local until the first accepted term, then create root `CONTEXT.md` | Create only for a qualifying accepted ADR |

```md
# {Context name}

{One or two sentences describing the context.}

## Language

**{Canonical term}**
{One or two sentences defining what it is.}
_Avoid_: {rejected alias}
```

An ADR lives at `docs/adr/{NNNN}-{slug}.md`, uses the next sequential number, and contains a short title plus one to three sentences stating context, decision, and why. Optional sections appear only when they change later understanding.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Design or Plan needs a candidate domain model | [Design](../design.md) or [Plan](../plan.md) | Bound task facts and the candidate model's consumer |
| A term, rule, ownership, or ADR choice is semantic | [Review](review.md), then [Decision](decision.md) | Findings and accepted disposition before any exact write |
| Human-owned meaning remains unresolved | [Grilling](grilling.md) | Accepted rule or next question for the candidate model |
| A compatible host enters or advances a Domain Modeling boundary | [Host Enforcement](host-enforcement.md) | Checkpoint-scoped candidate return or exact write; `instruction-guided` when no interception exists |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Candidate terms or rules need a semantic disposition | `candidate_returned` | Review then Decision or Grilling return without a durable write |
| Exact human-accepted content and its applicable location are known | `exact_write` | Scoped glossary, ADR, or final Grilling decision-record write only |
| Exact durable content is written | `record_returned` | Declared calling lifecycle consumes that unchanged content |

## Operation

1. Locate the applicable context: `CONTEXT-MAP.md`, a root `CONTEXT.md`, or packet-local candidate terms when neither exists.
2. Inspect accepted terminology, decisions, interfaces, schemas, and relevant code.
3. Identify overloaded, conflicting, undefined, or cross-context terms.
4. Define each candidate by what it is; identify rejected aliases; bind entities, relations, rules, ownership, lifecycle, and boundaries. Use the glossary format for accepted terms.
5. Test boundaries with concrete edge conditions. Preserve code, document, and meaning conflicts for human resolution.
6. Use the Review and Decision reference for a semantic term, rule, ownership, or ADR choice; otherwise return the candidate at `candidate_returned`.
7. At `exact_write`, after exact human acceptance, write only the accepted canonical terms to the applicable glossary. Create a short ADR only when the trade-off is hard to reverse, surprising without context, and real.
8. For a completed Grilling session, use `exact_write` to write its final decision record without changing its scoped questions, intent baseline, accepted rules, supersession, or next consumers, then return it at `record_returned`.

## Returns

| Result | Consumer |
|---|---|
| Candidate model or domain conflict | [Design](../design.md) or [Plan](../plan.md) |
| Human-owned meaning | [Grilling](grilling.md) |
| Accepted glossary, ADR, or final Grilling decision record | Calling lifecycle |
| Missing context or contradictory ownership | Originating caller |
