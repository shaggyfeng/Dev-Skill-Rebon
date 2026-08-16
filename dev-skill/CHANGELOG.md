# Local dev-skill changelog

## 1.3.0-local.5 — 2026-08-16

Merged `wait-what` into `writing-for-agents` and applied the representation doctrine + re-pitch concision to every dev-skill file (owner-directed):

- `wait-what.md` deleted (archived): its craft lives in `writing-for-agents.md` as "Plain-sentence concision (the wait-what re-pitch)" with two triggers — runtime (human confusion signal → re-pitch) and authoring (compress existing prose without changing what it teaches); SKILL.md Layer 2 and the routing index re-pointed;
- compression pass over the whole collection with an instruction inventory per file — nothing removed, wording tightened, Contents blocks cut, enumerable prose converted to lists: ultrawork-orchestration 6,407→5,315 words (−17%), setup 3,106→2,280 (−27%), prototype 2,795→1,761 (−37%), improve-codebase-architecture 1,987→1,309 (−34%), codebase-design 1,821→1,259 (−31%), code-review 1,363→961 (−29%), hard-code-review 1,328→964 (−27%), domain-modeling 1,406→814 (−42%), to-tickets 991→686 (−31%), diagnosing-bugs 1,594→1,241 (−22%), grilling 1,356→1,196 (−12%), deep-decision-review 753→611 (−19%), tdd 1,100→992 (−10%), plus to-spec, handoff, phase-boundaries, research, wizard, teach tightened; aggregate ≈33,600→22,900 words across the compressed set (−32%);
- verified already-optimal, left unchanged: the four teach-format files, the tdd mocking/tests sub-references, implement, rebon-workflow-visual-display, collection-overview, and all literal CLI/template blocks (doctrine: query DSL and structure/grammar layers are pure wins already);
- three more stale pre-rebuild prose forks found and fixed to mode-table wording (phase-boundaries, to-tickets, research; improve-codebase-architecture reworded) and teach's sequential-only authority note aligned to the mode table;
- verification: no stale Rebon-online forks remain, all fences balanced, every rule spot-checked per file during rewrite.

## 1.3.0-local.4 — 2026-08-16

Compressed SKILL.md for per-load token cost without instruction loss, and adopted a representation-selection doctrine (owner-directed):

- SKILL.md tightened from 2,612 words / 19,042 bytes to 2,260 words / 16,929 bytes (13.5% words, 11% bytes): intro collapsed to a read-order line; the UltraWork paragraph and the Layer 1 prose walkthrough trimmed (the diagram carries the topology); stage-detection and persistence rules rewritten as crisp invariant bullets; Layer 2 and contract cells tightened to MUST/NEVER style. Instruction inventory checked rule by rule — mode table, detection evidence table, session persistence, all 25 Layer 2 rows, all 21 routing rows, and all 7 contract items survive with meaning intact; the Mermaid diagram is unchanged (per the doctrine, diagrams earn their tokens);
- `writing-for-agents.md` gained the representation-selection table (prose vs tables vs algorithms vs schemas vs state machines vs invariants vs formal notation vs query DSL vs XML-tagged sections, each with token-cost and misparse-risk guidance) — optimize tokens per unit of reliable behavior, not raw brevity; rendering rules remain in `markdown-tables-and-diagrams`;
- fixed a stale pre-rebuild sentence in `writing-for-agents.md` that still gated ultrawork-orchestration on Rebon-online instead of the mode table.

## 1.3.0-local.3 — 2026-08-16

Made dev-skill enablement a session-level state (owner-directed fix):

- once enabled, the skill stays the operating contract for all work in the session and is never silently dropped between tasks; only an explicit user disable ends it;
- every task runs through the corresponding stage subskill — an edit is a Work slice, a decision-opening question is a grill round, an artifact challenge is session-mode review, a fact lookup is research; uncovered requests route through ask-dev intake, so there is no raw path around the stages;
- mode classification and the detected stage persist across tasks, with stage transitions following Layer 1 on evidence rather than drop-and-re-enter;
- the structure is constant and its depth scales with the task (a one-line fix is a Work slice sized to one line).

## 1.3.0-local.2 — 2026-08-16

Added stage detection as the second operating action, so an activated dev skill resumes the current situation's stage automatically and the user goes straight to work:

