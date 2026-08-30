# Implementation and TDD

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `work.execution` receives a current admitted slice and lease |
| Input | `work.execution-argument.v0.1`: `task_id`, `scope_digest`, `slice_id`, objective, inputs/exclusions, dependencies, write set, accepted test seams, review surface, verification, recovery, producer, consumer |
| Output | `work.candidate.v0.1` plus successor `work.repository-snapshot.v0.1` and frozen `review.request.v0.1`: candidate digest, changed artifacts, behavioral changes, tests/results, deviations, unresolved gates, predecessor/changed-artifact digests, review kind and surface, status, consumer |
| Authority | mutation only inside the admitted leased write set |
| Failure | `execution_blocked`, `verification_failed`, or upstream reopen |
| Consumer | `work.review` with `implementation_logic` |

## Operation

1. Revalidate the lease, repository snapshot, dependencies, authority, and write ownership.
2. Read the bounded implementation surface and preserve unrelated user changes.
3. Use TDD when behavior can be asserted through an accepted seam — a seam named in the admitted slice's accepted test seams; a needed seam missing from that set returns upstream reopen for scope and is never improvised mid-slice. For each cycle: add one behavior test, confirm the expected failure, implement the minimum behavior, then confirm focused success — vertical one-test-one-implementation cycles only; all-tests-first horizontal slicing is an anti-pattern.
4. Derive expected values independently from implementation — an assertion that recomputes the expected value the way the code does is tautological, and expected values come from an independent source of truth. Test through public interfaces and mock only external effects or nondeterministic boundaries; a test that mocks internal collaborators, tests private methods, or verifies through a side channel is implementation-coupled — its tell is breaking on refactor with unchanged behavior.
5. When no valid red-first seam exists, record the reason and use the declared verification path.
6. Run focused static and behavioral checks and read back or digest every changed artifact.
7. Construct the frozen two-axis implementation review request before verification or integration.

## Parallel-normal dispatch

Follow `successor-v0/work.md` Parallel-normal dispatch for roles, budgets, dependency ordering, and serialization. This module adds:

- The implementer remains inside the admitted lease and write set.
- Candidate output and the successor repository snapshot are persisted before review.
- This module cannot activate optimized preworkflow.

## Invariants

- No speculative behavior, private-implementation test contract, or mutation outside the write set.
- Tests exist only at seams named in the admitted slice's accepted test seams.
- An implementation candidate cannot advance without Standards and Specification findings resolved.
- Passing tests do not replace declared behavior coverage.

## Recovery

- Unexpected failure routes to `diagnosing-bugs`; write conflict serializes through the integrator.
- Oversized scope splits the Work slice; changed meaning, scope, authority, or acceptance returns upstream reopen.
- A verification failure creates a bounded successor candidate and reruns affected review and verification.

## Completion

Complete only when the candidate and digest exist, changed artifacts are read back, focused checks resolve, deviations are explicit, bindings are current, and an implementation review consumer is ready.
