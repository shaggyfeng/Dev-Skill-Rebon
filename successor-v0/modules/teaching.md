# Teaching

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | explicit teaching or learning trigger; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | explicit request to learn, teach, practice, or build human competence; model inference alone does not activate Teaching |
| Input | `teaching.request.v0.1`: `task_id`, `scope_digest`, mission in the learner's wording, intended capability, smallest learning increment, prior knowledge, constraints, material boundary, material snapshot digest, mutable seams, demonstration request, persistence preference, optional persistence authorization and named persistence writer when persistence is requested, producer, and consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `teaching_blocked` or `teaching_invalidated` failure. |
| Output | `teaching.progress.v0.1`: mission, observable outcome, bounded material, material snapshot and seam digests, source lineage, meaning partition, lesson or exercise, demonstration evidence, failure cause and repair, `mastery_judgment` (`sufficient` or `insufficient`), persistence state, persistence authorization digest, optional durable-record handoff, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, and consumer |
| Authority | lesson and demonstration guidance plus candidate durable-record preparation only; no durable write; the human owns mission, persistence authorization, and mastery judgment |
| Failure | `teaching_blocked` or `teaching_invalidated` |
| Consumer | human learner, optional durable teaching workspace, Route, Research, Write, or Handoff |

Teaching absorbs the four source formats: mission, resources, learning-record, and glossary. It remains stateless by default and creates durable records only on explicit request.

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

1. Bind one mission in the learner's wording, one intended capability, one smallest learning increment, and one observable demonstration. If the mission is unclear, ask for the real learning goal; do not change it silently.
2. Distinguish stored material, retrieval, term recall, point recall, structural understanding, and successful application. Reading, notes, recognition, or lesson coverage do not prove competence.
3. Reuse Research's additive Lexical Uplift and Professional Positioning to select a task- and learner-appropriate knowledge structure. Preserve the original wording and source every uplift edge.
4. Select bounded mission-relevant material with provenance, source lineage, and trustworthy-source gaps. Prefer primary sources and defer unrelated expansion.
5. Partition material by meaning, preserve structural context, and create bounded learning questions only after the structure exists. Keep wisdom or practice advice separately labeled from sourced knowledge.
6. Require one bounded application exercise and observable performance. Record what was demonstrated, not merely what was exposed or discussed.
7. If the demonstration fails, classify the cause as lexical entrance, missing prerequisite, structural misunderstanding, ambiguous question, insufficient application practice, or unsuitable processing structure. Repair the cause rather than repeating the same lesson.
8. Require a new demonstration after repair. Record a typed human `mastery_judgment` as `sufficient` or `insufficient`; only `sufficient` can advance to stateless completion or persistence, while `insufficient` returns to a new demonstration. Teaching never decides mastery for the human.
9. Keep the default state stateless. On an explicit persistence request, enter `persistence_authorization_pending`; create or update candidate mission, resources, lesson/assets, notes, learning record, and glossary records only after explicit human persistence authorization and a named durable writer, then emit the typed durable-record handoff with provenance. Teaching itself never writes the durable records.

## Durable format rules

| Format | Durable content and gate |
|---|---|
| Mission | `Why` as a concrete outcome, observable `Success looks like` capability, constraints, and explicit out-of-scope boundaries; revise only after the human confirms the mission changed. |
| Resources | Annotated high-trust sources with coverage and use, separately labeled wisdom/practice, and important trustworthy-source gaps; shallow or off-mission material is excluded. |
| Learning record | A sequential one-to-three-sentence record only after demonstrated understanding, relevant prior knowledge, misconception correction, or confirmed mission change; lesson coverage and session logs do not qualify. Supersede outdated records rather than deleting them. |
| Glossary | Canonical teaching language with one preferred term and a tight definition; add a term only after demonstrated understanding, avoid ambiguous aliases, and revise as understanding improves. |

Durable records never prove mastery. The evidence remains the human's demonstrated application and judgment.

## State machine

```text
teaching_requested -> mission_bound | teaching_blocked
mission_bound -> material_selected
material_selected -> meaning_partitioned | teaching_invalidated
meaning_partitioned -> lesson_ready | teaching_invalidated
lesson_ready -> demonstration_requested | teaching_invalidated
demonstration_requested -> demonstrated | demonstration_failed | teaching_invalidated
demonstration_failed -> cause_classified | teaching_invalidated
cause_classified -> repaired | teaching_invalidated
repaired -> new_demonstration | teaching_invalidated
demonstrated -> human_mastery_judgment | teaching_invalidated
new_demonstration -> demonstrated | demonstration_failed | teaching_invalidated
human_mastery_judgment [mastery_judgment=sufficient] -> stateless_complete | persistence_requested | teaching_invalidated
human_mastery_judgment [mastery_judgment=insufficient] -> new_demonstration | teaching_invalidated
human_mastery_judgment [mastery_judgment=missing] -> teaching_blocked
persistence_requested -> persistence_authorization_pending | teaching_invalidated
persistence_authorization_pending [authorized and writer_bound] -> durable_records_bound
persistence_authorization_pending [declined] -> stateless_complete
persistence_authorization_pending [missing authorization or writer] -> teaching_blocked
durable_records_bound -> durable_record_handoff
teaching_invalidated -> mission_bound | teaching_blocked
```

## Invariants

- Mastery judgment is human-owned and typed: `sufficient` permits completion or requested persistence, `insufficient` requires a new demonstration, and a missing judgment blocks progression.
- One mission and one observable demonstration govern each teaching operation.
- Research positioning, lexical uplift, source lineage, and trustworthy-source gaps remain separate from the human's mastery judgment.
- Failed demonstrations are repaired by cause and rechecked with a new demonstration.
- The model may generate bounded exercises but cannot certify mastery, change the mission, or promote unsupported wisdom to fact.
- Material is bounded by mission and provenance; unrelated expansion is deferred.
- Material snapshot and mutable-seam digests are revalidated before lesson, demonstration, mastery, or persistence transitions; a stale result enters `teaching_invalidated` and cannot advance.
- Durable records require explicit human persistence authorization and a named writer; Teaching emits only a candidate handoff and never treats persistence as mastery evidence.
- Glossary entries follow demonstrated understanding, use one preferred term, and retain evolving meaning without deleting superseded learning history.

## Recovery

- Missing mission, intended capability, observable demonstration, material boundary, or consumer returns `teaching_blocked`.
- A mission change requires human confirmation and a successor scope.
- Missing source lineage or an unsupported claim returns the material to Research or marks the source gap; it does not advance the lesson.
- A failed demonstration keeps the learning state unchanged until its cause is repaired and a new demonstration is completed.
- A stale material or seam invalidates affected lesson and demonstration evidence, preserves only proven-unaffected progress, and returns through `teaching_invalidated` to a successor mission/material binding.
- A persistence request without explicit human authorization or a named writer returns `teaching_blocked` or the stateless branch; it cannot write records.

## Terminals

`stateless_complete`, `durable_record_handoff`, and `teaching_blocked` are the only Teaching result terminals; `teaching_invalidated` always re-enters successor binding before a result can be returned.

## Completion

Complete only when one mission, observable outcome, bounded material, current material/seam digests, source lineage, meaning partition, demonstration evidence, typed human mastery judgment, failure repair when needed, persistence choice, and one consumer are current; candidate durable records have explicit authorization and a named writer, durable handoff exists only when requested, and records never substitute for demonstrated application.