- on activation, immediately after mode classification, inspect the workspace's durable state (open review findings, tickets, handoff docs, boards, approved specs, candidate specs and paired views, grill frontiers and decision maps, records) and enter the detected stage directly, announcing it in one line the user can correct;
- the most downstream unfinished obligation wins; multiple unrelated active situations produce one bounded question, never a guess; detection reads only durable artifacts so fresh sessions resume identically;
- ask-dev intake now fires only when detection finds no active situation or the user brings a new problem nothing covers; the Layer 1 diagram gained the detection fork and the Layer 2 ask-dev row was re-triggered accordingly.

## 1.3.0-local.1 — 2026-08-16

Rebuilt the routing architecture around provider modes and five stages. Breaking: seven reference files were merged or replaced and their names no longer exist on disk (pre-rebuild copies archived in `.scratch/dev-skill-pre-rebuild-archive/`; session record in `docs/reviews/dev-skill-rebuild-deep-review-001.md`).

- added a mode system classified once at activation — `isolated` (subagents, no context fixes), `parallel` (subagents, bounded contexts), `sequential` (no subagents, handoff-doc packets) — with the workflow identical in every mode and only per-mode instructions differing; references point to the mode table instead of restating per-setup forks;
- re-staged the workflow as 0 Route · 1 Design · 2 Plan · 3 Work · 4 Release, dissolving the monolithic review stage: review is now grill-round infrastructure, session-overlay review of plan artifacts, and the mandatory three-axis close-out inside Work's one standard loop (implement → review → fix → verified);
- `ask-dev.md` replaces `ask-techlead.md` as the single entry dispatcher: three intake facts, one binary artifact-touch check (greenfield → deep-review intake untangle; brownfield → triage), no catalog knowledge required of the user;
- `grilling.md` absorbs `grilling.md`, `grill-me.md` (now a no-repo flag), `grill-with-docs.md` (with-docs instruction), `wayfinder.md` (path-finder instruction), and `to-questionnaire.md` (multi-party instruction) into one round-loop skill that classifies the value gap per question — pointy, straight, expanding, multi-party — from the always-on reviewer's findings;
- `hard-code-review.md` absorbs `adversarial-reviewer-SKILL.md` and `implementer-reviewers.md` (historically one skill duplicated per execution setup) with a four-way trigger: important-code pre-merge, session-mode deep review with code context, pasted diff with any check request, and explicit hunt phrases; important code includes any authoritative controller in any language;
- `code-review.md` became three-axis (Standards · Spec · Correctness) in isolated role contexts with no cross-axis reranking; the Correctness axis's brief is the hard-code-review contract;
- `deep-decision-review.md` became findings-only across three contexts (intake, grill infrastructure, session overlay); recording moved to the workflows (grilling's with-docs instruction and normal artifacts after human resolution); session-mode activation stays never-model-inferred, satisfied by a deterministic offer plus an affirmative human reply; no skill remains user-explicit-only;
- `triage.md` rescoped from the issue-tracker state machine to the brownfield maintenance survey (what to maintain, not what's wrong); the tracker-triage, agent-brief, and out-of-scope material is archived;
- `ultrawork-orchestration.md` repositioned as the host-neutral Work-stage execution strategy (full in isolated, modified in sequential, absent in parallel); Rebon survives only as the Kanban display vehicle and in historical evidence;
- removed project-specific governance from the standing contract (it belongs to the host project's governing documents, e.g. AGENTS.md) and genericized the runtime-plane clause;
- verification: dangling-reference sweep clean, all routing-index targets exist, Mermaid fences balanced; `setup-matt-pocock-skills.md` tracker references repointed to grilling's instructions.

## 1.2.3-local.7 — 2026-08-14

Added visible Rebon Workflow dispatch for the admitted Rebon-online branch:

- every delegated DevSkill role now runs inside a visible Workflow run rather than a standalone agent call;
- a generic `preflight_display_harness` displays Reader, Planner, Deep Reviewer, and sequential Applier stages before the applied plan exists;
- the distinct `kanban_execution_script` remains blocked until all preflight artifacts validate and renders exact card IDs, card groups, dependencies, verification, and recovery; and
- the display adds no Python, publication, human-decision, or close-stage authority and has no mandatory effect on Codex, Rebon-local, or unknown providers.

## 1.2.3-local.6 — 2026-08-14

Narrowed the development orchestration scope by human decision:

- UltraWork Kanban, pre-Kanban scripts, context envelopes, Task-Budget Estimators, and oversized-agent fanout now apply only when the acting development host is Rebon and its selected provider is online;
- Codex agents, Rebon agents using local providers, and other hosts use their native workflows without those mandatory rules; and
- governed Python-runtime serialization and any separately accepted runtime context-partitioning contracts remain unchanged.

## 1.2.3-local.5 — 2026-08-14

Historical entry. Its universal plan-driven scope was superseded by `1.2.3-local.6` and must not be executed as the current rule.

Made the UltraWork Kanban gate universal for plan-driven work:

- roadmap, checklist, implementation-guide, ticket, and ADR-driven tasks must run the pre-Kanban four-stage preflight before any workflow script or substantive action;
- online providers use delegated Reader/Task Breaker, Planner, Deep Reviewer, and sequential Plan Applier groups; and
- a single local model performs the same roles in the same order, sequentially, with no preflight-stage or Kanban bypass.

## 1.2.3-local.4 — 2026-08-14

Historical entry. Its development-agent scope was superseded by `1.2.3-local.6`; separately accepted Python-runtime and roadmap contracts remain governed by their own authoritative documents.

Applied the oversized-task rule across current and future roadmap work:

- require a manifest-only Task-Budget Estimator before any local-model reading, planning, reasoning, review, aggregation, testing, or writing;
- fan out oversized reads into bounded summaries plus aggregation and write one shared artifact through sequential digest-chained chunks;
- re-estimate failed or oversized partitions into smaller ranges, invalidate dependent fragments, and resume without resending the oversized task; and
- require V7 remediation evidence for completed packets while carrying the invariant into V8, V9, V10, and later roadmaps.

## 1.2.3-local.3 — 2026-08-14

Historical entry. Its local and generic provider-adaptive UltraWork modes were superseded by `1.2.3-local.6`; only Rebon with an online provider now enters UltraWork.

Merged the human-approved provider-adaptive UltraWork orchestration method:

- added a separate Development Execution Profile so online Rebon development swarms cannot be confused with the governed local Runtime Provider Binding;
- added automatic provider/capacity discovery with one human fallback question and fail-closed sequential operation when unknown;
- added one Kanban workflow per roadmap item, atomic fresh-context cards, dependency/write-set parallelism, context-budgeted worker rotation, and split-not-retry recovery;
- added a sequential UltraWork mode for one local model and bounded parallel mode for supported online or explicitly multi-capacity providers;
- added Standing Development Gate Authorization so preapproved Rebon development gates do not pause eligible work while uncovered authority remains explicit; and
- preserved Python authority and runtime serialization while making atomic facilitation, option-analysis, integration, and review roles provider-adaptive: delegated to isolated subagents when usable, sequential in fresh contexts otherwise; append-only evidence and human-owned normative decisions remain unchanged.

## 1.2.3-local.2 — 2026-08-13

Promoted the human-approved two-layer development-planning method proven during Roadmap V7:

- added a routed reference for synchronized Workflow Evolution and Structure and Authority views;
- kept the Structural Trace Ledger as a derived implementation handoff rather than a second design authority;
- required conflicts to return to the human-facing design workflow; and
- retained V7-specific trial evidence outside the reusable skill reference.

## 1.2.3-local.1 — 2026-08-06

Imported the useful upstream 1.2.3 workflow material and adapted it to this
repository's single-local-LLM contract:

- added frontier-round `grilling` and `phase-boundaries` references;
- added sequential `handoff`, `teach`, `to-questionnaire`, `wait-what`, and
  `writing-for-agents` references;
- added a controller-aware manual-procedure `wizard` reference;
- added secret redaction to diagnosis guidance;
- added HTML-as-primary-source guidance for UI prototypes;
- added explicit project routing for the new references;
- retained sequential review, no delegation, Python authority, append-only
  governance, resumability, and human publication gates.

Upstream instructions that assume hosted models, parallel agents, background delegation, or a remote issue tracker are never copied as project runtime rules. The Development Execution Profile governs only an admitted Rebon-online UltraWork development run.
