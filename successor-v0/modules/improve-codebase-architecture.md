# Improve Codebase Architecture

## Trigger and boundary

Run for brownfield architecture survey or insufficient architecture evidence. A user naming the module is sufficient. It is read-only evidence and candidate formation; it does not select a refactor or mutate the repository.

## Survey structure

| Field | Content |
|---|---|
| Scope | Named area, relevant domain terms, ADRs, and observed hot spots |
| Candidate | A module or cluster with one concrete friction |
| Evidence | Files, callers, interfaces, tests, or observed behavior supporting the friction |
| Deepening | The smaller interface and concentrated responsibility the candidate could create |
| Test surface | Observable seam that would replace implementation-coupled checks |
| Constraints | Accepted decisions, dependencies, and risks that limit the change |
| Strength | `strong`, `worth exploring`, or `speculative` from the evidence |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Survey analysis needs architecture vocabulary | [Codebase Design](codebase-design.md) | Module, interface, depth, seam, adapter, leverage, and locality definitions |
| Candidate comparison, priority, or selected refactor is semantic | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition for the caller |
| Evidence must become a new architecture candidate | [Codebase Design](codebase-design.md) | Survey as input to candidate design |
| A human-facing report is needed | [Write](write.md) | Rendered survey without changed findings |
| Evidence remains missing | [Research](research.md) | Evidence to extend the survey |

## Operation

1. Map modules, interfaces, callers, dependencies, state, effects, tests, accepted decisions, domain terms, and ADRs.
2. Identify shallow pass-throughs, leaked invariants, scattered state, duplicated behavior, dependency-direction problems, weak seams, and deletion-test failures.
3. Form evidence-backed candidates using the Codebase Design vocabulary reference.
4. Use the Review and Decision reference for any candidate comparison, prioritization, or selected refactor.
5. Return the Survey structure through the Codebase Design or Plan reference. Use the Write reference when a human-facing report is needed.

## Returns

| Result | Consumer |
|---|---|
| Evidence-backed survey | [Codebase Design](codebase-design.md) or [Plan](../plan.md) |
| Conflict with accepted meaning | [Design](../design.md) |
| Semantic candidate choice | [Review](review.md), then [Decision](decision.md) |
| Missing evidence | [Research](research.md) or caller |
