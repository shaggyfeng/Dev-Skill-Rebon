# Stage 0 / Route

## Admission

Run Route for every engineering request after Mode Gate admits the host and mode. Route owns lifecycle order, call/return edges, stage selection, and terminals only.

## Lifecycle

```text
provider and host evidence
  -> mode admission
  -> clear direct overlay
  -> or active-stage Common Sense checkpoint
  -> or project-discovery, then Common Sense checkpoint
  -> stage selection
  -> one current consumer
```

Direct overlay precedes stage continuation. An overlay returns to its current stage or requester and does not change stage itself.

## Runtime calls

| When | Load | Returns to Route |
|---|---|---|
| Mode admission is needed | [Mode Gate](../mode-gate.md) | Admitted profile or `mode_not_admitted` |
| Design or human-owned meaning is unresolved | [Grilling](modules/grilling.md), then [Review](modules/review.md) and [Decision](modules/decision.md) | Accepted rule or next question |
| A frozen surface needs findings | [Review](modules/review.md) | Findings or closure result |
| A semantic choice needs a structural representation | [Think in Decision](modules/decision.md) | Candidate representation |
| A bounded symptom exists | [Diagnosing Bugs](modules/diagnosing-bugs.md) | Diagnosis evidence |
| A bounded empirical or human-experience question exists | [Prototype](modules/prototype.md) | Non-production evidence |
| A report, conversation, roadmap, or bound document is needed | [Write](modules/write.md) | Rendered presentation |
| A controlling fact is missing or facts are under fog | [Research](modules/research.md) | Evidence or factual frontier |
| A human-only action is needed | [Manual Procedure](modules/manual-procedure.md) | Temporary procedure result |
| Teaching or practice is needed | [Teaching](modules/teaching.md) | Learning result |
| An active stage continues without an overlay | [Common Sense](modules/common-sense.md) | No reference or compact pattern reference before continuation |
| No active stage or adequate direct route exists | [Project Discovery](modules/project-discovery.md), then [Common Sense](modules/common-sense.md) | Factual orientation and optional pattern reference |

## Operation

1. Complete mode admission via `mode-gate.md` before task work. Missing admission returns `mode_not_admitted`.
2. Apply direct-overlay precedence. A family signal is eligible only with a current valid intake result when no stage is active.
3. For an active stage with no overriding direct trigger, use the matching Common Sense Runtime call, then continue the stage. Common Sense never routes or decides.
4. When no active stage or valid direct route supplies sufficient orientation, use the Project Discovery then Common Sense Runtime calls. Discovery returns only `greenfield`, `brownfield`, or `uncertain` facts.
5. Select stage:

   | Condition | Result |
   |---|---|
   | Valid direct trigger | Named overlay |
   | Active stage, no overriding direct trigger | Continue active stage |
   | Bounded outcome with current accepted design meaning | Plan |
   | Outcome, constraint, trade-off, authority, or meaning unresolved | Design |
   | Current facts cannot distinguish the above | One focused clarification |
6. Load the selected runtime module after admission and before execution. Check its trigger, prerequisites, authority limit, return route, and consumer.
7. Compile exactly one Route terminal.

## Invariants

- Route is a lifecycle skeleton; modules own detailed operations. Every operation has one owner.
- Mode Gate alone owns provider/host evidence, suggestion, governance, mode, host, roadmap intent, admission, and re-entry.
- Direct overlay precedes active-stage continuation; overlay returns to its stage or requester without changing stage.
- Project Discovery runs only when no active stage or valid direct route supplies sufficient orientation; returns facts only.
- Common Sense checkpoints return no reference or one compact reference; never route, decide, or dispatch.
- Every Design or Plan entry from brownfield discovery carries artifact-touch classification and convention bindings.

## Recovery

- Missing or stale admission returns `mode_not_admitted` without beginning task work.
- Unresolved stage intent returns one human clarification.
- Missing convention files are silent; discovery never creates setup artifacts.

## Terminals

`active_stage_continuation`, `stage_handoff`, `overlay_return`, `clarification_required`, and `live_fallback` are the only Route terminals.
