# Domain modeling

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | vocabulary, concept-boundary, relation, ownership, or rule dispute blocks Design, a roadmap-completion lifecycle review arrives from the Release controller at roadmap completion before the terminal compiles, a term crystallizes during an admitted Plan architecture survey, a typed Grilling `with-docs` publication branch reaches its durable publication consumer, an accepted Grilling continuity-record reminder reaches its durable consumer, or a grilling-session closure carries a working-record target |
| Input | `design.domain-input.v0.1`: `task_id`, `scope_digest`, affected tickets, existing vocabulary/context map, relevant code, accepted decisions, disputed terms/relations, owners, producer, consumer; the lifecycle arm binds the Release reconciliation digest, roadmap-completion scope, current context/history identities or explicit absence, promoted candidates, and the project `CONTEXT.md`; the Grilling `with-docs` arm additionally binds `publication_branch`, `reviewed_packet_digest`, exact reviewed questions/options/findings/answer, `persistence_request`, `governance_profile`, authority source IDs, human-disposition digest, and the `domain-modeling` consumer; the continuity arm binds `continuity_record_branch` with `operation=update|close`, `grilling_session_id`, ordered pending accepted rounds, reminder answer, immutable working-record target or explicit absence, closure disposition `retain|promote|remove` and its documentation consumer when closing, and the same consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `domain_gap` or `domain_authority_conflict` failure. |
| Output | `design.domain-model.v0.1`: term candidates, definitions, rejected aliases, entities, relations, ownership, rules, boundaries, contradictions, disputed meaning, authority bindings, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, consumer; the lifecycle arm returns typed context-lifecycle request, proposal, disposition, and result records with applied, retained, rejected, and retired IDs plus successor context/history digests or no-change; the `with-docs` arm returns the exact reviewed-packet publication result and durable-writer handoff without changing the packet; continuity update returns the matching `grilling_session_id`, immutable working-record target, and exact accepted rounds represented by the completed write; continuity close returns `retained`, `promoted`, or `removed` after read-back or confirmed absence |
| Authority | candidate vocabulary and relations only; durable writes after human disposition |
| Failure | `domain_gap`, `domain_authority_conflict`, or `context_lifecycle_blocked` |
| Consumer | grilling, paired-view planning, or packet compiler after human resolution; Grilling `with-docs` and continuity-record branches return through Domain Modeling publication; the lifecycle arm returns its typed result to Release effect-request or complete-state compilation, or `release_blocked`; the survey arm returns to the Plan architecture classifier |

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

1. Discover the context: `CONTEXT-MAP.md` names the contexts; a root `CONTEXT.md` is single-context; when neither exists, retain the context candidate packet-local and let the durable-write branch lazily create `CONTEXT.md` only for the first human-accepted term.
2. Inspect accepted terminology, decisions, code, schemas, and interfaces.
3. Detect overloaded, conflicting, undefined, and cross-context terms.
4. Define each domain-specific term by what it is; record rejected aliases for the same concept.
5. Keep terminology candidates packet-local until human disposition. After exact human acceptance and only on the durable-write branch, write accepted terms to the context glossary inline at resolution, never batched, in the opinionated format: one canonical term per concept, `_Avoid_` aliases, one-to-two-sentence definitions, project-specific terms only.
6. Bind entities, relations, rules, ownership, lifecycle, and context boundaries.
7. Test boundaries with concrete edge conditions and expose code/document/meaning conflicts.
8. ADR gate: write a one-paragraph ADR in `docs/adr/` only when all three hold — hard to reverse, surprising without context, a real trade-off; any one missing skips it. ADRs are lazily created, sequentially numbered, and record what was decided and why.
9. Route a hard-to-reverse trade-off to the owning Design ticket and human disposition.
10. Return disputed meaning to its human owner; supersede accepted meaning explicitly rather than rewriting it silently.
11. For a Release roadmap-completion request, validate the reconciliation, roadmap-completion scope, context/history identities or explicit absence, promoted candidates, and current read-plan/coverage binding; form the immutable request-to-proposal chain with stable retained, removal, and retirement IDs, evidence, authority, predecessor digests, history entries, and explicit empty/no-change status. An empty proposal returns typed no-change without a question or write. For a non-empty proposal, require the exact human per-entry accept/reject disposition before the durable-write branch; apply accepted removals or retirements only, create `CONTEXT-HISTORY.md` only when accepted archive content exists, leave all-rejected files unchanged, preserve ADRs, and emit the typed result with applied, retained, rejected, and retired IDs plus successor context/history digests or no-change. Outside roadmap completion, return `not_applicable`. A stale or missing binding, incomplete read coverage, authority conflict, missing or stale disposition, write/read-back failure, or exhausted retry returns `context_lifecycle_blocked` to Release.
12. When Grilling selects `with-docs`, accept only a typed `publication_branch` whose `reviewed_packet_digest`, exact questions, options, findings, answer, persistence request, governance profile, authority source IDs, human-disposition digest, and `domain-modeling` consumer are current. Require the Grilling human-disposition result before any durable write; in `pair`, preserve the exact human answer; in explicitly selected `lead_ungoverned`, apply only the bound reviewer `bottom_line_fix` for the human's admitted option; under `governance_unresolved`, return without publication. When no repository or no persistence request exists, keep the result stateless. Domain Modeling alone may write the accepted glossary or ADR result, and only after the typed disposition gate.
13. When Grilling supplies an accepted `continuity_record_branch` with `operation=update`, validate its `grilling_session_id`, ordered pending rounds, and reminder answer. Before the first write, choose the first existing location in this order: `docs/records/`, `docs/`, repository root. Derive the filename as `grilling-session-<id>.md`, where `<id>` is the UTF-8 base64url encoding without padding of `grilling_session_id`; bind that target before creation and use idempotent upsert there. Resolve and reuse it after interruption; an existing target bound to another session or any later target mismatch returns `domain_authority_conflict`. Preserve each round's exact question, reviewed findings and options, exact answer, accepted runtime meaning, rejected meanings required to prevent regression, unresolved successors, and required source imports. Return the matching session, target, and represented rounds only after write and read-back succeed.
14. When the same branch arrives with `operation=close`, validate the same `grilling_session_id`, target, ordered pending rounds, and closure disposition. Before `retain` or `promote`, idempotently upsert every pending round to the working target and read it back. `retain` requires the consumer to equal the target and returns only when that target contains the exact complete record. `promote` requires a different bound documentation consumer, writes and reads back the exact complete record there, then removes the working target and confirms its absence. `remove` requires no documentation consumer, removes the working target without first persisting pending rounds, and confirms its absence. Return exactly `retained`, `promoted`, or `removed`.

