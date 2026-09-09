# Markdown Tables and Diagrams

## Trigger and boundary

Run only when [Write](write.md) selects a Markdown table or Mermaid diagram. It returns the rendered form to Write. This module renders supplied structure; it does not choose meaning, add facts, or validate a decision.

## Runtime reference

| When | Load | Return or use |
|---|---|---|
| Write selects a Markdown table or Mermaid diagram | [Write](write.md) | Already-bound structure to render; renderer returns the form to Write |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Write supplies already-bound repeated fields, mapping, or relationship | `form_rendering` | Table or Mermaid rendering from that supplied structure |
| Rendered form preserves supplied meaning | `form_returned` | Write receives the table or diagram |

## Operation

At `form_rendering`, render only the structure Write supplied.

### Tables

- Use a table for repeated fields, direct comparisons, or exact mappings; use a short list when it would have one row or one meaningful column.
- Put one blank line before and after a table, with no blank lines between rows. Use one header row, one separator row, and one physical line per row.
- Keep one concept per column, one comparable item per row, and cells concise and parallel. Move long explanation below the table.
- Escape literal pipes as `\|`. Use `<br>` only for a deliberate visual break inside a cell. Do not put multiline lists or fenced code inside cells.

### Diagrams

- Use Mermaid only when a relationship is materially clearer than prose or a table. Prefer the smallest diagram that preserves the relationship.
- Use `flowchart TB` when branching, label length, or depth needs vertical space. Use `flowchart LR` or `flowchart TD` only for a short, lightly branched flow that fits the expected viewport. Split a diagram when no direction keeps it readable.
- Use `sequenceDiagram` for time-ordered interaction and `stateDiagram-v2` for states and transitions.
- Use unique stable ASCII identifiers, short quoted labels when punctuation appears, and one edge per line. Derive all nodes and edges from bound meaning; do not encode unaccepted meaning in arrows, colors, or styling.
- Add a short prose interpretation when the reader may not render Mermaid.

## Return

Return the rendered table or diagram at `form_returned` through the Write reference.
