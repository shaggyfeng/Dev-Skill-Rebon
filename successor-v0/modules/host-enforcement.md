# Host Enforcement

## Trigger and boundary

Use only when a caller needs to classify host interception, enter an owning module's first declared checkpoint, or advance one declared checkpoint. Host Enforcement defines portable meanings only. It does not implement a controller, route, decide, review, authorize, select a retry, or own Rebon-native tools.

## Terms

| Term | Meaning |
|---|---|
| `admitted` | Provider, host, mode, profile, and route capability passed; no operation has started |
| `current operation` | Route has selected and loaded an owner and entered its first checkpoint |
| `checkpoint-capable` | The host can map a future exact checkpoint; no current checkpoint is yet `enforced` |
| `enforced` | The host can intercept the exact current checkpoint |
| `instruction-guided` | The host cannot intercept that checkpoint; the instruction still applies |

## Interactions

| When | Interaction | Return or use |
|---|---|---|
| Mode Gate classifies host capability | `capability` | `checkpoint-capable` or unavailable interception; no current `enforced` claim |
| Route selects one current owner | `enter` | The current operation reaches that owner's first declared checkpoint; a host with exact interception creates and holds the current binding as `enforced` |
| An owning operation reaches a local boundary | `advance` | A host with exact interception matches the current result or requested action and target to its held binding, then replaces it with the declared next owner boundary before allowing that checkpoint, return, or exact state-changing action |

The current binding contains only admitted profile, current transition, active module operation, allowed state-changing action, exact target at action time, and declared return consumer. A host with exact interception creates and holds it at `enter`, then matches and replaces it at `advance` with the declared next owner boundary. A terminal or return with no next owner has no held binding. Reads and discovery are not gated. `instruction-guided` follows the same declared transition without a host-held enforcement claim.

## Operation

1. At admission, classify only whether the host can map a future exact checkpoint. Do not claim `enforced` before Route selects one.
2. At entry or advance, classify the exact checkpoint the caller names. A hook elsewhere never makes the run globally `enforced`.
3. At `enter`, a host with exact interception creates and holds the current binding only for the first checkpoint declared by Route's selected owner.
4. At `advance`, a host with exact interception matches the current result or requested action and target to that binding, then replaces it with the declared next owner boundary before allowing its checkpoint, return, or state-changing action.
5. An `instruction-guided` caller follows the same declared transition without claiming host enforcement.
6. Reject an out-of-sequence governed action or progression without changing state and return it to the active owning operation.

## Returns

| Result | Consumer |
|---|---|
| Interception capability | Mode Gate |
| Entered or advanced checkpoint | Calling Route or owning operation |
| Rejected checkpoint | Active owning operation recovery or declared return |

Host Enforcement verifies sequence, not Human Intent, Value Gap, semantic quality, route, review, authority, or completion. A host without interception remains `instruction-guided`; it never claims enforcement.
