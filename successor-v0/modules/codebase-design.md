# Codebase design

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | `plan.architecture.classify` returns `greenfield_proposal`, or Plan requires a new interface or seam |
| Input | `plan.codebase-design-input.v0.1`: `task_id`, `scope_digest`, Design IDs, constraints, required behavior, modules/callers, dependencies, accepted decisions, test seams, producer, consumer |
| Output | `plan.architecture-proposal.v0.1`: responsibilities, interfaces, seams/adapters, dependency direction, state ownership, errors/recovery, test surface, Design trace, status, consumer |
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

1. Map required behavior, callers, dependencies, state, external effects, and accepted constraints.
2. Classify every dependency path by its category predicate: in-process — pure computation or in-memory state with no I/O; local-substitutable — an existing local stand-in satisfies the dependency's Interface within the suite; remote-but-owned — you control the service and its contract, and the dependency crosses a network; true-external — you do not control the service or its contract.
3. Freeze a path-classified cluster manifest covering every mapped dependency path — a verification record inside the architecture proposal's Design trace, not a new file — traced to accepted Design IDs: stable Module IDs for both endpoints, the path category, and classification status — included, or excluded with its accepted-resolution reference. Membership is per dependency path, never per Module; a Module joined by an excluded path contributes only its included paths. Dependency-graph reachability is evidence only: traversal surfaces and orders candidates, never determines membership, and stops at the manifest boundary. A classification the module cannot ground in the category predicates or accepted Design IDs routes to `plan.architecture.resolve` before the manifest freezes; mechanically grounded classifications proceed.
4. Merge gate: every manifest path is classified, each included in-process path is re-verified against the in-process atoms — pure computation, in-memory state, no I/O — and every excluded path carries an accepted category-specific resolution Design ID; a missing or unaccepted resolution blocks the merge.
5. Apply each path's own category rule — an included in-process path through the cluster merge, and each excluded non-in-process path through its accepted category-specific resolution formed from its category rule; the in-process rule takes precedence over generic injected-dependency and seam guidance for its path:
   - in-process — always deepen; merge the selected shallow Modules; test through the resulting Module Interface; no Adapter and no external Seam;
   - local-substitutable — deepen; test with the stand-in in the suite; the seam is internal, justified by production-plus-stand-in implementations; no external port and no production Adapter pair;
   - remote-but-owned — the deepened Module owns the logic; the Port is the Interface at the injected seam; transport is injected as an Adapter, in-memory for tests and the network transport for production; the external seam is justified by the production and test adapters;
   - true-external — take the dependency as an injected port; a mock adapter serves tests only; production uses the real service through the port, justified by the client-plus-mock pair.
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
