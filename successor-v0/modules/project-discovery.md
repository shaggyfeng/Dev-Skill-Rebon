# Project Discovery

## Trigger and boundary

Run when no active stage or valid direct route provides sufficient project orientation. A user naming Project Discovery is sufficient. It returns facts only; [Route](../stage-0-route.md) selects the stage and [Decision](decision.md) handles semantic choices.

## Orientation result

| Field | Meaning |
|---|---|
| Classification | `greenfield`: no relevant existing solution artifact; `brownfield`: relevant artifacts control the work; `uncertain`: one orientation fact is missing |
| Active work | Current stage, handoff, roadmap, or task facts if present |
| Artifacts and conventions | Relevant files, interfaces, tests, commands, and local rules; for `brownfield`, `artifact -> condition -> maintain boundary` per touched artifact |
| Source order | Release, Work, Plan, then Design material actually read |
| Remaining gap | One focused clarification when classification is `uncertain` |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Project orientation is needed | [Route](../stage-0-route.md) | Route consumes factual orientation and selects the stage |
| A handoff pointer is the current task source | [Handoff layout](handoff-and-boundaries.md) | Goal and current task facts for orientation |
| `uncertain` leaves a controlling fact missing | [Research](research.md) | Evidence or factual frontier for Route |
| Orientation reaches a semantic choice | [Decision](decision.md) | Semantic disposition outside Project Discovery; this module returns facts only |

## Operation

1. Read the current task or handoff pointer first.
2. Read only relevant material in this order: Release, Work, Plan, then Design.
3. If one orientation fact remains missing, flash start with native listing, targeted search, metadata, and counts for that fact only.
4. Identify existing artifacts, active work, conventions, and whether the evidence is greenfield, brownfield, or uncertain.
5. Return the factual orientation through the Route reference. Do not select a solution, stage, module, or design option.

## Returns

| Result | Consumer |
|---|---|
| `greenfield` | [Route](../stage-0-route.md) stage selection |
| `brownfield` | [Route](../stage-0-route.md) stage selection with artifact and convention facts |
| `uncertain` | [Route](../stage-0-route.md) clarification or [Research](research.md) |
