# teach reference

**name:** `teach`

**description:** Teach the human a concept or skill over multiple sessions using this workspace as a stateful learning record.

Teaching is a human-facing workflow, not a project execution shortcut. If the mission is unclear, ask why the user wants to learn the topic before choosing a lesson. One mission per learning workspace; every lesson tied to an observable outcome.

## Workspace records — only when the user asks for a durable teaching workspace

- `MISSION.md` — the concrete reason for learning, success criteria, constraints, out-of-scope topics ([`teach-mission-format.md`](teach-mission-format.md)).
- `RESOURCES.md` — high-trust sources annotated with what each covers and when to use it ([`teach-resources-format.md`](teach-resources-format.md)).
- `learning-records/NNNN-<slug>.md` — short records of demonstrated understanding, prior knowledge, corrected misconceptions, or mission change ([`teach-learning-record-format.md`](teach-learning-record-format.md)).
- `lessons/NNNN-<slug>.md` or `.html` — one self-contained lesson, one tangible win.
- `assets/` — reusable lesson components (stylesheets, quiz widgets, diagram helpers).
- `NOTES.md` — non-authoritative preferences and scratch notes.

A maintained `GLOSSARY.md` uses [`teach-glossary-format.md`](teach-glossary-format.md) and is the canonical learning language.

Create directories lazily; reuse existing assets before authoring new ones. A lesson is not complete merely because it was read — require retrieval, practice, or another observable demonstration. Keep fluency (easy recall now) separate from storage strength (durable recall later); use spaced retrieval and interleaving only where they serve the mission. Prefer primary sources, official documentation, recognized experts, and peer-reviewed work; record citations and source limitations. Wisdom claims are framed as experience or open questions, never promoted to project fact without evidence.

## Mission template

```md
# Mission: <topic>

## Why
<the real-world outcome>

## Success looks like
- <observable capability>

## Constraints
- <time, budget, prior knowledge, or preference>

## Out of scope
- <explicit boundary>
```

## Learning-record rule

Write a record only when the user demonstrates understanding, discloses relevant prior knowledge, corrects a misconception, or changes the mission — never for lesson coverage or session logs. Supersede outdated records instead of deleting history.

## Authority

The human-teacher dialogue and mastery judgment stay in one main role in every mode (see the mode table in `dev-skill/SKILL.md`; orchestration may track preparation cards but cannot replace teacher or learner). The human's demonstrated understanding is the evidence; the model may generate controlled exercises but cannot certify mastery or change the mission without the human.
