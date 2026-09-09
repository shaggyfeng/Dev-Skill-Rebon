# Stage 0 / Route

## Admission

Run Route for every engineering request after Mode Gate admits the host and mode. Route owns lifecycle order, call/return edges, stage selection, and terminals only.

## Lifecycle

```text
provider and host evidence
  -> mode admission
  -> clear direct overlay
  -> or accepted active-stage continuation
  -> or indirect intent to Decision
  -> or project-discovery, then Common Sense checkpoint
  -> stage selection
  -> one current consumer
  -> first owner checkpoint
```

Direct overlay precedes stage continuation. An overlay returns to its current stage or requester and does not change stage itself.

## Runtime calls

| When | Load | Returns to Route |
|---|---|---|
| Mode admission is needed | [Mode Gate](../mode-gate.md) | Admitted profile or `mode_not_admitted` |
| An indirect engineering thought, concern, preference, constraint, or goal does not mechanically determine its next operation | [Decision](modules/decision.md) | Human Intent State, factual gap, or human question; Route selects the next consumer |
| A direct human-owned design choice is unresolved | [Grilling](modules/grilling.md) | Accepted rule or next question |
| A frozen surface needs findings | [Review](modules/review.md) | Findings or closure result |
| A bounded symptom exists | [Diagnosing Bugs](modules/diagnosing-bugs.md) | Diagnosis evidence |
| A bounded empirical or human-experience question exists | [Prototype](modules/prototype.md) | Non-production evidence |
| A report, conversation, roadmap, or bound document is needed | [Write](modules/write.md) | Rendered presentation |
| A controlling fact is missing or facts are under fog | [Research](modules/research.md) | Evidence or factual frontier |
| A human-only action is needed | [Manual Procedure](modules/manual-procedure.md) | Temporary procedure result |
| Teaching or practice is needed | [Teaching](modules/teaching.md) | Learning result |
| An active stage continues without an overlay | [Common Sense](modules/common-sense.md) | No reference or compact pattern reference before continuation |
| No active stage or adequate direct route exists | [Project Discovery](modules/project-discovery.md), then [Common Sense](modules/common-sense.md) | Factual orientation and optional pattern reference |
| Route has selected one family or overlay owner | [Host Enforcement](modules/host-enforcement.md) | Enter that owner's first declared checkpoint; capability status remains checkpoint-scoped |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| One family or overlay owner is selected | `consumer_selected` | Host Enforcement enters the selected owner's first declared checkpoint |
| Route produces a terminal | `terminal_compiled` | Return the exact Route terminal; no owner entry |

## Operation

1. Complete mode admission via `mode-gate.md` before task work. Missing admission returns `mode_not_admitted`.
2. Apply direct-overlay precedence. An explicit valid overlay or an accepted in-scope continuation stays direct. A family signal is eligible only with a current valid intake result when no stage is active.
3. When an indirect engineering intent does not mechanically determine its next operation, use the Decision Runtime call before discovery or stage selection. Decision returns Human Intent State, a factual gap, or a human question; it does not select a stage, module, or terminal.
4. For an accepted active-stage continuation with no overriding direct trigger, use the matching Common Sense Runtime call, then continue the stage. Common Sense never routes or decides.
5. When no active stage or valid direct route supplies sufficient orientation, use the Project Discovery then Common Sense Runtime calls. Discovery returns only `greenfield`, `brownfield`, or `uncertain` facts.
6. Select stage:

   | Condition | Result |
   |---|---|
   | Valid direct trigger | Named overlay |
   | Accepted in-scope continuation | Continue active stage |
   | Bounded outcome with current accepted design meaning | Plan |
   | Outcome, constraint, trade-off, authority, or meaning unresolved | Design |
   | Current facts cannot distinguish the above | One focused clarification |
7. When the selected result has a family or overlay owner, load its runtime module after admission and before execution. Check its trigger, prerequisites, authority limit, return route, consumer, and first declared checkpoint.
8. When a family or overlay owner is selected, use the Host Enforcement Runtime call to enter that owner's first declared checkpoint. `instruction-guided` still requires that operation; it never becomes an `enforced` claim.
9. When Route produces a terminal, compile exactly that terminal without a Host Enforcement entry.

## Invariants

- Route is a lifecycle skeleton; modules own detailed operations. Every operation has one owner.
- Mode Gate alone owns provider/host evidence, suggestion, governance, mode, host, roadmap intent, admission, and re-entry.
- `admitted` is profile capability only. A `current operation` starts only after Route selects an owner and its first checkpoint is entered.
- Direct overlay precedes active-stage continuation; overlay returns to its stage or requester without changing stage.
- Indirect engineering intent that does not determine its next operation enters Decision. Decision returns Human Intent State, a factual gap, or a human question only; Route alone selects its consumer and terminal.
- A Route terminal has no owner and never enters an owner checkpoint.
- Project Discovery runs only when no active stage or valid direct route supplies sufficient orientation; returns facts only.
- Common Sense checkpoints return no reference or one compact reference; never route, decide, or dispatch.
- Every Design or Plan entry from brownfield discovery carries artifact-touch classification and convention bindings.

## Recovery

- Missing or stale admission returns `mode_not_admitted` without beginning task work.
- A rejected enforced checkpoint returns to its active owning operation without changing state; Route does not select its recovery.
- Unresolved stage intent returns one human clarification.
- Missing convention files are silent; discovery never creates setup artifacts.

## Terminals

`active_stage_continuation`, `stage_handoff`, `overlay_return`, `clarification_required`, and `live_fallback` are the only Route terminals.
