# Development skill collection overview

This is the project adaptation of the upstream dev-skill 1.2.3 collection, rebuilt around three provider modes (isolated, parallel, sequential) and five stages (Route, Design, Plan, Work, Release). The references are routed through `dev-skill/SKILL.md`, whose mode table and two-layer trigger map are the single routing authority. Project-specific constraints live in the host project's own governing documents, not in this collection.

## Provenance note

The rebuild merged file pairs that existed only because one skill had been written twice for two execution setups — once as a subagent workflow, once for a single local LLM. The mode table now owns setup differences, so setup-duplicated files became one skill with per-mode instructions:

- `grilling.md` absorbs `grilling.md`, `grill-me.md` (now the no-repo flag), `grill-with-docs.md` (the with-docs instruction), `wayfinder.md` (the path-finder instruction), and `to-questionnaire.md` (the multi-party instruction).
- `hard-code-review.md` absorbs `adversarial-reviewer-SKILL.md` and `implementer-reviewers.md`.
- `ask-dev.md` replaces `ask-techlead.md` as the single entry dispatcher.
- `triage.md` is rescoped from the issue-tracker state machine to the brownfield maintenance survey (the tracker triage, agent-brief, and out-of-scope material is archived in `.scratch/dev-skill-pre-rebuild-archive/`).

Pre-rebuild copies of every superseded file are archived in `.scratch/dev-skill-pre-rebuild-archive/`. The rebuild session is recorded in `docs/reviews/dev-skill-rebuild-deep-review-001.md`.

## Engineering flow

### Entry and dispatch

- [`ask-dev.md`](ask-dev.md) — the single entry point: intake and one dispatch.
- [`triage.md`](triage.md) — brownfield maintenance survey.
- [`deep-decision-review.md`](deep-decision-review.md) — the deep reviewer (intake, grill infrastructure, session overlay).

### Design

- [`grilling.md`](grilling.md) — the gap-closing interview loop with gap-shaped instructions.
- [`domain-modeling.md`](domain-modeling.md) — glossary, scenario, and ADR discipline.
- [`codebase-design.md`](codebase-design.md) — deep modules, interfaces, seams, adapters, and locality.
- [`improve-codebase-architecture.md`](improve-codebase-architecture.md) — survey deepening opportunities.
- [`prototype.md`](prototype.md) — throwaway experiments collecting evidence from outside the text world.
- [`research.md`](research.md) — provider-adaptive primary-source research.
- [`two-layer-development-planning.md`](two-layer-development-planning.md) — synchronized workflow-evolution and structure-and-authority views.

### Plan

- [`to-spec.md`](to-spec.md) — turn accepted discussion into a specification.
- [`to-tickets.md`](to-tickets.md) — split a specification into dependency-ordered vertical packets.

### Work

- [`implement.md`](implement.md) — implement approved packets and verify them.
- [`tdd.md`](tdd.md) — red/green testing at confirmed public seams.
- [`ultrawork-orchestration.md`](ultrawork-orchestration.md) — the per-mode execution strategy (full in isolated, modified in sequential, absent in parallel).
- [`code-review.md`](code-review.md) — three-axis close-out: Standards, Spec, Correctness.
- [`hard-code-review.md`](hard-code-review.md) — the adversarial correctness contract; standalone hunts and the Correctness axis.
- [`diagnosing-bugs.md`](diagnosing-bugs.md) — tight feedback loop, redaction, diagnosis, and regression.
- [`resolving-merge-conflicts.md`](resolving-merge-conflicts.md) — intent-based conflict resolution.

### Release and cross-stage

- [`handoff.md`](handoff.md) — redacted context transfer to a sequential fresh pass.
- [`phase-boundaries.md`](phase-boundaries.md) — continue, clear, hand off, or compact at boundaries.
- [`writing-for-agents.md`](writing-for-agents.md) — agent-facing document and skill authoring.
- [`setup-matt-pocock-skills.md`](setup-matt-pocock-skills.md) — configure tracker and documentation conventions.
- [`wizard.md`](wizard.md) — human-only setup and migration procedures.

### Productivity and parallel tracks

- [`teach.md`](teach.md) — stateful learning workspace and evidence-based lessons.
- [`wizard.md`](wizard.md) — human-only setup and migration procedures.

`wait-what.md` was merged into `writing-for-agents.md` (the re-pitch and plain-sentence concision craft) with the 2026-08-16 compression pass.
- [`rebon-workflow-visual-display.md`](rebon-workflow-visual-display.md) — the Rebon Workflow display vehicle for isolated-mode UltraWork runs.

## Adaptation rules

Mode selection, stage routing, and trigger semantics are owned by the mode table and Layer 2 map in `dev-skill/SKILL.md`; references point there instead of restating per-setup forks. The Rebon Workflow display applies only on Rebon with Workflow support as the isolated-mode dispatch vehicle. Provider choice never changes the host project's own controller authority, records policy, or human-owned decisions.
