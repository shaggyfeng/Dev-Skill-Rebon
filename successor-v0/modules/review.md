# Review

## Trigger and boundary

Run Review when a frozen candidate needs findings. A user naming Review is sufficient; it does not replace the caller's normal trigger. Review produces findings only: it never selects, fixes, authorizes, records, or closes work.

## Assignments

| Surface | Required assignment |
|---|---|
| Grilling | Main agent, Standards and Specification together |
| Runtime or instructional implementation slice | Separate Standards and Specification passes, both focused on runtime logic |
| Plan-declared complete implementation, phase, roadmap, or merge unit governed by accepted Design or final Grilling decisions | Standards + Correctness and Specification + Correctness as separate assignments |

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
| A frozen candidate consumes a final Grilling decision record | [Grilling](grilling.md) | Exact intent baseline and accepted rules for the candidate's review |
| A candidate may complete an accepted Design or final Grilling record scope | [Plan](../plan.md) | The Plan-declared closure unit or no closure review |
| A finding set needs its determined-correction or semantic-disposition split | [Semantic-choice test](../../SKILL.md) | Determined correction to its owner or unresolved semantic finding to Decision |
| A final Grilling decision record governs review findings or corrections | [Decision](decision.md) | Actionable finding or invalidated action request for the current candidate |
| Findings leave a semantic option or closure disposition unresolved | [Decision](decision.md) | Semantic disposition to the calling operation or declared closure consumer |
| A compatible host enters or advances a declared Review boundary | [Host Enforcement](host-enforcement.md) | Checkpoint-scoped entry or findings return; `instruction-guided` when no interception exists |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Candidate, scope, authority, consumer, and any governing final Grilling decision record are frozen | `review_started` | Required findings assignments on that unchanged surface |
| Required assignments finish | `findings_returned` | Findings to the declared caller, or Decision actionability then the declared return; no selection, fix, record, or closure |

## Operation

1. At `review_started`, freeze the candidate, scope, accepted authority, return consumer, and any applicable final Grilling decision record.
2. Use the matching Runtime reference to size an optimized surface, re-enter Mode Gate, or obtain an implementation slice boundary.
3. Run each required assignment on the same frozen surface. A grilling review stays with the main agent; parallel implementation or closure assignments may use fitting reviewer workers.
4. Report every finding in the Finding shape against the frozen candidate, accepted rules, and any applicable final Grilling decision record. Findings do not contain an unreviewed replacement decision.
5. Return findings to the caller at `findings_returned`. When an applicable final Grilling decision record governs the candidate, send every finding and proposed correction to Decision for the record-based actionability check. Without that record, a smallest correction uniquely determined by an accepted rule returns directly to its owning slice; use Decision only when a semantic disposition remains.
6. For a Plan-declared closure unit, use the Decision reference for a final-record actionability check or unresolved semantic finding before returning closure findings to its declared consumer. Review never ticks, closes, or records the unit.

## Closure selection

The matching accepted Design result or final Grilling decision record and Plan select a closure unit. A roadmap checkbox records a passed unit; it never selects one.

| Complete Plan-declared unit | Required review |
|---|---|
| Standalone implementation slice with no complete closure unit | Separate Standards and Specification only |
| One complete implementation governed by accepted Design or final Grilling decisions | Three axes after every contributing slice integrates |
| One phase | Three axes before the phase closes |
| One roadmap | Three axes for every planned phase, then for the roadmap |
| One merge proposal | Three axes before the merge closes |

## Closure loop

For a complete Plan-declared closure unit:

1. Run Standards + Correctness and Specification + Correctness on the same frozen surface.
2. The closure owner sends every finding and proposed correction governed by an applicable final Grilling decision record to Decision for its actionability check. Without that record, the semantic-choice test applies: a determined smallest correction returns to its owner and an unresolved semantic finding uses Decision.
3. Apply the correction and rerun both assignments on the changed surface.
4. Return closure only when no finding shows a public-behavior, authority, consumer, terminal, or Value-Gap failure.

## Persistent review

Persistent review activates only from an exact user request and applies only to its confirmed scope. It remains findings-only and ends on an explicit disable request or scope terminal.

## Returns

| Result | Consumer |
|---|---|
| Findings for a semantic option | Decision |
| Findings for an implementation slice | Owning slice |
| Closure findings | Declared Plan, Work, Release, or checklist consumer; Decision checks every record-governed finding or correction and unresolved semantic disposition |
| Missing scope or authority | Originating caller |

Proxy artifacts—schemas, reports, catalogs, scaffolds, tests, or evidence bundles—never substitute for the runtime behavior or instruction required by a roadmap item.