## Context lifecycle representation

Domain Modeling owns proposal formation and disposition-gated writes while returning the typed lifecycle result to Release:

```text
context_lifecycle_requested
 -> context_lifecycle_proposal | context_lifecycle_blocked
context_lifecycle_proposal [proposal_empty]
 -> context_lifecycle_resolved
context_lifecycle_proposal [proposal_requires_disposition]
 -> context_lifecycle_disposition | context_lifecycle_blocked
context_lifecycle_disposition
 -> context_lifecycle_resolved | context_lifecycle_blocked
context_lifecycle_resolved
 -> context_lifecycle_result | context_lifecycle_blocked
context_lifecycle_result
 -> release.effect-request.compile | release.complete-state.compile
```

| Gate | Domain authority | Required evidence | Result |
|---|---|---|---|
| request | no mutation | current reconciliation, scope, context/history identities, promoted candidates, read-plan and coverage | immutable lifecycle request |
| proposal | proposal only | admitted context/history reads, stable IDs, evidence, authority, predecessor digests, history entries | immutable proposal or typed empty/no-change |
| disposition | human-owned | exact proposal and per-entry accept/reject decisions | disposition packet or blocked result |
| resolved result | Domain Modeling only after disposition | accepted entries, retained/rejected/retired IDs, current read-back, successor context/history digests | lifecycle result to Release |
| blocked | no advancement | retained reason and finite retry state | `context_lifecycle_blocked` |

Candidate terminology remains packet-local; only accepted entries may reach the existing durable-write branch, and no lifecycle result is current without its exact packet chain.

## State machine

```text
context_lifecycle_requested -> context_lifecycle_proposal | context_lifecycle_blocked
context_lifecycle_proposal [proposal_empty] -> context_lifecycle_resolved
context_lifecycle_proposal [proposal_requires_disposition] -> context_lifecycle_disposition | context_lifecycle_blocked
context_lifecycle_disposition -> context_lifecycle_resolved | context_lifecycle_blocked
context_lifecycle_resolved -> context_lifecycle_result | context_lifecycle_blocked
context_lifecycle_result -> release.effect-request.compile | release.complete-state.compile
domain_request -> candidate_model | publication_pending
domain_request [operation=update] -> continuity_record_pending
domain_request [operation=close and current session/target] -> continuity_record_closure
domain_request [operation=close and stale or mismatched session/target] -> domain_authority_conflict
domain_authority_conflict [non-continuity request] -> domain_gap
domain_authority_conflict [continuity update; changed state and retry remains] -> continuity_record_pending
domain_authority_conflict [continuity close; changed state and retry remains] -> continuity_record_closure
candidate_model -> human_disposition | candidate_return | domain_authority_conflict
publication_pending -> human_disposition | domain_authority_conflict
human_disposition [approved and governance_allowed and persistence_requested] -> durable_write
human_disposition [approved and no_persistence_requested] -> stateless_return
human_disposition [governance_unresolved] -> stateless_return
human_disposition [rejected] -> candidate_return
stateless_return -> candidate_return
durable_write -> publication_handoff
continuity_record_pending [yes and current branch] -> continuity_record_write
continuity_record_write [write and read-back complete] -> continuity_record_handoff
continuity_record_write [write or read-back failed] -> domain_authority_conflict
continuity_record_pending [missing or stale branch] -> domain_authority_conflict
continuity_record_closure [documentation consumer equals target] -> continuity_record_retained
continuity_record_closure [different documentation consumer] -> continuity_record_promoted
continuity_record_closure [no documentation consumer] -> continuity_record_removed
continuity_record_retained | continuity_record_promoted | continuity_record_removed -> continuity_record_closure_handoff
continuity_record_closure [retain, promote, read-back, removal, or absence confirmation failed] -> domain_authority_conflict
```

