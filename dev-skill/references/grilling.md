---
name: grilling
description: Sharpen an uncertain plan, decision, or idea by closing its value gap in rounds. One skill with gap-shaped instructions — each round's reviewer findings tell the griller which instruction applies next (pointy, straight, expanding, or multi-party). Use when dispatched from ask-dev or triage, when the user asks to grill/interview a plan, or when a settled design re-opens mid-project. Absorbs the former grilling, grill-me, grill-with-docs, wayfinder, and to-questionnaire references.
---

# Grilling

The unit of progress is the **value gap** — the distance between the user's current state and the valued outcome, as understood. Grilling closes the understanding gap; implementation belongs to later stages. One skill, not five: the former grill variants were the same activity against differently shaped gaps.

## Core model — the round loop

1. The griller asks the current question frontier.
2. The human answers.
3. The reviewer — always on during grilling, per `deep-decision-review.md` grill infrastructure — returns findings: what closed, what remains, what expanded, who owns what. It never steers, facilitates, or answers.
4. **The griller** classifies the gap per question, from the review feedback, and picks the next instruction.

Round 1 boots with the plain instruction and the reviewer on; classification starts at round 1's end. Classification is per frontier question — one round may hold a straight question and an expanding question, each with its own instruction.

## Gap taxonomy

| Gap condition | Speed | Identification signature (from review findings) | Instruction |
|---|---|---|---|
| whole gap closed | super fast | this round closed everything; nothing remains | terminal — the reviewer clears the puzzling mind; the user exits |
| pointy | increasing | all of this round's gaps closed; none re-form in another shape next round | plain |
| straight | constant | some gap closed, but the same question returns narrower next round | with-docs |
| expanding | decreasing | no gap closed; the gap is larger next round — more dimensions than the closing direction | path-finder |
| multi-party | variable | an answer closes a gap but reveals someone else owns it; or the user deflects ("that's X's gap") | multi-party |

## Operating contract

Grilling is a requirements and decision activity, never implementation. Map the subject as a design tree; the **frontier** is the set of decisions whose prerequisites are settled. Ask the whole frontier in one numbered round, then wait for answers before recomputing.

Facts and decisions differ: the griller looks up facts from the workspace, tools, or cited primary sources; the human decides normative choices, priorities, acceptance boundaries, and publication. Never ask the human for a safely inspectable fact; never answer a human decision on the human's behalf.

```text
❓ Q1 — <short title>: <one decision question and its options>
➡ Recommended: <one option with the reason>
```

One decision per question; if an answer depends on an unresolved question in the same round, defer it. Preserve the user's exact answers and the options offered — later review must inspect that pair. The session is complete only when no decision branch remains silently assumed, and stops at the human confirmation gate before implementation, publication, or other material action. Auto-apply may authorize mechanical corrections preserving an accepted decision, never a new normative decision.

## Instructions

### Plain (pointy)

Converge. The gap closes faster each round and will close before anything bigger must be built — even if the solution isn't currently buildable, the *understanding* closes. Keep rounds tight; open no dimension the answers haven't forced. No recording.

### With-docs (straight)

Grind steadily and record: the same question returns narrower each round, so capture each increment as it lands — sharpen the glossary and record ADRs inline using `domain-modeling.md`. This instruction owns grilling's recording rules:

- Doc entries written during a round are **candidates**; a candidate publishes only after that round's review findings and the human's resolution.
- Recording is append-only: supersede or append, never silently rewrite accepted entries.
- Each round's record pairs the question, its exact options, the answer, the findings, and the resolution — later rounds and audits must be able to inspect that chain.

### Path-finder (expanding)

The gap has more dimensions than the closing direction; close all dimensions with the correct topology. Plan, don't do — decisions, not deliverables.

- **Name the destination first** (the spec, decision, or change this effort finds its way to); the destination fixes the scope.
- Chart the dimensions as **decision tickets** on the tracker (or local markdown tracker): one question per ticket, resolution is a decision, sized to one agent session, blocking edges wired between tickets. The **map** is a single index issue listing decisions made and pointing at tickets for detail; it never restates a decision. Refer to tickets by name, never bare ids.
- **Fog of war:** the map is deliberately incomplete. Keep suspected-but-unsharp questions in a "not yet specified" section; ticket a question only when it can be stated precisely now, blocked or not. Fog graduates into tickets as the frontier advances.
- **Out of scope:** work beyond the destination is ruled out in its own section and never graduates; close any ticket found past the destination and record it there.
- Claim a ticket before working it; resolve at most one per session (research excepted); record the resolution, close the ticket, append the decision to the map's index.
- When the way is clear, hand off — don't build: collapse the map via `to-spec.md`.

### Multi-party (variable)

The user is not the only party defining the gap. Grill the **send**, not the subject — ask the user only: (1) who receives it, their expertise, and their relationship to the project; (2) which concrete facts or decisions must come back. Write `to-questionnaire-<slug>.md` in the approved location: purpose, sender, recipient, use of answers, context, answer instructions, questions ordered by importance — one thing per question, a direct answer stub each, "why this matters" only where a question could be misunderstood, and an "Anything else?" section. The questionnaire records an information gap; it never makes the missing decision. Answers return through the normal decision path.

## No-repo flag

No repository or durable docs: run any instruction statelessly — in-memory rounds, no candidates, no records. If the idea becomes repository work, hand the settled decision record to the with-docs instruction or the main flow.

## Exits

- **Whole gap closed (any round):** the reviewer clears the user's puzzling mind; exit at the shared mind-cleared terminal. Nothing more is recorded.
- **Frontier settled:** proceed to Plan — `to-spec.md` for machine consumption, `two-layer-development-planning.md` for human-verifiable structure, then `to-tickets.md`.
- A falsified prototype or new Plan evidence re-opens a round here with the evidence in the frontier.

## Orchestration boundary

Represent each round as an atomic intent packet: the question frontier, relevant accepted decisions, factual evidence, options, and the prior answer when one exists. Dispatch per the mode table in `dev-skill/SKILL.md` — parallel option-analysis cards with round-integrator and facilitator where the mode permits, or the same roles one fresh context at a time. These roles recommend but never answer for the human or publish the decision. The reviewer stays separate from the facilitator in every mode.
