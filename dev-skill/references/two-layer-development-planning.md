# Two-layer development planning

Use this method for a consequential multi-session workflow whose branch logic and transferred structures cannot be inspected reliably in one ordinary roadmap. Use it with the applicable planning, grilling, deep-review, specification, ticketing, and implementation references; it does not replace them.

## Maintain two synchronized human-facing views

1. Maintain a **Workflow Evolution View** showing entry conditions, operations, branches, retries, checkpoints, recovery routes, terminal boundaries, next consumers, and exact status.
2. Maintain a **Structure and Authority View** showing what enters and leaves every consequential step, who supplies and transforms it, who validates or decides it, which later step consumes it, and what authority it gains or explicitly does not gain.

Treat the paired views as the human-facing design authority for intended behavior only. They cannot replace exact human resolutions, ADRs, schemas, controller state, accepted specifications, tests, or evidence.

## Describe each consequential transfer

For every consequential node or arrow, record:

| Field | Required content |
|---|---|
| Step and status | Stable identity plus `candidate`, `approved_unimplemented`, `implemented`, or `historical` |
| Semantic input | Meaning, observation, evidence, or decision being consumed |
| Representation | Exact bounded artifact, candidate, bundle, brief, state projection, or record carrying it |
| Input source | Human, controller state, model candidate, catalog, resource, or external system |
| Operation actor | Actor allowed to retrieve, propose, transform, compile, or present it |
| Output structure | Exact artifact, candidate, decision, evidence package, checkpoint, or transition emitted |
| Validator or decision actor | Actor allowed to validate shape, evidence, protected meaning, value judgment, publication, or authorization |
| Next consumer | Exact later workflow step permitted to receive it |
| Authority effect | Authority created and authority explicitly not created |

Do not trace trivial internal helpers when they do not affect a public seam, authority boundary, phase handoff, or independently testable outcome.

## Operating loop

1. Draw or revise the smallest complete Workflow Evolution View for the current design subject.
2. Mark every node and branch with its truthful status.
3. Complete the matching Structure and Authority rows at semantic, representation, and authority levels.
4. Find missing inputs, undefined outputs, circular dependencies, wrong actors, unjustified authority gain, and consumers unable to use what they receive.
5. Inspect repository facts directly. Ask the human only for normative choices exposed by the paired views.
6. Apply accepted human decisions and reviewer fixes to both views in the same revision.
7. Verify every workflow arrow transfers a named structure and every structure has one bounded next consumer or typed terminal route.
8. Convert the accepted paired design into specifications and vertical tickets without inventing new runtime meaning.
9. Derive a separate Structural Trace Ledger for implementation handoff only when the work spans consequential development units.

## Derived implementation handoff

The Structural Trace Ledger is not a third design layer. For each consequential development unit, bind the exact governing design and decisions to admitted inputs, exclusions, promised transformation, Structural Output Contract, invariants, validation and review evidence, terminal state, and next consumer.

An implementer reads the paired design to understand what must exist and why, then uses the ledger to determine the bounded unit and required return evidence. If the ledger conflicts with the design or another governing artifact, stop and route an unresolved conflict to the human-facing design workflow. Correct the ledger from the governing design; never change the design merely to match a stale ledger.

## Completion checks

- Every branch has a named next step, typed block, recovery route, or human gate.
- Every arrow transfers a named semantic structure in a bounded representation.
- Every receiver has permission to consume the structure for the stated purpose.
- Human protected meaning and normative judgment remain human-owned.
- Model proposals remain candidates until deterministic validation and required human gates pass.
- Proposal, validation, confirmation, acceptance, publication, planning approval, mutation, and execution authorization remain distinct.
- Implemented, approved-unimplemented, candidate, and historical behavior remain visibly distinct.
- A fresh session can continue from durable artifacts without hidden reasoning, full chat history, or cache state.
