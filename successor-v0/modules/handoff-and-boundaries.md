# Handoff and boundaries

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | family transition, context compaction, future-session transfer, harness/directory change, sequential-optimized role transfer, or bounded sequential read-slice transfer in either sequential mode |
| Input | `runtime.boundary-input.v0.1`: `task_id`, `scope_digest`, current packet, authoritative pointers, dependency state, optional read-plan digest and read frontier, open gates, verification command, producer, consumer, retry_budget |
| Output | `runtime.boundary-result.v0.1` or `runtime.handoff.v0.1`: boundary decision, compact transfer, optional completed-coverage and remaining-frontier bindings, retry_budget, status, consumer |
| Authority | context transfer only |
| Failure | `handoff_blocked` or `boundary_revalidation_required` |
| Consumer | exact next module, role, session, or family |

## Operation

1. Select exactly one boundary: `continue` when conversation remains authoritative; `clear` when repository artifacts fully determine the next phase; `handoff` across session/harness/directory; `compact` within the same session and environment.
2. Do not transition mid-decision or mid-mutation when omitted context remains authoritative.
3. A handoff contains scope, authoritative pointers, accepted decisions, current task, dependencies, changes, checks, failures, open gates, labeled assumptions, exact next action, and next command.
4. Redact secrets and personal data; point to authoritative state instead of copying it.
5. The receiver validates every pointer, digest, authority, gate, and retry budget before acting and reports mismatches.
6. For a sequential read-slice transfer, bind the current `runtime.read-plan.v0.1`, completed coverage-unit IDs and fragment digests, remaining ordered `runtime.read-frontier.v0.1`, accumulator digest, budgets remaining, remaining `retry_budget` and its decrement record, and exact next unit. The receiver revalidates these bindings before reading content.

## Invariants

- A handoff does not grant authority, approval, review completion, or checklist closure.
- Hidden reasoning and full transcripts are excluded.
- Only approved handoff locations may be written.
- A read handoff transfers coverage state only; it cannot silently drop, merge, duplicate, or expand a coverage unit.

## Recovery

- Missing authority pointer returns `handoff_blocked`; stale target returns `boundary_revalidation_required`.
- Oversized handoff retains IDs, decisions, failures, gates, commands, and pointers while removing duplicated prose.
- Changed scope or consumer invalidates the handoff and requires a successor envelope with a current retry budget.
- Missing, stale, duplicate, or undeclared read coverage returns `boundary_revalidation_required`; oversized remaining scope requires a smaller successor read plan. Zero retry budget returns `handoff_blocked` or `boundary_revalidation_required` without another dispatch.

## Completion

Complete only when a fresh consumer can identify the goal, current task, dependencies, accepted revision, open gates, verification command, authoritative artifacts, exact next action, and current retry budget; a read-slice transfer additionally requires current completed coverage, remaining frontier, accumulator, budgets, and next-unit bindings.
