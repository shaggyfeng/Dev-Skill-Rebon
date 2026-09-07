# Codebase design

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `plan.architecture.classify` returns `greenfield_proposal`, or Plan requires a new interface or seam |
| Input | `plan.codebase-design-input.v0.1`: `task_id`, `scope_digest`, Design IDs, constraints, required behavior, modules/callers, dependencies, accepted decisions, test seams, producer, consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `architecture_evidence_required` or `design_reopened` failure. |
| Output | `plan.architecture-proposal.v0.1`: responsibilities, interfaces, seams/adapters, dependency direction, state ownership, errors/recovery, test surface, Design trace, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, consumer |
| Authority | proposal inside accepted Design meaning only |
| Failure | `architecture_evidence_required` or `design_reopened` |
| Consumer | `plan.architecture.resolve` or paired-view planning; `to-spec` after architecture resolution |

## Vocabulary

| Term | Binding |
|---|---|
| Module | unit with one Interface and one Implementation |
| Interface | every caller-visible type, invariant, ordering, error, configuration, and performance requirement |
| Implementation | behavior inside the Module; distinct from an Adapter |
| Depth | behavior exercised per unit of Interface learned; deep means substantial behavior behind a small Interface |
| Seam | location where behavior can change without editing the caller location; the Interface lives at the Seam |
| Adapter | concrete role satisfying an Interface at a Seam |
| Leverage | capability delivered per unit of Interface learned |
| Locality | concentration of change, bugs, knowledge, and verification in one Module |

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

1. Map required behavior, callers, dependencies, state, external effects, and accepted constraints.
2. Classify every dependency path against one category predicate:

| Category | Predicate | Path rule |
|---|---|---|
| `in-process` | pure computation or in-memory state with no I/O | always deepen and merge; test through the resulting Module Interface; no Adapter or external Seam |
| `local-substitutable` | an existing local stand-in satisfies the dependency's Interface within the suite | deepen with the stand-in; use an internal seam justified by production-plus-stand-in implementations; no external port or production Adapter pair |
| `remote-but-owned` | the service and its contract are controlled and the dependency crosses a network | deepen the owning Module; inject the Port Interface and transport Adapter, with in-memory tests and network production transport |
| `true-external` | the service or its contract is not controlled | inject a Port; use a mock Adapter for tests and the real service through the Port in production |

3. Freeze a path-classified cluster manifest inside the architecture proposal's Design trace, not a new file. Each dependency path records stable Module IDs for both endpoints, its category, and classification status (`included` or `excluded` with an accepted-resolution reference). Membership is per path, never per Module; reachability only surfaces and orders candidates and stops at the manifest boundary. An ungrounded category or missing accepted Design ID routes to `plan.architecture.resolve` before freezing.

4. Apply the manifest gate:

```text
all_mapped_paths_classified
 -> manifest_frozen | architecture_evidence_required
manifest_frozen
 -> included_paths_reverified | design_reopened
included_paths_reverified
 -> excluded_paths_resolved | design_reopened
excluded_paths_resolved
 -> architecture_proposal
```

Every included `in-process` path is re-verified against pure computation, in-memory state, and no I/O; every excluded path carries an accepted category-specific resolution Design ID. A missing or unaccepted resolution blocks the merge.

5. Apply the category rule for each manifest path. The `in-process` rule takes precedence over generic injected-dependency and seam guidance for that path. `local-substitutable` uses the internal stand-in seam; `remote-but-owned` uses the injected Port and production/test transport Adapters; `true-external` uses the injected Port with a test-only mock Adapter. Excluded non-in-process paths proceed only through their accepted category-specific resolution.
6. Assign cohesive behavior and state ownership to modules; the resulting Module of a merge is new and owns the merged state.
7. Define each interface by operations, inputs, outputs, invariants, ordering, errors, configuration, and performance constraints; the merged Module's Interface is designed from the manifest cluster's required behavior — deep, smaller than the behavior it hides.
8. Place seams only where behavior must vary; require at least two justified adapters before adding an adapter seam, except where a category rule binds the seam above.
9. Prefer injected dependencies and returned observable outcomes over hidden effects.
10. Apply the deletion test: removing a useful module must redistribute meaningful complexity to callers.
11. Bind verification to interfaces and trace every choice to accepted Design IDs.
12. After a merge: callers migrate to the resulting Interface; the shallow Interfaces are removed once migration completes; old unit tests on the shallow Modules are deleted once tests at the deepened Interface exist — never before; new tests assert observable outcomes through the Interface, not internal state.

## Design-It-Twice

On an explicit user trigger — alternative interfaces for a chosen deepening candidate — hold a frame state with an explicit continuation gate before any pass runs. Run up to three independent fresh-context passes with distinct constraints; each pass returns an interface, a usage example, the behavior it hides, a dependency strategy, and its trade-offs. Compare the passes on depth, locality, and seam placement; recommend one opinionated winner and stage a hybrid when elements combine. Passes run sequentially; concurrently only when the mode permits. The passes are candidates; the human resolves the normative choice.

## State machine

```text
architecture_input -> architecture_mapping
architecture_mapping -> architecture_proposal | architecture_evidence_required | design_reopened
architecture_proposal -> architecture_resolution | paired_view_planning
architecture_resolution -> paired_view_planning | to_spec | design_reopened
```

Every transition retains the current `task_id`, `scope_digest`, Design trace, and named consumer.

## Invariants

- No new product meaning, speculative seam, or test-only public interface.
- Public interfaces remain smaller than the behavior hidden behind them.
- Consequential unresolved choices route to human architecture resolution.
- Every excluded non-in-process path carries an accepted category-specific resolution before a merge.
- A test that must change when the implementation changes is testing past the Interface; old unit tests on shallow Modules are deleted only after Interface tests exist.

## Recovery

- Missing architecture evidence returns `architecture_evidence_required` to one bounded survey.
- Semantic, scope, authority, or normative change returns `design_reopened`.
- Unresolved consequential architecture choice routes to `plan.architecture.resolve`.

## Completion

Complete only when every required behavior has an owner, interface, dependency direction, state owner, error route, verification seam, Design trace, and consumer.
