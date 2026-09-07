# Implementation and TDD

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `work.execution` receives a current admitted slice and lease |
| Input | `work.execution-argument.v0.1`: `task_id`, `scope_digest`, `slice_id`, objective, inputs/exclusions, dependencies, write set, accepted test seams, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs and current digests, exact frozen subject, verification, recovery, producer, consumer, and admitted `review_context`, `review_kind`, and `review_state`, current `runtime.read-plan.v0.1`, remaining `runtime.read-frontier.v0.1` when sequential, coverage accumulator, read budgets, and `retry_budget` |
| Output | `work.candidate.v0.1` plus successor `work.repository-snapshot.v0.1`, exact review-surface manifest, current read-plan and coverage-accumulator digests, remaining read frontier when applicable, and frozen `review.request.v0.1`: candidate digest, `slice_id`, changed artifacts, behavioral changes, tests/results, deviations, unresolved gates, predecessor/changed-artifact digests, exact frozen subject, `review_context`, `review_kind`, `review_state`, `review_surface`, declared `artifact_kinds`, `family_context`, authority source IDs and current digests, producer, status, remaining `retry_budget`, consumer |
| Authority | mutation only inside the admitted leased write set |
| Failure | `execution_blocked`, `verification_failed`, or upstream reopen |
| Consumer | `work.review` with `implementation_logic` |

## Slice execution representation

The implementation owner preserves one bounded slice through ordered gates:

```text
execution_argument
 -> read_admitted | execution_blocked
read_admitted
 -> implementing | upstream_reopen
implementing
 -> candidate | verification_failed | upstream_reopen
candidate
 -> review_request
review_request
 -> logic_review | execution_blocked
logic_review [findings=[]]
 -> close_proposal
logic_review [findings!=[]]
 -> bounded_fix | execution_blocked
bounded_fix
 -> candidate | execution_blocked
close_proposal
 -> verification | execution_blocked
verification
 -> slice_closure | diagnosis_disposition | work_invalidated | execution_blocked
slice_closure
 -> integration
```

| Gate | Required binding | Downstream authority |
|---|---|---|
| `read_admitted` | current lease, snapshot, exact write set, `runtime.read-plan.v0.1`, coverage, budgets, and finite `retry_budget` | implementation may read only admitted units |
| `candidate` | one slice's minimum TDD behavior, successor snapshot, changed-artifact digests, and declaration-complete review surface | immediate `implementation_logic` review |
| `logic_review` | frozen `review.request.v0.1` with Standards and Specification assignments and conditional Correctness only from validated artifact declarations | clean result to verification, findings to bounded fix |
| `verification` | current post-mutation read plan, complete verification coverage, and successor snapshot | closure or Work diagnosis disposition |
| `slice_closure` | verified candidate, matching review/verification digests, and integration write set | Work integration and continuation |

A failed or stale gate stays within its declared recovery route; no later slice is admitted from a pending candidate.

## Operation

1. Revalidate the lease, repository snapshot, dependencies, authority, write ownership, current read-plan binding, coverage accumulator, and remaining `retry_budget`.
2. Before reading any implementation surface, compile or validate the current `runtime.read-plan.v0.1`, compare the target with all read budgets, and partition by admitted coverage units before the first content read when it exceeds or may exceed a budget. In `parallel-normal`, use bounded evidence cards; in `sequential-normal`, consume one bounded unit and persist its fragment, `runtime.read-frontier.v0.1`, and `runtime.handoff.v0.1` before the next unit. No direct oversized implementation read is admitted.
3. Use TDD when behavior can be asserted through an accepted seam — a seam named in the admitted slice's accepted test seams; a needed seam missing from that set returns upstream reopen for scope and is never improvised mid-slice. For each cycle: add one behavior test, confirm the expected failure, implement the minimum behavior, then confirm focused success — vertical one-test-one-implementation cycles only; all-tests-first horizontal slicing is an anti-pattern.
4. Derive expected values independently from implementation — an assertion that recomputes the expected value the way the code does is tautological, and expected values come from an independent source of truth. Test through public interfaces and mock only external effects or nondeterministic boundaries; a test that mocks internal collaborators, tests private methods, or verifies through a side channel is implementation-coupled — its tell is breaking on refactor with unchanged behavior.
5. When no valid red-first seam exists, record the reason and use the declared verification path.
6. Before reading back or digesting changed artifacts, compile or validate a current post-mutation `runtime.read-plan.v0.1`; if target digests, scope, budgets, or consumer changed, invalidate the prior plan and bind a successor frontier. Read back only admitted changed-artifact coverage units, persist their fragments and remaining frontier or handoff, then run focused static and behavioral checks.
7. Construct the frozen two-axis implementation review request for exactly this `slice_id` before verification, integration, or another slice admission. Include the complete review-surface manifest: changed artifacts, applicable accepted requirements, tests, interfaces, integration seams, declared `artifact_kinds`, `family_context`, authority source IDs and their current digests, exact frozen subject, producer, consumer, `review_context`, `review_kind`, `review_state`, the current read-plan and coverage-accumulator digests, and remaining `retry_budget`. A validated code-artifact declaration may add the existing conditional Correctness assignment; Implementation does not infer it. Review owns deterministic read sizing and partitioning of that manifest.

## Parallel-normal dispatch

Follow `successor-v0/work.md` Parallel-normal dispatch for roles, budgets, dependency ordering, and serialization. This module adds:

- The implementer remains inside the admitted lease and write set.
- Candidate output and the successor repository snapshot are persisted before review.
- One candidate opens one immediate slice review barrier; do not batch it with another slice candidate.
- This module cannot activate optimized preworkflow.

## Invariants

- No speculative behavior, private-implementation test contract, or mutation outside the write set.
- Tests exist only at seams named in the admitted slice's accepted test seams.
- An implementation candidate cannot advance without Standards and Specification findings resolved.
- No later Work slice is admitted while this candidate's review, fixes, verification, or integration remains open.
- Passing tests do not replace declared behavior coverage.

## Recovery

- Unexpected failure routes to `diagnosing-bugs`; write conflict serializes through the integrator. Any retry carries and decrements the finite `retry_budget`.
- Oversized scope splits the Work slice after preserving completed read coverage; changed meaning, scope, authority, or acceptance returns upstream reopen. Zero `retry_budget` returns `execution_blocked` or upstream reopen without another read.
- A verification failure creates a bounded successor candidate, decrements and persists `retry_budget`, and reruns affected review and verification only from a changed frontier.

## Completion

Complete only when the candidate and digest exist, changed artifacts are read back under a current post-mutation read plan, focused checks resolve, deviations are explicit, bindings and coverage are current, remaining `retry_budget` is persisted, and an implementation review consumer is ready.
