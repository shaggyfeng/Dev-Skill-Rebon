# Manual Procedure

## Trigger and boundary

Run when a requested action is genuinely human-only or unavailable to the agent. A user naming this module is sufficient. It gives temporary human-action guidance; it does not publish decisions, authorize execution, or write governed artifacts.

## Procedure structure

| Stage field | Content |
|---|---|
| Action | One human action, command, or navigation step |
| Expected result | Observable result that proves the stage completed |
| Destination | Where any entered, copied, or produced value goes |
| Secrecy | Whether the value is safe to show, must remain local, or must not enter a record |
| Confirmation | Required human confirmation before an irreversible or external-account action |
| Return | The exact evidence or result supplied back to the caller |

The procedure is temporary. It has no durable location unless the caller names an authorized writer and target.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A procedure needs human-facing presentation | [Write](write.md) | Rendered ordered procedure without changed actions |
| A real transfer boundary receives the human result | [Handoff](handoff-and-boundaries.md) | Transferred result to its named consumer |

## Operation

1. Confirm the action cannot be safely performed by the admitted host or a local tool.
2. Define each stage with the Procedure structure.
3. Present the complete ordered stages before detailed instructions. Obtain explicit human confirmation before an irreversible action or external account change.
4. Verify unknown third-party paths, commands, and destinations from primary documentation. Do not guess them.
5. Keep secrets out of instructions, logs, reports, handoffs, and durable records.
6. Use the Write reference to present the procedure. Return the human's result through the named caller or the Handoff reference.

## Returns

| Result | Consumer |
|---|---|
| Temporary procedure | Human and calling family |
| Missing confirmation or verified third-party detail | Calling family |
| Safe automated alternative exists | Admitted host or tool |
