# Context Optimization

## Trigger and boundary

Run only in an admitted `parallel-optimized` or `sequential-optimized` mode, before each task's first context-bearing operation. A changed task, scope, consumer, or invalid result runs it again. A user naming the module is sufficient only in an optimized mode.

Context Optimization owns fit assessment, partitioning, bounded fragments, read-free synthesis, and sequential-optimized continuation. It does not route, plan, review, decide, implement, verify, or close work.

## Unit and continuation structure

| Item | Required content |
|---|---|
| Bounded unit | Target seam, purpose, input limit, expected fragment, and next consumer |
| Fragment | Relevant facts, omissions, unresolved point, and source location needed by the next consumer |
| Remaining frontier | Unread or unresolved units in their next execution order |
| Read-free synthesis | The complete fragment set, its consumer, and no full-source reread |
| Sequential continuation | Goal, completed fragments, remaining frontier, current authority pointers, and exact next action |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A normal-mode operation cannot fit its context | [Mode Gate](../../mode-gate.md) | Re-admitted profile with an optimized mode suggested |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| A task-specific context-bearing target is selected | `target_sized` | Fit decision or authoritative partition before content reading, reasoning, writing, review, verification, or continuation |
| Bounded units are read or produced | `fragments_ready` | Read-free synthesis to the declared consumer or sequential continuation |

## Operation

1. At `target_sized`, before content work, use native listing, targeted search, metadata, byte counts, and line counts to size the target.
2. If fit is uncertain or the target is oversized, partition it before reading at a file, section, symbol, source/test, roadmap outcome, or other authoritative seam.
3. Read one bounded unit at a time. Preserve only the compact fragment needed by the next consumer, including omissions and remaining frontier.
4. At `fragments_ready`, synthesize from bounded fragments without rereading the full source.
5. Apply the same sequence to reading, reasoning, writing, review, verification, and continuation when their target would exceed context.

## Mode behavior

| Mode | Context behavior |
|---|---|
| `parallel-optimized` | Give independent bounded units to fitting available workers; synthesis consumes their fragments. |
| `sequential-optimized` | Store the Sequential continuation structure in the project's `.dev-skill/context/` continuation document, then resume from it. |

The continuation document is only for sequential-optimized context transfer. It is not a general handoff or workflow record.

## Returns and recovery

- Never retry unchanged oversized scope.
- Preserve useful fragments and split only the unread or unresolved frontier.
- A valid bounded result returns to the calling operation.
- A normal-mode context failure uses the Mode Gate reference.
