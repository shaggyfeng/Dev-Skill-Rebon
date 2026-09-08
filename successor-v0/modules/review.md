# Review

## Trigger and boundary

Run Review when a frozen candidate needs findings. A user naming Review is sufficient; it does not replace the caller's normal trigger. Review produces findings only: it never selects, fixes, authorizes, records, or closes work.

## Assignments

| Surface | Required assignment |
|---|---|
| Grilling | Main agent, Standards and Specification together |
| Runtime or instructional implementation slice | Separate Standards and Specification passes, both focused on runtime logic |
| Roadmap checkbox or merge closure | Standards + Correctness and Specification + Correctness as separate assignments |

One Work slice completes its findings, fixes, repeat findings, verification, and integration before a dependent slice begins. Do not batch implementation slices into one review.

## Axes

| Axis | Question |
|---|---|
| Standards | Does it follow applicable project and engineering rules? |
| Specification | Does it preserve the accepted behavior, boundaries, and producer/consumer contract? |
| Correctness | Does the implemented runtime behavior operate as intended at its public seam, including its stated failure behavior? |

## Finding shape

| Field | Meaning |
|---|---|
| Violated rule or requirement | The exact Standards, Specification, or Correctness expectation not met |
| Consequence | What consumer, behavior, authority, or closure claim is affected |
| Failure mechanism | How the candidate produces the consequence |
| Required correction | The smallest owning change that removes the finding without selecting new meaning. Never an added safeguard, fallback, verification, recovery, or action to appear safer. A finding citing only safety without an exact violated expectation is invalid |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| An optimized review surface needs sizing or partitioning | [Context Optimization](context-optimization.md) | Bounded material or read-free synthesis for Review |
| A normal-mode review cannot fit | [Mode Gate](../../mode-gate.md) | Re-admitted profile with an optimized mode suggested |
| An implementation review needs its structural slice boundary | [Implementation and TDD](implementation-and-tdd.md) | Slice scope, seam, verification, and consumer for the frozen surface |
| Findings leave a semantic option or closure disposition unresolved | [Decision](decision.md) | Semantic disposition to the calling operation or checklist owner |

## Operation

1. Freeze the candidate, scope, accepted authority, and return consumer.
2. Use the matching Runtime reference to size an optimized surface, re-enter Mode Gate, or obtain an implementation slice boundary.
3. Run each required assignment on the same frozen surface. A grilling review stays with the main agent; parallel implementation or closure assignments may use fitting reviewer workers.
4. Report every finding in the Finding shape. Findings do not contain an unreviewed replacement decision.
5. Return findings to the caller. Use the Decision reference when a semantic disposition remains; send an implementation correction to the owning slice.
6. For roadmap or merge closure, use the Decision reference before returning complete closure findings to the checklist owner. Review never ticks, closes, or records the item.

## Persistent review

Persistent review activates only from an exact user request and applies only to its confirmed scope. It remains findings-only and ends on an explicit disable request or scope terminal.

## Returns

| Result | Consumer |
|---|---|
| Findings for a semantic option | Decision |
| Findings for an implementation slice | Owning slice |
| Closure findings | Decision, then checklist owner |
| Missing scope or authority | Originating caller |

Proxy artifacts—schemas, reports, catalogs, scaffolds, tests, or evidence bundles—never substitute for the runtime behavior or instruction required by a roadmap item.
