# Improve codebase architecture

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `plan.architecture.classify` returns `brownfield_survey` or architecture evidence is insufficient |
| Input | `plan.architecture-survey-input.v0.1`: `task_id`, `scope_digest`, repository digest, bounded scope, named areas, interfaces/dependencies, tests, accepted decisions, producer, consumer |
| Output | `plan.architecture-survey.v0.1`: structure map, evidence-backed friction, ranked candidates, dependency classes, decision conflicts, disposition, status, consumer; plus optional `plan.selected-candidate.v0.1` on the human's pick — the candidate with its evidence, options, and dependents as an eligible Design decision ticket |
| Authority | read-only survey and proposal |
| Failure | `architecture_survey_unresolved` or `design_reopened` |
| Consumer | architecture classifier or `codebase-design` |

## Operation

1. Bound the survey: a user-named direction is taken as-is; otherwise weight recently-changed code — change-frequency evidence from repository history — to find hot spots.
2. Map modules, interfaces, callers, dependencies, state, effects, and tests.
3. Detect shallow pass-through modules, leaked invariants, scattered state, duplicated behavior, unstable dependency direction, and inaccessible test seams.
4. Apply the deletion test to each candidate and classify each dependency and required adapter.
5. Check accepted architecture decisions before recommending change; findings and candidates use the codebase-design vocabulary exactly — module, interface, depth, seam, adapter, leverage, locality — grounded in CONTEXT.md domain terms and accepted ADRs; a candidate contradicting an accepted decision is surfaced marked and routed through `design_reopened`.
6. Rank candidates by locality, leverage, change frequency, testability, migration risk, and Design fit.
7. Return the survey to architecture classification; route any proposal need through `codebase-design` after classification.
8. Present the ranked candidates to the human; on the human's pick of one candidate, compile the typed `plan.selected-candidate.v0.1` — the candidate with its evidence, options, and dependents as an eligible Design decision ticket — and return it to the Plan architecture classifier for Design-entry construction by Route, through which the picked candidate enters Design grilling; the durable domain operations inline run through `domain-modeling` — CONTEXT.md terms update as they crystallize, and a rejection carrying a load-bearing reason is offered as an ADR; the presentation vehicle is host-side, not runtime text.

## Invariants

- No repository mutation or unsupported interface proposal.
- Every finding cites current repository evidence.
- No recommendation silently contradicts an accepted decision or changes Design meaning.

## Recovery

- Oversized scope partitions into bounded read sets.
- Evidence still unresolved after one survey returns `architecture_survey_unresolved` to clarification.
- A conflict with accepted meaning returns `design_reopened`.

## Completion

Complete only when survey coverage is explicit and every candidate has evidence, seam, dependency, test, risk, disposition, and consumer. Otherwise clarify or reopen upstream.
