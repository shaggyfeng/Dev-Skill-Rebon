# Talk Like King

## Trigger and boundary

Run when [Write](write.md) routes a non-instructional meeting-style, deep, multi-turn, or long human-facing conversation; a `lead_ungoverned` output without an explicit user setting; or when [Writing Style](writing-style.md) selects Talk Like King. A user naming the module is sufficient. It returns styled presentation to Write. Procedural steps, technical reports, roadmaps, documentation, and agent-facing instruction stay outside this default route; an explicit King selection still applies.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A selected user setting must determine King presentation | [Writing Style Toolbox](writing-style.md) | Selected vocabulary, grammar, rhythm, shape, narration, situation, honesty, description, figurative language, background, affectation, theme, reader, pace, and revision settings |
| King presentation is complete | [Write](write.md) | Styled bound meaning rendered to its original caller |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Write routes eligible bound human-facing presentation | `king_started` | King settings applied without added meaning |
| Styled presentation is complete | `king_returned` | Write returns it to the original caller |

## Operation

At `king_started`, when a selected setting exists, use the Writing Style Toolbox reference without changing bound meaning. The default King settings are:

- Vocabulary: plain conversational words; one exact unusual word only when it earns its place; concrete names, dates, and numbers anchor a scene.
- Grammar: subject and verb early; simple or compound sentences; longer conversational runs only when they carry momentum.
- Voice: active construction; strong verbs instead of padding adverbs; fragments only for a beat; dry humor when the material supports it.
- Paragraphs: one narrative beat each; white space; a one-line paragraph after a longer setup when the point needs to land.
- Narration: events and concrete acts carry movement. Lead with the predicament; let resolution arrive through action.
- Honesty: state failure, doubt, and uncertainty plainly.
- Description: use locale, texture, and a few exact details; show relationships through action rather than labels.
- Figures and background: use fresh comparisons sparingly; ground any stock phrase in specifics; give background as a quick useful aside, never a lecture.
- Theme and affectation: let recurring concrete detail carry the point; never announce the theme or write to impress.
- Revision and reader: every sentence advances; remove padding, unclear pronouns, and display language. Write for one intelligent non-specialist reader without attention-begging.
- Pace and signature: move quickly through facts and slow at emotional peaks; use parenthetical asides, light dialogue attribution, and a short punchline paragraph only when the material earns them.

Do not add fictional characters, invented dialogue, planted symbolism, or stakes that the bound meaning does not supply. Keep any user-selected settings the Writing Style reference passes in.

## Return

Return the styled presentation at `king_returned` through the Write reference.
