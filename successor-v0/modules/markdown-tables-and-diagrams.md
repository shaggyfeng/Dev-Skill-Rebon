# Markdown tables and diagrams

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | selected Markdown table or Mermaid diagram, or repair of either form |
| Input | `render.input.v0.1`: `task_id`, `scope_digest`, information shape, authoritative source and digest, expected viewport, existing convention, producer, consumer |
| Output | `render.candidate.v0.1`: rendered block, source map, form/direction decision, validation, status, consumer |
| Authority | rendering only |
| Failure | `render_not_admitted` or `render_source_conflict` |
| Consumer | `stage-0.write` or originating family |

## Operation

Select the form, apply its rules, then validate the rendered block against the bound authoritative source.

### Form selector

| Shape | Form |
|---|---|
| one fact or rule | prose |
| repeated fields across items | Markdown table |
| connected topology, branching, dependency, or lifecycle | Mermaid |
| exact normative behavior | authoritative prose plus schema or tests |

### Markdown tables

1. Put one blank line before and after the table and no blank line between rows.
2. Use one header row, one separator row, and one physical line per row.
3. Keep cells concise and parallel; move extended explanation below the table.
4. Escape literal pipes as `\|`; use `<br>` only for deliberate visual breaks inside a cell.
5. Do not place multiline lists or fenced code inside cells.
6. Use alignment markers only when alignment conveys meaning.

### Mermaid diagrams

1. Use `flowchart TB` or `flowchart TD` for wide branching, dense graphs, long labels, or an `LR` layout that would clip or require horizontal movement.
2. Use `flowchart LR` only for a short, lightly branched graph that fits the expected viewport.
3. Treat `TB` and `TD` as equivalent; preserve local convention unless layout requires another direction. When editing an existing diagram, reassess node count, label length, branching, and viewport; convention holds until the layout fails.
4. Use `sequenceDiagram` for time-ordered interactions and `stateDiagram-v2` for governed states.
5. Use unique stable ASCII node IDs, quoted human-readable labels when punctuation is present, and one edge per line.
6. Bind every node and edge to the authoritative source; keep labels concise.
7. Split the graph when no direction provides a readable complete view.
8. Add a concise textual interpretation when the consumer may not render Mermaid.

## Invariants

- Rendering does not introduce, resolve, or compete with authoritative meaning.
- Tables represent repeated fields; diagrams represent material relationships.
- Prefer the smallest useful form: no visualization merely because a section has several steps, and prose wins when equally clear.
- Every displayed fact, node, and edge remains derivable from the bound source.
- Direction is selected from rendered viewport fit, not subject label.
- One physical Markdown line per ordinary paragraph, list item, blockquote, and heading; never hard-wrap prose; break a line only at a real Markdown boundary; `<br>` only for deliberate breaks inside cells and labels; preserve author-written breaks when editing.
- Run the repository's Markdown checks when available.

## Recovery

- Verify equal unescaped cell separators, adjacent rows, physical-line rules, and deliberate `<br>` use.
- Verify closed Mermaid fences, unique IDs, existing referenced nodes, source agreement, and viewport fit.
- Repair unequal rows locally; reject unclosed fences, duplicate IDs, or missing nodes.
- Shorten labels or split an oversized graph; return `render_source_conflict` for competing meaning.

## Completion

Complete only when the selected form fits the information shape, source bindings are exact, all structural checks pass, and one consumer is named.
