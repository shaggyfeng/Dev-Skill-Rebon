# domain-modeling reference

---

**name:** `domain-modeling`

**description:** Build and sharpen a project's domain model. Use when the user wants to pin down domain terminology or a ubiquitous language, record an architectural decision, or when another skill needs to maintain the domain model.

# Domain Modeling

Actively build and sharpen the domain model as you design: challenge terms, invent edge-case scenarios, and write the glossary and decisions down the moment they crystallise. (Merely *reading* `CONTEXT.md` for vocabulary isn't this skill — that's a one-line habit any skill can do; this one is for *changing* the model.)

## File structure

Single context (most repos): `CONTEXT.md` at the root, ADRs in `docs/adr/` (`0001-event-sourced-orders.md`, …). Multi-context: a `CONTEXT-MAP.md` at the root points to each `src/<context>/CONTEXT.md` plus its context-specific `docs/adr/`; system-wide ADRs live in the root `docs/adr/`. Create all files lazily — only when there's something to write.

## During the session

- **Challenge against the glossary** — a term conflicting with `CONTEXT.md` gets called out immediately: "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"
- **Sharpen fuzzy language** — vague or overloaded terms get a proposed canonical term: "'account' — Customer or User? Different things."
- **Discuss concrete scenarios** — stress-test domain relationships with edge-case scenarios forcing precision about concept boundaries.
- **Cross-reference with code** — when the user states how something works, check the code agrees; surface contradictions ("your code cancels entire Orders; you just said partial cancellation is possible — which is right?").
- **Update CONTEXT.md inline** as terms resolve — never batched; use [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md). `CONTEXT.md` is a glossary and nothing else: devoid of implementation details, never a spec, scratch pad, or decision repository.
- **Offer ADRs sparingly** — only when all three hold: **hard to reverse** (changing your mind later costs something meaningful), **surprising without context** (a future reader will wonder why), and **a real trade-off** (genuine alternatives existed; one was picked for specific reasons). Any missing → skip. Use [ADR-FORMAT.md](./ADR-FORMAT.md).

### Reference: domain-modeling/ADR-FORMAT.md

# ADR Format

ADRs live in `docs/adr/` (created lazily), sequentially numbered `0001-slug.md`, `0002-slug.md`, … (scan for the highest number, increment).

**Template:**

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it — an ADR can be one paragraph; the value is recording *that* a decision was made and *why*, not filling out sections. Optional sections only when they add genuine value: **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) when decisions get revisited; **Considered Options** when the rejected alternatives are worth remembering; **Consequences** for non-obvious downstream effects.

**What qualifies:** architectural shape ("the write model is event-sourced; the read model projects into Postgres"); integration patterns between contexts ("Ordering and Billing communicate via domain events, not synchronous HTTP"); lock-in-carrying technology choices (database, message bus, auth provider, deployment target — not every library, just the quarter-to-swap ones); boundary and scope decisions ("Customer data is owned by the Customer context; others reference it by ID only" — explicit no-s are as valuable as yes-s); deliberate deviations from the obvious path ("manual SQL instead of an ORM because X" — stops the next engineer "fixing" something deliberate); constraints invisible in code ("no AWS for compliance; response times under 200ms for the partner contract"); non-obvious rejections (GraphQL-considered-REST-chosen, or someone re-proposes it in six months).

### Reference: domain-modeling/CONTEXT-FORMAT.md

# CONTEXT.md Format

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A one or two sentence description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

**Rules:** be opinionated — when multiple words exist for one concept, pick the best and list the rest under `_Avoid_`; keep definitions tight (1–2 sentences, define what it IS, not what it does); include only terms specific to this project's context — general programming concepts (timeouts, error types, utility patterns) never belong, however heavily used; group terms under subheadings when natural clusters emerge (a flat list is fine for one cohesive area).

**Single vs multi-context:** `CONTEXT-MAP.md` exists → read it to find contexts; only root `CONTEXT.md` → single context; neither → create a root `CONTEXT.md` lazily at first resolved term. With multiple contexts, infer which one the current topic relates to; if unclear, ask. A context map lists the contexts and their relationships:

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md) — receives and tracks customer orders
- [Billing](./src/billing/CONTEXT.md) — generates invoices and processes payments
- [Fulfillment](./src/fulfillment/CONTEXT.md) — manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched`; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

---
