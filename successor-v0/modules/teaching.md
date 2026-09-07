# Teaching

## Trigger and boundary

Run for learning, teaching, practice, or human competence-building. A user naming Teaching is sufficient. It teaches and demonstrates; the human owns the learning mission and mastery judgment.

## Learning structure

| Part | Content |
|---|---|
| Mission | Learner's words, intended capability, constraints, and out-of-scope boundaries |
| Learning increment | Smallest concept or relation needed before the next application |
| Material | Bounded trustworthy source, its use, and an identified knowledge gap |
| Exercise | One application task that requires the intended capability |
| Demonstration | Observable learner performance, not reading, notes, recognition, or lesson coverage |
| Repair | One classified cause and a changed exercise or explanation that addresses it |

If the human asks for a durable teaching workspace and names its writer, use this layout:

| Location | Content |
|---|---|
| `MISSION.md` | `Why`, `Success looks like`, constraints, and out-of-scope boundaries |
| `RESOURCES.md` | Annotated high-trust sources, coverage, use, and source limits |
| `GLOSSARY.md` | Preferred teaching terms and tight definitions |
| `learning-records/{NNNN}-{slug}.md` | Demonstrated understanding, corrected misconception, relevant prior knowledge, or confirmed mission change only |
| `lessons/` | One self-contained lesson or exercise per bounded increment |
| `assets/` | Reusable lesson material |
| `NOTES.md` | Non-authoritative preferences or scratch notes |

Create locations lazily. Teaching itself remains stateless by default.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Mission-relevant source knowledge is missing | [Research](research.md) | Evidence, [Lexical Uplift](research.md), and [Professional Anchor](research.md) for the next teaching step |
| A durable learning artifact needs rendering | [Write](write.md) | Rendered material at the human-authorized location |

## Operation

1. Bind the Mission, Learning increment, and Demonstration from the Learning structure.
2. Select bounded, trustworthy, mission-relevant material. Use the Research reference when source knowledge, Lexical Uplift, or Professional Anchor is needed.
3. Separate term recall, point recall, structural understanding, and successful application. Reading or notes do not prove competence.
4. Give one bounded exercise and observe its application.
5. On failure, classify the cause—lexical entrance, missing prerequisite, structural misunderstanding, ambiguous question, insufficient practice, or unsuitable processing structure—then repair that cause and require a new demonstration.
6. The human judges mastery. Keep records stateless unless the human explicitly asks for durable material and names its writer.

## Returns

| Result | Consumer |
|---|---|
| Demonstrated learning and human judgment | Learner or calling family |
| Missing source knowledge | [Research](research.md) |
| Failed demonstration | Next bounded teaching step |
| Requested durable learning material | Human-authorized writer, using [Write](write.md) when rendering is needed |
