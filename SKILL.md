---
name: dev-skill
description: Mandatory engineering route for repository inspection, planning, implementation, diagnosis, testing, review, refactoring, release, and roadmap closure.
when_to_use: Use for every software-engineering task. Do not use for unrelated non-engineering requests.
user-invocable: true
disable-model-invocation: false
---

# DevSkill runtime

Use only this package for DevSkill runtime instructions. The package contains runtime rules, not records, generated evidence, or controller state.

## Entry and load order

1. Read `manifest.json` for the runtime-file set.
2. When admission is needed, load [Mode Gate](mode-gate.md); it returns an admitted profile or `mode_not_admitted` to [Route](successor-v0/stage-0-route.md).
3. When admission succeeds, load [Route](successor-v0/stage-0-route.md); it returns one family, overlay, or terminal.
4. When Route selects a family, load that family and only the modules named by its Runtime calls; each returns to its declared consumer.
5. When findings are needed, load [Review](successor-v0/modules/review.md); it returns findings to the calling operation. When the admitted host is Rebon, load [Rebon host adapter](successor-v0/modules/rebon-host-adapter.md); it returns native results to its declared consumer.

Resolve all paths from this package root. Keep runtime checkpoints internal. State the selected host, mode, subagent use, and context optimization once when admission completes.

## Runtime references

A cross-runtime reference is a load instruction, not prose. Declare it once at its caller with this shape:

| Field | Required content |
|---|---|
| When | The condition that needs the module or term |
| Load | A Markdown link to its runtime file |
| Return or use | The exact result or structural information and its consumer |

For an imported structural term, `Load` means read its owning module before using the term. A later mention in the same file reuses its declared reference; a bare module or imported-term name is never a new call.

## Universal route

Route every engineering request before family work. Direct overlays precede stage continuation. With no active stage or valid overlay, Route performs factual discovery before selecting Design or Plan.

| When | Load | Return to |
|---|---|---|
| A design choice or meaning is unresolved | [Grilling](successor-v0/modules/grilling.md) | Accepted rule or next question to the caller |
| An output is semantic | [Review](successor-v0/modules/review.md), then [Decision](successor-v0/modules/decision.md) | Findings, then semantic disposition to the caller |
| Inspection is findings-only | [Review](successor-v0/modules/review.md) | Findings to the caller |
| A controlling fact or factual path is missing | [Research](successor-v0/modules/research.md) | Evidence or factual frontier to the caller |
| A prototype question is bounded | [Prototype](successor-v0/modules/prototype.md) | Bounded evidence to the caller |
| A Work symptom is bounded | [Diagnosing Bugs](successor-v0/modules/diagnosing-bugs.md) | Diagnosis evidence to Work |
| Presentation is bound | [Write](successor-v0/modules/write.md) | Rendered bound meaning to the caller |
| A Design result hands off | [Plan](successor-v0/plan.md) | Approved Work handoff or upstream return |
| A Plan slice is authorized | [Work](successor-v0/work.md) | Integrated result or upstream reopen |
| A Work result is ready | [Release](successor-v0/release.md) | Release effect or reopen |

User naming a module is sufficient when its local prerequisites hold. Every module also has a non-user trigger.

When bound presentation needs a form, load [Write](successor-v0/modules/write.md); it returns rendered meaning to the caller. When Write selects a non-instructional meeting-style, deep, multi-turn, or long human-facing conversation, `lead_ungoverned` output without an explicit setting, or a King setting, load [Talk Like King](successor-v0/modules/talk-like-king.md); it returns styled presentation to Write. When Write receives an explicit setting or long-form creative work, load [Writing Style](successor-v0/modules/writing-style.md); it returns selected settings or a King route to Write.

## Module contract

Each module owns its trigger, prerequisite, operation, result, authority limit, failure or re-entry route, and consumer. A family owns only lifecycle order, calls, returns, and terminals.

```text
Does an accepted rule uniquely determine this output?
  yes -> mechanical or evidentiary route
  no  -> does it create, remove, recommend, select, narrow,
         prioritize, trade off, authorize, or record an option?
           yes -> Review -> Decision
           no  -> ordinary module consumer
```

Do not add a module, artifact, verification loop, state wrapper, fallback, or record merely because it appears safer. Retain a rule only when it changes a current read, question, choice, action, return, or human Value Gap.

## Context and review

When an admitted optimized operation needs context-bearing work, load [Context Optimization](successor-v0/modules/context-optimization.md); it returns bounded fragments or a read-free synthesis to that operation. A normal-mode capacity failure returns to [Mode Gate](mode-gate.md) with an optimized mode suggested.

| Surface | Findings assignment |
|---|---|
| Grilling | Main agent: Standards and Specification |
| Implementation slice | Separate Standards and Specification passes, both focused on runtime logic |
| Roadmap closure or merge | Standards + Correctness and Specification + Correctness |

Implementation review is a barrier for one slice. Correct, review again, and verify before a dependent slice starts. Review returns findings only.

## Re-entry

After every module result, silently return to the active family and its [Route](successor-v0/stage-0-route.md) edge. When a stage change, review result, semantic change, host or mode change, failed tool call, or resume occurs, load the current family and the producing and consuming modules; their declared return determines the next consumer. A sequential-optimized continuation also loads [Context Optimization](successor-v0/modules/context-optimization.md) for its continuation structure.

Never print internal checkpoints, ledgers, retries, fragments, or worker results.

## Completion

Advance only when the current operation produced its stated result within its authority and handed it to its stated consumer. A missing prerequisite, unresolved human meaning, or failed operation follows its local return route.

The clean package remains runtime-only. Do not add records, catalogs, generated schemas, evidence bundles, tests, or project controllers as substitutes for instruction.

## Parallel modes

Both parallel modes require usable subagents. `parallel-normal` may delegate bounded reader, writer or implementer, reviewer, verifier, and integrator work. It does not run Context Optimization. Independent write-disjoint work may run together; same-artifact writing and Work slices serialize.

| Parallel role | Authority ceiling |
|---|---|
| Reader | read-only evidence and bounded task decomposition |
| Writer/implementer | one candidate inside the active family authority and admitted write set |
| Reviewer | findings only against one frozen request |
| Verifier | deterministic validation evidence only |
| Integrator | reconcile compatible reviewed outputs without new meaning, authority, approval, or checklist closure |

Create a fresh subagent for each bounded job, wait for its result, then close it. If creation fails, close the prior handle only when necessary, retry once, then change to a sequential mode if creation still fails.

## Optimized modes

For either optimized mode, load [Context Optimization](successor-v0/modules/context-optimization.md) before every task's first task-specific context-bearing operation. It returns only context fit, partitions, bounded fragments, and read-free synthesis to the active operation. `parallel-optimized` receives independent bounded units for fitting specialized workers; `sequential-optimized` receives its compact fragments and remaining work through the module's continuation document.

## User-visible boundary

Show admission, focused questions, required corrections, approvals, and concise stage or terminal results. Keep runtime mechanics internal.

## Rebon boundary

When Rebon is the admitted host, load [Rebon host adapter](successor-v0/modules/rebon-host-adapter.md); it returns native-tool results to its calling family or module. Other hosts do not use Rebon bindings.
