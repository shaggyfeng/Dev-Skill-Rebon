# Domain modeling

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | vocabulary, concept-boundary, relation, ownership, or rule dispute blocks Design, a roadmap-completion lifecycle review arrives from the Release controller at roadmap completion before the terminal compiles, or a term crystallizes during an admitted Plan architecture survey |
| Input | `design.domain-input.v0.1`: `task_id`, `scope_digest`, affected tickets, existing vocabulary/context map, relevant code, accepted decisions, disputed terms/relations, owners, producer, consumer; the lifecycle arm binds the Release reconciliation digest and the project `CONTEXT.md` |
| Output | `design.domain-model.v0.1`: term candidates, definitions, rejected aliases, entities, relations, ownership, rules, boundaries, contradictions, disputed meaning, authority bindings, status, consumer; the lifecycle arm returns the disposition-gated prune-and-archive proposals with promoted-candidate retirements |
| Authority | candidate vocabulary and relations only; durable writes after human disposition |
| Failure | `domain_gap` or `domain_authority_conflict` |
| Consumer | grilling, paired-view planning, or packet compiler after human resolution; the lifecycle arm returns to `release.complete-state.compile`; the survey arm returns to the Plan architecture classifier |

## Operation

1. Discover the context: `CONTEXT-MAP.md` names the contexts; a root `CONTEXT.md` is single-context; when neither exists, create it lazily at the first resolved term.
2. Inspect accepted terminology, decisions, code, schemas, and interfaces.
3. Detect overloaded, conflicting, undefined, and cross-context terms.
4. Define each domain-specific term by what it is; record rejected aliases for the same concept.
5. Write accepted terms to the context glossary inline at resolution, never batched, in the opinionated format: one canonical term per concept, `_Avoid_` aliases, one-to-two-sentence definitions, project-specific terms only.
6. Bind entities, relations, rules, ownership, lifecycle, and context boundaries.
7. Test boundaries with concrete edge conditions and expose code/document/meaning conflicts.
8. ADR gate: write a one-paragraph ADR in `docs/adr/` only when all three hold — hard to reverse, surprising without context, a real trade-off; any one missing skips it. ADRs are lazily created, sequentially numbered, and record what was decided and why.
9. Route a hard-to-reverse trade-off to the owning Design ticket and human disposition.
10. Return disputed meaning to its human owner; supersede accepted meaning explicitly rather than rewriting it silently.
11. Lifecycle review: at roadmap completion (the Release terminal), before the handoff closes, run a bounded `CONTEXT.md` review — propose outdated entries for removal, move each human-confirmed removal or retirement to a lazily-created append-only `CONTEXT-HISTORY.md`, and never delete silently; retire promoted dimension-vocabulary candidates; ADRs are untouched.

## Invariants

- A glossary is not a specification, scratchpad, or list of general programming terms.
- Repository state is evidence, not automatic normative authority.
- Human-owned meaning remains unresolved until human disposition.
- Each accepted concept has one canonical term per context.
- The module performs durable writes of accepted meaning only after human disposition and records resolved meaning only in the glossary; candidate-ledger entries are recorded per the candidate-ledger invariant.
- One per-project `CONTEXT.md` serves both the domain glossary and the dimension-vocabulary candidate ledger; this module is its only maintainer.
- Candidate ledger: terminology discovered in the work is recorded as candidate entries — a proposed name plus a prose description of its intended datum referent — non-normative; every proposal is formed against the file's current contents read at its fixed per-project path at the point of proposal formation, creating the file when it does not yet exist; a candidate reaches the assignment process only as an amendment proposal into the governed spec amendment.
- A candidate entry never constitutes or modifies an assignment and carries no operator semantics, closure, canonical definitions, or degenerate-value content; no matching, validation, or digest mechanism consumes the ledger.

## Recovery

- Unknown context returns `domain_gap`; conflicting owners return `domain_authority_conflict`.
- Code/document disagreement preserves both claims for human disposition.
- An unresolved trade-off remains on the owning Design ticket.

## Completion

Complete only when every blocking term and relation is defined, disputed, or routed to an owner; contradictions and authority are explicit; and affected tickets and consumer are bound.
