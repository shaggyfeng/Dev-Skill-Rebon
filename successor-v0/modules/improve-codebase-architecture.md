# Improve codebase architecture

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `plan.architecture.classify` returns `brownfield_survey` or architecture evidence is insufficient |
| Input | `plan.architecture-survey-input.v0.1`: `task_id`, `scope_digest`, repository digest, bounded scope, named areas, interfaces/dependencies, tests, accepted decisions, producer, consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `architecture_survey_unresolved` or `design_reopened` failure. |
| Output | `plan.architecture-survey.v0.1`: structure map, evidence-backed friction, ranked candidates, dependency classes, decision conflicts, disposition, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, consumer; plus optional `plan.selected-candidate.v0.1` on the human's pick — the candidate with its evidence, options, and dependents as an eligible Design decision ticket |
| Authority | read-only survey and proposal |
| Failure | `architecture_survey_unresolved` or `design_reopened` |
| Consumer | architecture classifier or `codebase-design` |

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

| Relation | Required source/evidence | Result | Consumer |
|---|---|---|---|
| `source_bound` | user-named direction as-is, or recent-change evidence from repository history, bounded scope, and named areas | frozen read scope | survey operation |
| `evidence_collected` | modules, interfaces, callers, dependencies, state, effects, tests, current repository evidence, accepted decisions, `CONTEXT.md` terms, and accepted ADRs | structure map and evidence-backed friction | candidate formation |
| `candidate_set` | shallow pass-throughs, leaked invariants, scattered state, duplicated behavior, dependency direction, test seams, deletion test, and category predicates | candidates using the codebase-design vocabulary exactly: module, interface, depth, seam, adapter, leverage, locality | decision check and ranking |
| `ranked` | locality, leverage, change frequency, testability, migration risk, and Design fit | ranked candidates with evidence, seams, dependencies, tests, risks, dispositions, and consumers | human choice |
| `no_pick` | no human candidate selection or evidence remains unresolved | `plan.architecture-survey.v0.1` with `architecture_survey_unresolved` when necessary, returned to architecture classification or clarification; any proposal need routes through `codebase-design` after classification | architecture classifier or `codebase-design` |
| `picked` | one human-selected candidate with its evidence, options, and dependents | typed `plan.selected-candidate.v0.1` eligible Design decision ticket, returned through Route to the Plan architecture classifier; host-side presentation only | Plan/Route/Design |
| `meaning_conflict` | candidate contradicts an accepted architecture decision or domain term | `design_reopened`; no silent Design meaning change | Design |

The survey is read-only. Durable domain operations run through `domain-modeling`; a load-bearing rejection may be offered as an ADR, and no candidate is implementation authority.

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
