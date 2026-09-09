# Writing Style

## Trigger and boundary

Run when Write receives an explicit user setting, sample, or long-form creative, persuasive, promotional, story, or plot work. A non-instructional meeting-style, deep, multi-turn, or long human-facing conversation goes directly from Write to Talk Like King unless an explicit setting or sample has priority. A user naming a style is sufficient. Writing Style selects presentation settings; it does not change meaning, decide content, or render the result.

## Toolbox

| Setting | Determines |
|---|---|
| Vocabulary | Register, word economy, informality, and concrete anchors such as names, dates, or numbers |
| Grammar | Sentence simplicity, direct subjects and verbs, and connective runs |
| Sentence rhythm | Active or passive habit, adverb density, fragments, intensifiers, and humour register |
| Paragraph shape | Beat length, white space, topic/support order, and single-sentence emphasis |
| Narration | Whether events or abstractions carry movement |
| Situation | Predicament-first framing, inversion, and resolution mode |
| Honesty | How failure, doubt, and uncertainty are stated |
| Description | Locale, texture, detail density, anchors, and show-versus-tell |
| Figurative language | Comparison density, freshness, and treatment of stock phrases |
| Background | Placement and compression of prior context without lecturing |
| Affectation | Display language to remove or retain only when it carries meaning |
| Theme | Recurring concrete elements and whether the point is stated or implied |
| Reader | Assumed knowledge, jargon tolerance, address stance, and attention needs |
| Pace | Where the text moves quickly, slows, or lands a point |
| Revision | Redundancy, unclear pronouns, padding, and missing clarification |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Write receives an explicit setting, sample, or long-form creative work | [Write](write.md) | Bound meaning and form; Writing Style returns selected settings to Write |
| A King setting is selected | [Talk Like King](talk-like-king.md) | Styled presentation to Write without changed meaning |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Write supplies an explicit setting, sample, or long-form creative request | `style_selected` | Reusable presentation settings or a King route |
| Setting, missing detail, or human preference is known | `style_returned` | Declared Write, Talk Like King, or human consumer |

## Operation

1. At `style_selected`, use an explicit user-supplied style, sample, or plain-language preference when one exists. Keep only its reusable settings; never copy its content or claims. Use the Talk Like King reference for an explicit King choice; return another setting to Write.
2. For long-form creative work without a preference, ask once whether to use the Talk Like King reference or a supplied setting or sample. `lead_ungoverned` never reaches this question: Write routes it directly to Talk Like King.
3. For an unfamiliar named style, ask for a short description or sample instead of inventing its settings.
4. Express a selected setting through the Toolbox. Keep each selected value tied to the bound reader, form, and meaning.
5. At `style_returned`, keep the setting for the current conversation. Do not create a durable style record unless the human asks for one.

## Return

| Selection | Consumer |
|---|---|
| Direct technical treatment or explicit non-King settings | [Write](write.md) |
| King selection | [Talk Like King](talk-like-king.md), then [Write](write.md) |
| No preference for long-form creative work | Human |
| Missing style detail | Human or [Write](write.md) |
