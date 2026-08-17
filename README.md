# dev-skill

Development workflow router and operating contract that guides software-engineering work from intake through design, planning, work, and release. Current version: **1.3.0-local.6** (an overhaul remake of the upstream dev-skill 1.2.3).

Designed for [Rebon](https://github.com/Quorafind/Rebon-Releases) and [Codex](https://github.com/openai/codex)

## What this skill

DevSkill turns engineering work into a repeatable process instead of a sequence of ad-hoc model calls. Every request enters a five-stage workflow — Route, Design, Plan, Work, Release — that runs identically whether the host is a cheap subagent farm, a normal online provider, or one local model. The design is driven by the failures that break unsupervised development: context walls on oversized tasks, conflicting parallel edits, skipped human authority, silent drops of the operating contract between tasks, and reviews that approve instead of find.

| Value | What it prevents | Where it lives |
| --- | --- | --- |
| Every task routed through the structure | Ad-hoc work drifting out of the workflow; the contract silently dropped between tasks | Stage detection + session persistence, `SKILL.md` |
| Human authority preserved | Reviewers or orchestrators implementing, approving, or publishing; gates passing without authorization | Findings-only reviewers; standing contract |
| Context-size-bounded work | Context walls on oversized cards; repeated failures from retrying them; conflicting writes to shared seams | Card splitting, write sets, split-not-retry — `references/ultrawork-orchestration.md` |
| Provider-adaptive execution | Guessing host capacity; a local model depending on a subagent workflow it cannot access | Mode table, `SKILL.md` |
| Review as workflow infrastructure | Review postponed to a later stage; reviewers steering instead of finding | One standard loop: implement → three-axis review → fix → verified |
| Sharp design before build | Building from an unsettled frontier; planning without machine-consumable specs | Grill loop; to-spec; to-tickets |
| Continuity across sessions | Fresh sessions re-deriving state from chat history | Durable-artifact stage detection; handoff; phase-boundaries |
| Low-friction direct entries | Re-routing a pasted diff, bug symptom, or review request through intake | Direct entries bypass Route by design |

## How it works

- **Three provider modes, one workflow.** `parallel-isolated` (cheap, delegated subagents, UltraWork selected) · `parallel-bounded` (normal native workflow, default) · `sequential-local` (one local model, the same roles run sequentially through bounded handoff packets). The workflow is identical in every mode; only per-mode instructions differ.
- **Five stages.** Route (intake and dispatch) → Design (gap-closing grill loop) → Plan (shape, specify, atomize) → Work (implement → three-axis review → fix → verified) → Release (record and carry).
- **One standard loop.** Implement → three-axis review (Standards · Spec · Correctness) → fix → verified. Reviewers find but never implement, approve, or publish.

The mode table, trigger maps, and routing index in [`SKILL.md`](SKILL.md) are the single routing authority; references point there instead of restating per-setup forks.

## Reading it

Follow [`SKILL.md`](SKILL.md) in order: mode activation → stage detection → session persistence → Layer 1 (which skill fires at which stage) → Layer 2 (which skill is enabled for which content) → routing index → standing contract. Read [`references/collection-overview.md`](references/collection-overview.md) when you need provenance or the engineering flow.

The directory holds `SKILL.md`, `references/` (32 reference skills, one discipline each), and `agents/openai.yaml` (Rebon interface metadata).