`publication_pending` carries the exact reviewed packet and `publication_branch`; `durable_write` is reachable only from the typed human disposition and hands the exact packet to the sole writer operation. `continuity_record_pending` is independent of the selected Grilling gap route and reaches its writer only from the exact accepted reminder answer.

## Invariants

- Grilling `with-docs` publication and accepted continuity-record reminders are the only durable-writing branches owned here. Neither changes a reviewed question, option, finding, answer, decision, or unresolved successor; each requires its exact typed branch and human disposition.
- Repository state is evidence, not automatic normative authority.
- Human-owned meaning remains unresolved until human disposition.
- Each accepted concept has one canonical term per context.
- The module performs durable writes of accepted meaning only after human disposition and records resolved meaning only in the glossary; the `with-docs` writer receives the exact reviewed packet and cannot alter it; candidate-ledger entries are recorded per the candidate-ledger invariant.
- One per-project `CONTEXT.md` serves the accepted domain glossary; candidate terminology remains packet-local until the disposition-gated durable write, and this module is its only maintainer.
- Candidate ledger: terminology discovered in the work is recorded as packet-local candidate entries — a proposed name plus a prose description of its intended datum referent — non-normative; when `CONTEXT.md` exists, a proposal may inspect its current contents at the fixed per-project path under the current read plan, but a missing file is not created and no candidate write occurs before human disposition; after exact acceptance, the durable-write branch may lazily create or update `CONTEXT.md`; a candidate reaches the assignment process only as an amendment proposal into the governed spec amendment.
- The lifecycle arm is an immutable request → proposal → exact human disposition → result chain; empty/no-change and all-rejected proposals do not write context files, partial acceptance applies only accepted entries, `CONTEXT-HISTORY.md` is created only for accepted archive content, and ADRs remain untouched.
- A lifecycle binding or disposition failure returns `context_lifecycle_blocked` to Release and cannot be consumed by effect or terminal compilation.
- A continuity write resolves by `grilling_session_id`, creates at most one working record, atomically binds its immutable target, updates that same target after every later accepted reminder, and returns represented rounds only after write and read-back succeed.
- Continuity closure uses the same session and immutable target and returns only after retained or promoted content is current or removal is confirmed.

## Recovery

- Unknown context returns `domain_gap`; conflicting owners return `domain_authority_conflict`.
- Code/document disagreement preserves both claims for human disposition.
- An unresolved trade-off remains on the owning Design ticket.
- Lifecycle requests with stale or missing bindings, incomplete coverage, authority conflict, missing or stale disposition, write/read-back failure, or exhausted retry return `context_lifecycle_blocked` to Release; no lifecycle result is emitted as current.
- A stale continuity branch or failed continuity write preserves the pending accepted rounds and returns `domain_authority_conflict`; it cannot reset Grilling's unrecorded-round count.
- Failed continuity closure preserves the session, working target, promotion target when present, pending rounds, close intent, and intended disposition, returns `domain_authority_conflict`, and emits no closure handoff.
- Continuity conflict retries only after changed state and while the bound finite retry budget remains; exhaustion returns `domain_authority_conflict` with the complete update or close state preserved.

## Terminals

`publication_handoff`, `continuity_record_handoff`, `continuity_record_closure_handoff`, `stateless_return`, `candidate_return`, `context_lifecycle_blocked`, `domain_gap`, and `domain_authority_conflict` are the only Domain Modeling terminals.

## Completion

Complete only when every blocking term and relation is defined, disputed, or routed to an owner; contradictions and authority are explicit; the applicable disposition and persistence branch are current; and affected tickets and consumer are bound. A continuity write additionally requires a matching session and immutable target, exact represented pending rounds, and successful read-back; continuity closure additionally requires a confirmed `retained`, `promoted`, or `removed` result.
