# phase-boundaries reference

A **phase** is a bounded chunk of work inside a session: discovery, grilling, specification, implementation, testing, or review. Decide what happens to context at the boundary between phases, never mid-phase.

Decision tree, in order:

1. **Continue** — the next phase needs this conversation as a primary source, or remaining context is safely inside the working zone.
2. **Clear** — everything in context is disposable and the next phase can start from the repository alone.
3. **Handoff** — context must travel to a new harness, directory, or future session; write a redacted handoff pointing to authoritative artifacts.
4. **Compact** — context stays relevant, harness and directory unchanged, and a bounded summary suffices.

| Source | Information | Cost |
|---|---|---|
| Continue | Full conversation and decisions | Largest context load |
| Handoff | Portable, redacted summary plus artifact pointers | Lossy but durable |
| Compact | Same-session summary | Lossy, less portable |
| Clear | No conversation | Lowest load, highest loss |

At a phase boundary, an isolated-mode UltraWork run terminates completed workers, checkpoints the board and integration state, and spawns fresh workers only for newly ready cards; other modes checkpoint per the mode table. Never compact mid-decision-round or mid-implementation-unit when omitted reasoning is still a primary source.

The transition is complete only when the next session can identify: the accepted goal, current packet, dependencies, latest revision, open human gate, exact verification command, and authoritative artifact paths.
