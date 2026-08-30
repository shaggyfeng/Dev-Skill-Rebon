# Agent-facing writing

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | a bound semantic packet requires presentation, compression, restructuring, or agent-facing documentation, or any report, user-facing message, or agent-facing document is written |
| Input | `write.input.v0.1`: `task_id`, `scope_digest`, semantic packet and digest, stable semantic IDs, claims, relations, constraints, unknowns, authority labels, consumer profile, output form, producer, consumer |
| Output | `write.candidate.v0.1`: candidate, semantic-ID mapping, semantic delta, unresolved meaning, rendering validation, status, consumer |
| Authority | presentation only |
| Failure | `write_not_admitted` or `write_semantic_delta` |
| Consumer | active family or `stage-0.review` |

A report, user-facing message, or agent-facing document without a bound semantic packet is admitted on the broadened trigger alone: the packet fields are absent, the writing doctrine applies, the semantic-delta gate is vacuous, and authority remains presentation only.

## Representation selector

| Information shape | Form |
|---|---|
| isolated rule or judgment | prose |
| at least three items sharing at least two fields | table |
| ordered work or branching | numbered procedure or decision table |
| exact data shape | schema or grammar |
| named states and transitions | state machine |
| at least three materially connected nodes | diagram |

## Writing doctrine

The writing rules apply to every output this module produces — every report, user-facing message, and agent-facing document — not only repair or confusion paths.

**Source pairing:** this section is the runtime view of the repository's writing reference (`writing-for-agents.md`); a doctrine change updates both surfaces.

**Completion criteria**
- Every step ends with a checkable, exhaustive completion criterion stating what must exist, what must be validated, and what remains blocked.
    - "Understand" or "produce a change list" is not enough.

**Leading words**
- Use a short defined term consistently when it compresses a recurring behavior.
- State the desired positive behavior first.
    - Keep a prohibition only as an unavoidable guardrail, paired with the behavior that should happen instead.

**Re-pitch**
- On a human confusion signal: stop, and re-pitch the last explanation as a short restatement in plain sentences with the project's established terms.
    - Add no new decisions, jargon, or implementation changes.
    - Ask one concise follow-up only when the missing point cannot be inferred.
- When compressing: cut the sentence to the meaning it must carry.
    - Verify the compressed form still teaches the same behavior; a compressed sentence that changes what the reader does is a misparse, not a saving.

**Hierarchy and pointers**
- Inline what every branch needs.
    - A context pointer names out-of-context material and states concretely when to open it.
- Co-locate each definition with its rules and caveats.
- Order content: in-file steps every run performs, then in-file reference several branches consult, then disclosed reference only a specific branch reaches.
    - Split by sequence when later steps make the current step look prematurely complete.
    - Split by invocation only when a distinct trigger deserves its own reference.

**Structure selection**
- Select the structural format before writing a block.
    - The most important key terms are headings.
    - Multiple heading-capable key terms inside logically continuous text are bold lead-ins.
    - Key terms carrying detailed descriptions fragment the prose into an outline indentation list: one short phrase per point, each qualifier an indented child of the point it qualifies, and a point needing a second sentence split into parent and child with deeper indentation.
    - Parallel items without many key points are a shortened flat list.
    - A comparison of parallel items against shared attributes is a table.

**Pruning and authority**
- Keep each meaning in one source of truth and point to it instead of duplicating it.
- Treat the environment as authoritative for facts it exposes.
    - Document unwritten conventions, rationale, and failure modes rather than caching stale output.
- Remove stale and no-op instructions sentence by sentence.
- Governed state, append-only records, accepted artifacts, human gates, and deterministic validators outrank a convenient summary.

**Representation guidance**
- Optimize tokens per unit of reliable behavior, not raw brevity.
    - A shorter form that gets misread is worse than the original.
- The Representation selector above owns the choice; the rendering module owns tables and diagrams.

## Operation

1. Reject input without bound meaning, stable semantic IDs, authority labels, and a consumer; on the unbound-output path, require only the envelope, authority, consumer, and output form — the packet fields are declared absent.
2. Select the smallest reliable form from the representation selector.
3. Preserve every semantic ID, relation, constraint, uncertainty, and authority label.
4. Use established project terms; keep each definition with its rules, caveats, and completion conditions.
5. Order executable instructions and give every step a checkable completion condition.
6. For long human-facing prose — a README, documentation, essays, announcements, any long-form writing for human readers — or conversational text, load `successor-v0/modules/talk-like-king.md` and run its voice acquisition before drafting.
7. Load `successor-v0/modules/markdown-tables-and-diagrams.md` before emitting or repairing a table or Mermaid diagram.
8. Compare the candidate with the semantic packet and emit a semantic delta; an empty delta is valid only when every binding is preserved. On the unbound-output path the delta gate is vacuous and the emitted delta is empty.
9. Return the candidate and delta to the consumer; a non-empty delta follows `write_semantic_delta` and cannot advance as complete.

## Invariants

- Writing never reconstructs missing meaning, resolves ambiguity, adds decisions, changes authority, or publishes a candidate.
- Compression preserves required behavior and consumer-visible implications.
- Each meaning has one authoritative location; pointers replace duplication.
- Ordinary headings, paragraphs, list items, and blockquotes use one physical Markdown line.
- Explanatory prose remains only when its removal would change execution or interpretation.

## Recovery

- Missing meaning or IDs on a bound path returns `write_not_admitted`; on the unbound-output path the absent packet fields are not a rejection.
- Non-empty semantic change: return `write_semantic_delta` with the candidate and exact delta; an empty delta remains eligible for completion.
- Rendering failure: regenerate only the affected block through the rendering module.
- Oversized output: split by consumer-visible section without separating a contract from its caveats.

## Completion

Complete only when every semantic ID is preserved or explicitly unresolved, the semantic delta is empty, rendering validates, bindings are current, and one consumer is named; on the unbound-output path, completion requires the writing doctrine applied, rendering validated, and one consumer named.
