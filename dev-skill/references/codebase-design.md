# codebase-design reference

---

**name:** `codebase-design`

**description:** Shared vocabulary for designing deep modules. Use when the user wants to design or improve a module's interface, find deepening opportunities, decide where a seam goes, make code more testable or AI-navigable, or when another skill needs the deep-module vocabulary.

# Codebase Design

Design **deep modules**: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface — leverage for callers, locality for maintainers, testability for everyone. Use this language wherever code is designed or restructured.

## Glossary

Use these terms exactly — don't substitute "component", "service", "API", or "boundary"; consistent language is the whole point.

- **Module** — anything with an interface and an implementation; scale-agnostic (function, class, package, tier-spanning slice). *Avoid:* unit, component, service.
- **Interface** — everything a caller must know to use the module correctly: type signature plus invariants, ordering constraints, error modes, required configuration, performance characteristics. *Avoid:* API, signature (too narrow).
- **Implementation** — what's inside a module. Distinct from **Adapter**: a thing can be a small adapter with a large implementation (Postgres repo) or a large adapter with a small implementation (in-memory fake). Say "adapter" when the seam is the topic, "implementation" otherwise.
- **Depth** — leverage at the interface: behaviour exercisable per unit of interface learned. **Deep** = much behaviour behind a small interface; **shallow** = interface nearly as complex as the implementation.
- **Seam** _(Feathers)_ — a place where you can alter behaviour without editing in that place; the *location* where a module's interface lives. Seam placement is its own design decision. *Avoid:* boundary (overloaded with DDD's bounded context).
- **Adapter** — a concrete thing satisfying an interface at a seam; describes *role*, not substance.
- **Leverage** — what callers get from depth: capability per unit of interface learned; one implementation pays back across N call sites and M tests.
- **Locality** — what maintainers get from depth: change, bugs, knowledge, and verification concentrate in one place. Fix once, fixed everywhere.

## Deep vs shallow

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│  Deep Implementation│  ← Complex logic hidden
└─────────────────────┘
```

A **shallow module** inverts this: large interface, thin pass-through implementation — avoid. When designing an interface, ask: can I reduce methods? simplify parameters? hide more complexity inside?

## Principles

- **Depth is a property of the interface, not the implementation.** A deep module can be internally composed of small, swappable parts that aren't part of the interface — internal seams (private, used by its own tests) as well as the external seam.
- **The deletion test.** Imagine deleting the module: if complexity vanishes, it was a pass-through; if it reappears across N callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam; wanting to test *past* the interface means the module is the wrong shape.
- **One adapter means a hypothetical seam; two adapters means a real one.** Introduce no seam unless something actually varies across it.

## Designing for testability

1. **Accept dependencies, don't create them** — `processOrder(order, paymentGateway)` not `processOrder(order)` constructing its own `StripeGateway`.
2. **Return results, don't produce side effects** — `calculateDiscount(cart): Discount` not `applyDiscount(cart): void` mutating in place.
3. **Small surface area** — fewer methods = fewer tests; fewer params = simpler setup.

## Relationships

A Module has exactly one Interface; Depth is a Module property measured against its Interface; a Seam is where the Interface lives; an Adapter sits at a Seam satisfying the Interface; Depth produces Leverage for callers and Locality for maintainers.

## Rejected framings

- Depth as ratio of implementation-lines to interface-lines (Ousterhout) — rewards padding; we use depth-as-leverage.
- "Interface" as the TS `interface` keyword or public methods — too narrow; ours includes every fact a caller must know.
- "Boundary" — overloaded; say **seam** or **interface**.

## Going deeper

- Deepening a cluster given its dependencies — [DEEPENING.md](DEEPENING.md).
- Exploring alternative interfaces — [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md).

### Reference: codebase-design/DEEPENING.md

# Deepening

How to deepen a cluster of shallow modules safely, given its dependencies, using the SKILL vocabulary (**module**, **interface**, **seam**, **adapter**). Classify each dependency — the category determines how the deepened module is tested across its seam:

1. **In-process** — pure computation, in-memory state, no I/O. Always deepenable: merge the modules, test through the new interface directly, no adapter.
2. **Local-substitutable** — dependencies with local test stand-ins (PGLite for Postgres, in-memory filesystem). Deepenable if the stand-in exists; test with the stand-in in the suite. The seam is internal — no port at the external interface.
3. **Remote but owned (Ports & Adapters)** — your own services across a network (microservices, internal APIs). Define a **port** (interface) at the seam; the deep module owns the logic; transport is injected as an **adapter** — in-memory for tests, HTTP/gRPC/queue for production. Recommendation shape: *"Define a port at the seam, implement an HTTP adapter for production and an in-memory adapter for testing, so the logic sits in one deep module even though it's deployed across a network."*
4. **True external (Mock)** — third-party services you don't control (Stripe, Twilio). The deepened module takes the dependency as an injected port; tests provide a mock adapter.

**Seam discipline:** one adapter = hypothetical seam, two = real — introduce no port until at least two adapters are justified (typically production + test); a single-adapter seam is just indirection. Don't expose internal seams through the interface just because tests use them.

**Testing strategy — replace, don't layer:** old unit tests on shallow modules become waste once tests at the deepened interface exist — delete them; write new tests at the interface (the interface is the test surface); assert on observable outcomes through the interface, not internal state; tests survive internal refactors — if a test must change when the implementation changes, it's testing past the interface.

### Reference: codebase-design/DESIGN-IT-TWICE.md

# Design It Twice

When the user explicitly wants alternative interfaces for a chosen deepening candidate: independent fresh-context passes — sequentially in single-model mode, concurrently in bounded-parallel mode. Based on Ousterhout's "Design It Twice": your first idea is unlikely to be the best. Uses the SKILL vocabulary plus CONTEXT.md domain terms.

1. **Frame the problem space.** Write a user-facing explanation of the candidate: constraints any new interface must satisfy; dependencies and their [DEEPENING.md](DEEPENING.md) categories; a rough illustrative code sketch grounding the constraints (not a proposal). Show the user; proceed only after they explicitly ask to continue.
2. **Run independent design passes.** Up to three fresh-context passes (concurrent when mode-permitted — no shared candidate context; sequential otherwise), each producing a **radically different** interface. Each gets a separate technical brief (file paths, coupling, dependency category, what sits behind the seam) independent of the step-1 user-facing explanation, and a different constraint: (1) minimize the interface — 1–3 entry points, maximize leverage; (2) maximize flexibility — many use cases and extension; (3) optimize the most common caller — trivial default case; (4, if applicable) ports & adapters for cross-seam dependencies. Each pass outputs: the interface (types, methods, params, invariants, ordering, error modes); a usage example; what the implementation hides; dependency strategy and adapters; trade-offs — where leverage is high, where thin. Afterward a comparison-and-integration card evaluates, recommends one, stages any hybrid; the human resolves any normative architecture choice.
3. **Present and compare.** Present sequentially so the user absorbs each, then compare in prose on **depth** (leverage at the interface), **locality** (where change concentrates), and **seam placement**. Give an opinionated recommendation — a strong read, not a menu — and propose a hybrid when elements combine well.

---
