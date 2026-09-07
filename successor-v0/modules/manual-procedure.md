# Manual Procedure

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | explicit temporary human-action trigger; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | a bounded action requires clicks, account access, legal or physical confirmation, unavailable agent capability, or another human-only step |
| Input | `manual-procedure.request.v0.1`: `task_id`, `scope_digest`, goal, ordered stages, captured values, destinations, secrecy classes, irreversible actions, available evidence, verification scope, source/destination digests, persistence preference, producer, and consumer |
| Read plan | Current `runtime.read-plan.v0.1`, coverage accumulator/frontier, per-unit budgets, source freshness identity, and finite retry budget by reference to `SKILL.md`; validate before the first content read, preserve `read_plan_digest`, `coverage_digest`, and remaining read frontier when applicable in the result, and route an unusable binding through the existing `manual_procedure_blocked` or `manual_procedure_invalidated` failure. |
| Output | `manual-procedure.packet.v0.1`: ordered action/result stages, confirmation gates with `confirmation_actor` and `confirmation_digest` when required, destinations, secrecy handling, verification, source/destination digests, temporary-artifact disposition, returned evidence request, `persistence_decision`, optional `manual-procedure.retention-handoff.v0.1`, `read_plan_digest`, `coverage_digest`, remaining read frontier when applicable, status, and consumer |
| Authority | temporary human-action guidance only |
| Failure | `manual_procedure_blocked` or `manual_procedure_invalidated` |
| Consumer | `stage-0.write`, `handoff`, the requesting family, or human return |

No approved Plan or Work handoff is required when the procedure performs no repository implementation. The procedure cannot publish decisions, authorize execution, or directly write governed artifacts.

## Operation

Before step 1 or any later content read, validate the current Read plan binding above and admit only its coverage units; carry its read-plan and coverage digests and remaining frontier through the result.

1. Confirm that the requested work is genuinely human-only or unavailable to the agent. A safe admitted controller or local tool owns a step instead of this module.
2. Scope every manual stage with one action, captured value, destination, secrecy class, confirmation gate, irreversible-action marker, and expected result. Separate values that may be shown from secrets that must remain hidden.
3. Present the complete ordered stage list before authoring detailed instructions. When any stage includes an irreversible action or external account change, enter `confirmation_requested` and wait for a human confirmation carrying `confirmation_actor` and `confirmation_digest`; without that evidence, do not enter `drafting_authorized`. A safe stage list may proceed directly to drafting.
4. Verify unknown third-party details from primary documentation before including UI paths, commands, copy locations, or other operational steps. Do not invent a URL, dashboard path, command, or destination.
5. Author each stage as one focused task with a confirmation gate, explicit output, and idempotent write location where a write is unavoidable. Trace every first-stage value to its declared destination.
6. Prepare bounded input or temporary artifacts only inside the admitted scope. Keep secrets out of logs, reports, handoffs, and durable records.
7. Syntax-check or statically validate the procedure without performing irreversible actions. Record external verification separately from the human's returned result.
8. Use `stage-0.write` for the requested rendering and `handoff` for the human's returned evidence. The procedure is temporary by default; when durable retention is explicitly requested and authorized, compile a typed `manual-procedure.retention-handoff.v0.1` for the named durable consumer without writing governed artifacts from this module.
9. Return incomplete, blocked, or invalidated stages with their exact next human action; do not silently skip a failed confirmation gate.

## State machine

```text
request_received -> human_only_confirmed | manual_procedure_blocked
human_only_confirmed -> stages_scoped
stages_scoped -> stage_list_presented
stage_list_presented [irreversible_or_external=false] -> drafting_authorized
stage_list_presented [irreversible_or_external=true] -> confirmation_requested | human_revision_required
confirmation_requested -> confirmation_received | human_revision_required | manual_procedure_blocked
confirmation_received -> drafting_authorized
human_revision_required -> stages_scoped
drafting_authorized -> primary_details_verified | manual_procedure_blocked
primary_details_verified -> packet_rendered | manual_procedure_invalidated
packet_rendered -> syntax_verified | manual_procedure_invalidated
syntax_verified -> human_execution | manual_procedure_invalidated
human_execution -> results_handed_off | stage_failed | manual_procedure_invalidated
stage_failed -> results_handed_off | manual_procedure_blocked
results_handed_off -> temporary_complete | durable_retention_requested
durable_retention_requested [persistence_decision=authorized] -> durable_retention_handoff
durable_retention_requested [persistence_decision=declined or missing] -> temporary_complete | manual_procedure_blocked
manual_procedure_invalidated -> stages_scoped | manual_procedure_blocked
```

## Invariants

- Each stage has one action, one expected result, one destination, one confirmation gate, and one secrecy classification.
- Secret values remain hidden and never enter logs, handoffs, reports, or durable records.
- Irreversible actions are disclosed and cannot reach `drafting_authorized` without a recorded human confirmation actor and digest; verification never performs them.
- Third-party details come from verified primary documentation; unknown details remain unresolved.
- The module guides or prepares human work but cannot publish decisions, authorize execution, or write governed artifacts.
- A procedure is temporary by default and creates no durable workspace record without an explicit retention request.
- A procedure with no repository implementation does not require an approved Plan or Work handoff.
- Returned evidence follows the declared Handoff consumer and cannot be treated as approval by itself.

## Recovery

- A non-human-safe or safely automatable step returns `manual_procedure_blocked` to the owning family or controller.
- Missing destination, secrecy class, confirmation gate, or expected result blocks the affected stage.
- Unverified external details remain blocked; do not guess the path or URL.
- An invalidated destination, source, or seam reopens the affected stage and discards only dependent instructions; the successor stage carries a current digest before rendering or execution resumes.
- A failed human confirmation returns the stage for revision or explicit cancellation.
- Requested durable retention without an explicit persistence decision remains temporary.
- A durable-retention request completes only as a typed handoff to its named consumer; this module never performs the durable write.

## Completion

Complete only when every ordered stage is scoped, primary details are verified, confirmation and secrecy gates are explicit, syntax/static validation passes without irreversible execution, temporary-artifact disposition and any retention handoff are bound, the returned evidence and Handoff consumer are named, and no governed authority is implied.
