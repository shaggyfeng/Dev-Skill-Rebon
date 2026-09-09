# Work family

## Lifecycle

```text
approved Work slice
  -> implementation
  -> separate Standards and Specification findings
  -> smallest owning correction
  -> repeat affected findings and verification
  -> integrate
  -> next slice | Plan-declared closure review | Plan or Design reopen | Release

bounded symptom
  -> diagnosis
  -> implementation | declared gap return | Review -> Decision | Grilling
```

Work owns admission, one-slice order, module calls and returns, the per-slice review barrier, integration, continuation, reopen, and terminals. It does not own implementation, diagnosis, Research, Handoff, Review, or Decision procedures.

## Runtime calls

| When | Load | Return to |
|---|---|---|
| An authorized implementation slice is ready | [Implementation and TDD](modules/implementation-and-tdd.md) | Candidate or upstream return to Work |
| A bounded symptom exists | [Diagnosing Bugs](modules/diagnosing-bugs.md) | Supported cause, declared gap, or competing fix path to Work |
| A controlling fact is missing | [Research](modules/research.md) | Evidence or Research gap to Work |
| Work needs bounded human-experience evidence | [Prototype](modules/prototype.md) | Evidence to the owning Work slice |
| A real session or environment boundary exists | [Handoff](modules/handoff-and-boundaries.md) | Next consumer to Work |
| A candidate needs findings | [Review](modules/review.md) | Findings to the owning Work slice |
| All slices of a Plan-declared closure unit are integrated | [Review](modules/review.md), then [Decision](modules/decision.md) | Three-axis closure findings and disposition to the declared consumer |
| A frozen implementation or diagnosis-fix candidate is semantic | [Review](modules/review.md), then [Decision](modules/decision.md) | Findings and semantic disposition to the owning Work slice |
| Human-owned Work meaning, priority, or authority remains unresolved | [Grilling](modules/grilling.md) | Accepted rule or next human question to Work |
| A completed outcome has a reusable pattern | [Common Sense](modules/common-sense.md), after Review and Decision | Accepted table update or no change to the project table |
| A compatible host enters or advances a Work boundary | [Host Enforcement](modules/host-enforcement.md) | Checkpoint-scoped slice order or integration advance; `instruction-guided` when no interception exists |

Complete review, correction, verification, and integration for one slice before beginning a dependent slice. A changed meaning, authority, or scope reopens Plan or Design. An eligible completed result may offer a Common Sense update through Review and Decision.

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| An authorized slice and its prerequisites are present | `slice_entered` | Implementation or diagnosis for that one slice |
| Diagnosis returns a supported cause, declared gap, or competing fix path | `diagnosis_returned` | Determined implementation, declared fact or environment return, Review then Decision, or Grilling |
| A candidate returns from the owning module | `review_barrier` | Work dispatches and consumes the required Review findings |
| Findings remain | `correction_required` | Same-meaning correction and repeat affected review and verification, or upstream semantic return |
| Findings clear and declared verification passes | `slice_integrated` | Integration, Common Sense eligibility, declared closure readiness, or the next non-dependent terminal |
| All contributing slices of a Plan-declared closure unit are integrated | `closure_ready` | Three-axis closure review before that unit's declared consumer |

## Operation

1. Enter one authorized slice at `slice_entered`; do not begin a dependent slice.
2. At `diagnosis_returned`, a supported cause with an accepted bounded fix scope enters Implementation. A declared fact or environment gap follows its declared return. A frozen competing fix candidate uses Review then Decision. Human-owned meaning, priority, or authority uses Grilling.
3. Receive the candidate at `review_barrier` and dispatch Review. The implementer does not appoint, dispose of, or replace this barrier.
4. At `correction_required`, keep a same-meaning correction in the slice and repeat affected findings and verification. Return changed meaning, scope, or authority through Review then Decision and its declared Plan or Design route.
5. Enter `slice_integrated` only when findings clear and the slice's declared verification passes. A remaining finding blocks integration, closure-unit review, and dependent-slice advance.
6. When every contributing slice of a Plan-declared closure unit is integrated, enter `closure_ready` and use Review's three-axis closure loop before its declared consumer.
7. Apply bounded closure to the integrated result: use the declared terminal when determined; otherwise use the existing Research, Prototype, Grilling, Review, or Decision call.

## Terminals

`work_continue`, `work_to_release`, `work_to_plan`, `work_to_design`, and `work_blocked`.
