# writing-for-agents reference

**name:** `writing-for-agents`

**description:** Write or edit skills, AGENTS.md, CLAUDE.md, and other documents consumed by an agent.

Treat every agent-facing document as an executable specification. The goal is repeatable process, not identical prose. Keep the process in ordered steps and put definitions, rules, and caveats in a reference section or a narrowly triggered companion file.

## Context pointers

A context pointer names out-of-context material and states the branch that should trigger reading it. Make the trigger concrete: say what the document contains and when it must be opened. Weak pointers are a variance bug. Always-loaded text has context cost; disclosed references have pointer and navigation cost. Inline only what every branch needs.

## Information hierarchy

Use this order:

1. In-file steps that every run performs.
2. In-file reference consulted by several branches.
3. Disclosed reference reached only by a specific branch.

Co-locate a term's definition, rules, and caveats. Split by sequence when later steps make the current step look prematurely complete. Split by invocation only when a distinct trigger deserves its own reference.

## Completion criteria

Every step ends with a checkable and exhaustive completion criterion. “Understand” or “produce a change list” is not enough. State what must exist, what must be validated, and what remains blocked. Demand drives the legwork: “every affected public seam accounted for” is stronger than “inspect the code.”

## Leading words and positive steering

Use a short defined term consistently when it compresses a recurring behavior, such as `frontier`, `seam`, `tight loop`, or `append-only`. State the desired positive behavior first. Keep a prohibition only when it is an unavoidable guardrail, and pair it with the behavior that should happen instead.

## Pruning and authority

- Keep each meaning in one source of truth; point to it instead of duplicating it.
- Treat the environment (`--help`, scripts, config, directory layout, schemas, and tests) as authoritative for facts it can expose. Document unwritten conventions, rationale, and failure modes rather than caching command output that can go stale.
- Remove stale sediment and no-op instructions sentence by sentence.
- Preserve project-specific authority: Python state, append-only records, accepted artifacts, human gates, and deterministic validators outrank a convenient summary.

## Choosing a representation

Optimize tokens per unit of reliable behavior — not raw brevity. A shorter file that gets misread is worse than the original. Match each block to the layer that carries it cheapest with the least misparse risk:

| Layer | Best for | Token cost vs prose | Misparse risk | Use when |
|---|---|---|---|---|
| Plain sentences | One-off instructions, judgment calls, "why" explanations | Baseline | Lowest — no special convention to learn | Anything requiring interpretation, not just mechanical execution |
| Tables (Markdown) | Enumerable options, config keys, priority orders | Cheaper for longer lists (drops repeated connective phrasing); break-even or worse for 2–3 short items (header/separator overhead) | Low | 3+ items sharing 2+ attributes each |
| Algorithmic/procedural (numbered steps, pseudocode) | Sequential processes with branching | Cheaper — drops transition words | Low for numbered steps; higher for pseudocode with invented syntax — stick to common conventions (if/else, indentation) | A workflow with fixed step order or branch logic |
| Structure/grammar (JSON Schema, EBNF, YAML shape) | Defining a data format precisely — config, frontmatter, API payloads | Cheaper and more precise — a genuine pure win over field-by-field prose | Low — models are heavily trained on JSON Schema/YAML/type definitions | Any input/output contract, file format, or config block |
| State machines | Multi-turn processes with named states and transitions | Cheaper as a table (`state \| trigger \| next_state \| on_failure`); can cost more as inline prose-math | Low as a table; medium as prose-math | Distinct phases with transitions (e.g. planning → executing → complete) |
| Contracts/guarantees (pre/post-conditions, invariants) | Validation steps, "what must hold true before proceeding" | Cheaper, and forces precision prose checks usually lack | Low — MUST / MUST NOT / ALWAYS / NEVER are unambiguous common instruction tokens | Validation and state-verification steps — replace narrative checks with an explicit invariant list |
| Formal logical/mathematical notation (∀, ⇒, set notation) | Dense multi-condition logic a table can't cleanly represent | Cheapest per-symbol, but the first-use gloss usually erases savings unless notation recurs | High — not a parsing failure but a convention mismatch; errors are silent | Rarely — only when a table genuinely can't represent it and notation recurs enough to amortize the gloss |
| Query/retrieval DSL (SQL, JSONPath, grep patterns) | "How to find X in stored data" | Cheaper and precise, but only as a real widely-known syntax | Low with an actual standard; high with invented shorthand | A skill reading from a state file, log, or database — give the literal query, not a description of one |
| LLM-facing structured prompting (XML-tag delimited sections) | Separating instructions from context from expected output inside the skill | Roughly neutral cost, large reliability win | Low — heavily instruction-tuned pattern | When the model must distinguish "here is context" from "here is what to produce" |

Rendering rules for tables and diagrams stay in `markdown-tables-and-diagrams.md`; this table decides *which* representation a block uses.

## Plain-sentence concision (the wait-what re-pitch)

Two triggers, one craft — restate with only the context needed, in plain Simplified Technical English, using the project's established terms (`CONTEXT.md`, domain records), adding no new decisions, jargon, or implementation changes while repairing comprehension:

- **Runtime (human confusion signal):** stop the current explanation; re-pitch the last explanation as a short restatement. Ask one concise follow-up only if the missing point cannot be inferred.
- **Authoring (compressing existing plain sentences):** apply the same re-pitch to prose already written — cut the sentence to the meaning it must carry, keep the project's established terms, and verify the compressed form still teaches the same behavior. A compressed sentence that changes what the reader does is a misparse, not a saving.

## Markdown presentation

Keep ordinary paragraphs, list items, blockquotes, and headings on one physical Markdown line. Break lines only at real Markdown boundaries; use `<br>` only for deliberate breaks inside a table cell or Mermaid label. Follow the full rendering rules in `markdown-tables-and-diagrams.md` when the document contains tables or diagrams.

## Skill mechanics in this repository

User-invoked references are reached only when the human names them; use them to orchestrate a process and honor their gate policy. Model-invoked references may be selected when their description matches, but they still cannot authorize a new normative decision. Apply [ultrawork-orchestration.md](ultrawork-orchestration.md) per the mode table in `dev-skill/SKILL.md` — full in isolated mode, modified in sequential mode, absent in parallel mode. When editing a skill, read the affected reference, update the router, check every relative pointer, and run a pointer/route validation before declaring it complete.
