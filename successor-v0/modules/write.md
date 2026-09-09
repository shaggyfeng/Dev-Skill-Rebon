# Write

## Trigger and boundary

Use Write for reports, technical conversation, Grilling packets, roadmaps, documentation, and agent-facing material. A user naming Write is sufficient. Write receives bound meaning and chooses its presentation; it does not create or change a decision.

## Representation and placement

| Information shape | Form |
|---|---|
| One rule or judgment | Prose |
| Three or more items sharing two or more fields | Table |
| Ordered work or conditional branch | Numbered procedure or decision table |
| Exact data shape | Schema or grammar |
| Named states and transitions | State table or state machine |
| Three or more material relationships | Diagram |

| Material | Placement |
|---|---|
| Every branch needs it | In-file step |
| Several branches consult it | In-file reference beside its term |
| One named branch needs it | Pointer that names the target and trigger |

## Runtime calls

| When | Load | Return to Write |
|---|---|---|
| The selected form is a Markdown table or Mermaid diagram | [Markdown Tables and Diagrams](markdown-tables-and-diagrams.md) | Rendered table or diagram from already-bound structure |
| An explicit user style or sample, or long-form creative work, selects style | [Writing Style](writing-style.md) | Selected settings or a King route |
| A non-instructional meeting-style, deep, multi-turn, or long human-facing conversation; `lead_ungoverned` without an explicit setting; or a King route | [Talk Like King](talk-like-king.md) | Styled presentation without changed meaning |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Bound meaning, authority, and unanswered questions are supplied | `representation_selected` | Form, placement, and matching renderer or style route |
| Form and style return rendered bound meaning | `presentation_returned` | Original caller receives presentation without a changed decision |

## Operation

1. Keep the bound meaning, authority, and unresolved questions intact.
2. Treat agent-facing material as executable instruction. Keep universal steps inline; point to branch-specific material with the condition that requires it.
3. At `representation_selected`, select the form and placement from the tables above. Use headings for independent sections and short lists for ordered or grouped facts.
4. State the important result first. For prose, keep only the needed context and established terms; remove repetition, process noise, jargon, and wording that does not change the reader's action or understanding. For an executable step, state its observable result or return.
5. Keep procedural steps, technical reports, roadmaps, documentation, and agent-facing instruction in direct technical treatment unless the user explicitly selects another style. Treat a meeting-style, deep, multi-turn, or long human-facing exchange as conversation and use the Talk Like King Runtime call unless the user explicitly selects a non-King style.
6. Use the matching Runtime call when a selected form or style condition matches. Otherwise render direct technical treatment at `presentation_returned`.

## Return

Return the rendered bound meaning at `presentation_returned` to the caller. If the meaning is missing or changes during drafting, return to the owning caller; do not repair it through style.
