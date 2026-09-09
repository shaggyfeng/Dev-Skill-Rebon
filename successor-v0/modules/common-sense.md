# Common Sense

## Trigger and boundary

Run a Common Sense checkpoint at Route discovery, active-stage continuation, and any operation that recognizes a relevant failure or success pattern. A user naming Common Sense is sufficient. It is a pattern reference only: never a router, diagnosis engine, ranking system, recommender, capability selector, tool dispatcher, or decision maker.

## Pair structure

| Field | Meaning |
|---|---|
| Subject | Exact pattern name used for cue lookup |
| Scope | Operation conditions in which the pair applies |
| Failure condition, outcome, evidence | What goes wrong, its effect, and the observable cue |
| Success condition, outcome, evidence | What corrects the same pattern, its effect, and observable evidence |
| Vibe scope cue | Targeted-search text that makes the subject a candidate |
| Vibe failure cue | Targeted-search text that makes the failure pattern a candidate |

Each complete pair is one separate two-column table in that fixed row order. Its `### Pair` heading improves human navigation only; Subject is the matching identity.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A project table is absent | [Common Sense native table](../common-sense-table.md) | Initial pair set for `.dev-skill/common-sense.md` |
| A compact matched reference reaches a semantic choice | [Semantic-choice test](../../SKILL.md) | Mechanical or evidentiary result, or a Decision call, applied by the caller; Common Sense still returns only the reference |
| An eligible Work or Release outcome may update the table | [Review](review.md), then [Decision](decision.md) | Findings and accepted disposition before Common Sense writes the pair |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| A project table is absent or a current operation names a pattern | `cue_scan` | Native-table initialization when absent, or targeted cue-row reading |
| An exact Subject becomes a candidate | `pair_read` | Read that complete pair and return no reference or one compact reference |
| Review and Decision accept a reusable pair update | `pair_update` | Write only that accepted pair and refresh its cue rows |

## Operation

```text
caller
  -> Vibe reads headings and cue rows only
  -> candidate subject
  -> Attention reads matching complete pair only
  -> no reference | compact matched-pattern reference
  -> caller applies the semantic-choice test
```

1. At `cue_scan`, initialize the project's `.dev-skill/common-sense.md` from the native table only when absent.
2. At `cue_scan`, use targeted search to read cue rows, not the complete table.
3. At `pair_read`, read a complete pair only after its exact Subject becomes a candidate.
4. A matching success cancels a failure only when the pair declares that exact same subject and condition. Otherwise retain both references.
5. A missing or unreadable table returns no reference and ordinary work continues.

## Updates

Eligible Work or Release outcomes may draft one complete reusable failure and success pair. Review supplies findings; Decision selects the disposition; at `pair_update`, Common Sense makes only the accepted update and refreshes its cue rows. It does not invent new pairs during ordinary work.

## Returns

| Result | Consumer |
|---|---|
| No matching cue | Caller continues ordinary work |
| Compact matching reference | Caller loads the [semantic-choice test](../../SKILL.md) when a semantic choice remains |
| Accepted table update | Project Common Sense table |
