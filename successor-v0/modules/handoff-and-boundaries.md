# Handoff and boundaries

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | family transition, context compaction, future-session transfer, harness/directory change, or sequential-optimized role transfer |
| Input | `runtime.boundary-input.v0.1`: `task_id`, `scope_digest`, current packet, authoritative pointers, dependency state, open gates, verification command, producer, consumer |
| Output | `runtime.boundary-result.v0.1` or `runtime.handoff.v0.1`: boundary decision, compact transfer, status, consumer |
| Authority | context transfer only |
| Failure | `handoff_blocked` or `boundary_revalidation_required` |
| Consumer | exact next module, role, session, or family |

## Operation

1. Select exactly one boundary: `continue` when conversation remains authoritative; `clear` when repository artifacts fully determine the next phase; `handoff` across session/harness/directory; `compact` within the same session and environment.
2. Do not transition mid-decision or mid-mutation when omitted context remains authoritative.
3. A handoff contains scope, authoritative pointers, accepted decisions, current task, dependencies, changes, checks, failures, open gates, labeled assumptions, exact next action, and next command.
4. Redact secrets and personal data; point to authoritative state instead of copying it.
5. The receiver validates every pointer, digest, authority, and gate before acting and reports mismatches.

## Invariants

- A handoff does not grant authority, approval, review completion, or checklist closure.
- Hidden reasoning and full transcripts are excluded.
- Only approved handoff locations may be written.

## Recovery

- Missing authority pointer returns `handoff_blocked`; stale target returns `boundary_revalidation_required`.
- Oversized handoff retains IDs, decisions, failures, gates, commands, and pointers while removing duplicated prose.
- Changed scope or consumer invalidates the handoff and requires a successor envelope.

## Completion

Complete only when a fresh consumer can identify the goal, current task, dependencies, accepted revision, open gates, verification command, authoritative artifacts, and exact next action.
