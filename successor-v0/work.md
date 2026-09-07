# Work family

## Lifecycle

```text
approved Work slice
  -> implementation or diagnosis
  -> separate Standards and Specification findings
  -> smallest owning correction
  -> repeat affected findings and verification
  -> integrate
  -> next slice | Plan or Design reopen | Release
```

Work owns admission, one-slice order, module calls and returns, the per-slice review barrier, integration, continuation, reopen, and terminals. It does not own implementation, diagnosis, Research, Handoff, Review, or Decision procedures.

## Runtime calls

| When | Load | Return to |
|---|---|---|
| An authorized implementation slice is ready | [Implementation and TDD](modules/implementation-and-tdd.md) | Candidate or upstream return to Work |
| A bounded symptom exists | [Diagnosing Bugs](modules/diagnosing-bugs.md) | Diagnosis or fix path to Work |
| A controlling fact is missing | [Research](modules/research.md) | Evidence or Research gap to Work |
| A real session or environment boundary exists | [Handoff](modules/handoff-and-boundaries.md) | Next consumer to Work |
| A candidate needs findings | [Review](modules/review.md) | Findings to the owning Work slice |
| An implementation direction is semantic | [Decision](modules/decision.md) | Admitted direction to the owning Work slice |
| A completed outcome has a reusable pattern | [Common Sense](modules/common-sense.md), after Review and Decision | Accepted table update or no change to the project table |

Complete review, correction, verification, and integration for one slice before beginning a dependent slice. A changed meaning, authority, or scope reopens Plan or Design. An eligible completed result may offer a Common Sense update through Review and Decision.

## Terminals

`work_continue`, `work_to_release`, `work_to_plan`, `work_to_design`, and `work_blocked`.
