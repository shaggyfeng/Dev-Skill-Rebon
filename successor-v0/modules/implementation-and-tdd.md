# Implementation and TDD

## Trigger and boundary

Run for one admitted Work slice with accepted scope, dependencies, write ownership, test seams, verification, and consumer. A user naming the module is sufficient only when those boundaries exist. It mutates only inside the admitted slice; changed meaning, authority, or scope returns upstream.

## Slice structure

| Part | Definition |
|---|---|
| Objective | One observable behavior or correction the slice must make true |
| Scope | Inputs, exclusions, dependencies, and admitted write ownership |
| Accepted seam | Public behavior location already agreed by Design or Plan where callers and tests observe the same result |
| Behavior test | A test that drives the accepted seam and asserts the observable outcome independently of implementation |
| Verification | Focused static and behavioral checks that prove the slice objective and stated failure behavior |
| Consumer | Integration or the next admitted slice after review and verification pass |

A TDD cycle is one behavior test that fails for the expected reason, the minimum implementation that changes that behavior, and the same focused test passing. It is not an all-tests-first or layer-by-layer batch.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| An optimized slice needs context fit | [Context Optimization](context-optimization.md) | Bounded material for the slice |
| A normal-mode slice cannot fit | [Mode Gate](../../mode-gate.md) | Re-admitted profile with an optimized mode suggested |
| A bounded unexpected failure appears | [Diagnosing Bugs](diagnosing-bugs.md) | Supported cause and bounded fix scope to Work |
| A compatible host enters or advances an implementation boundary | [Host Enforcement](host-enforcement.md) | Checkpoint-scoped candidate construction or return; `instruction-guided` when no interception exists |
| A correction changes meaning, scope, authority, or adds a safeguard, fallback, verification, recovery, or action beyond the smallest owning change | [Work](../work.md) | Semantic correction candidate; Work invokes its Review then Decision calls before any expanded write |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| One admitted slice has its current write ownership | `candidate_build` | State changes only within that ownership |
| Candidate and focused checks are complete | `candidate_submitted` | Return candidate and checks to Work's review barrier |
| Work returns a same-meaning correction | `correction_build` | Bounded correction and repeat candidate submission |

## Operation

1. Enter `candidate_build`. Read only the material needed by the slice. Use the matching Context Optimization or Mode Gate reference when context fit requires it.
2. Use the TDD cycle when an accepted seam can assert the behavior.
3. Derive expected values independently. Test public behavior; mock only external effects or nondeterministic boundaries. Do not couple tests to private collaborators or refactor-insensitive behavior.
4. When no valid red-first seam exists, state why and use the slice's declared verification path. Do not invent a seam mid-slice.
5. Read back changed artifacts and run focused static and behavioral checks.
6. Return the candidate and focused checks at `candidate_submitted` to Work. Work dispatches and consumes separate Standards and Specification findings. When Work returns a same-meaning finding, use `correction_build`, rerun affected checks, and resubmit. When a correction changes meaning, scope, authority, or adds a safeguard, fallback, verification, recovery, or action beyond the smallest owning change, return that semantic correction candidate to Work; Work uses Review then Decision before any expanded write.

## Returns

| Result | Consumer |
|---|---|
| Candidate and focused checks | [Work](../work.md) review barrier |
| Unexpected failure with bounded symptom | [Diagnosing Bugs](diagnosing-bugs.md) |
| Missing seam, changed meaning, or invalid scope | [Plan](../plan.md) or [Design](../design.md) |
| Semantic implementation choice or expanded correction | [Work](../work.md) for Review then Decision |
