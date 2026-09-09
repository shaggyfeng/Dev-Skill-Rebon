# Handoff and boundaries

## Trigger and boundary

Use Handoff only for a real session, harness, directory, colleague, or side-task boundary. Context compaction and sequential-optimized continuation belong to Context Optimization, not this module.

Handoff transfers current work; it does not grant authority, approval, review completion, or checklist closure.

## Handoff layout

| Section | Content |
|---|---|
| Goal and current task | The bounded outcome and work currently in progress |
| Accepted decisions and pointers | Links or paths to authoritative material; do not duplicate it |
| Dependencies and changes | What is complete, changed, or still needed |
| Checks and failures | Commands or observations already run and unresolved failure facts |
| Open gates and assumptions | Human decisions still needed and clearly labeled provisional assumptions |
| Next action | One exact action, its consumer, and next command when one exists |

The caller chooses a real temporary or approved handoff directory as the transfer target; a Handoff is never an authority source. Context Optimization owns compact continuation material and its separate `.dev-skill/context/` location.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Work needs compact sequential-optimized transfer instead of a real boundary handoff | [Context Optimization](context-optimization.md) | Sequential continuation structure in `.dev-skill/context/` |
| A transferred pointer changes scope or consumer | [Route](../stage-0-route.md) | Re-routed stage and next consumer |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| A real session, harness, directory, colleague, or side-task boundary exists | `handoff_ready` | Transfer the current Handoff layout to that exact boundary |
| Receiver has the transferred authoritative pointers | `handoff_returned` | Resume the stated consumer or Route re-entry on changed scope or consumer |

## Operation

1. Confirm that a real boundary exists. Otherwise return to the current family without creating a handoff.
2. At `handoff_ready`, transfer the Handoff layout.
3. Redact secrets and personal data. Point to authoritative artifacts instead of copying their contents.
4. The receiver reads the transferred pointers and resumes from the stated consumer at `handoff_returned`. A changed scope or consumer uses the Route reference.

## Returns

| Condition | Return |
|---|---|
| Boundary is real and transfer is sufficient | Named next session, harness, directory, colleague, side task, or family |
| Current work needs compact context transfer | [Context Optimization](context-optimization.md) |
| No real boundary | Current family |
| Required pointer is unavailable | Originating family for correction |
