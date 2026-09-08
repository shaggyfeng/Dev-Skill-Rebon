# Decision

## Trigger and boundary

Use Decision when an accepted rule does not uniquely determine an output and the output creates, removes, recommends, selects, narrows, prioritizes, trades off, authorizes, or records an option. It also frames an indirect engineering thought, concern, preference, constraint, or goal when no accepted rule maps it directly to a responsible operation. A user naming Decision is sufficient; it is not the only trigger.

```text
accepted rule uniquely determines output -> caller uses the mechanical or evidentiary result
indirect engineering intent -> Decision frames Human Intent State -> Route selects the next consumer
candidate needs findings -> Review findings -> Decision -> presenter, recorder, or admitted actor
```

Decision is the final semantic operation. A later semantic change returns to Review, then Decision. Decision does not route, research, write, implement, review, or update Common Sense.

## Inputs

- Candidate options and current facts when an option already exists.
- Indirect intent: the stated thought, concern, preference, constraint, or goal, without invented project facts or candidate options.
- Human Intent State: current observation, desired outcome, Value Gap, protected meaning, unresolved ambiguity, and any provisional hypothesis.
- Relevant Review findings.
- A compact Common Sense reference when one matched; its absence changes nothing.

| Human Intent field | Meaning |
|---|---|
| Current observation | What is true now in the supplied facts |
| Desired outcome | The state the human wants reached |
| Value Gap | The material difference between the current observation and desired outcome |
| Protected meaning | Accepted wording, scope, constraint, or choice that may not be silently changed |
| Unresolved ambiguity | Meaning the supplied facts or accepted rules do not determine |
| Provisional hypothesis | A testable, non-accepted explanation that needs evidence or a human decision |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A candidate option needs findings | [Review](review.md) | Finding shape facts that Decision may consume without inventing evidence |
| A current operation has a matched pattern | [Common Sense](common-sense.md) | Compact matched-pattern reference; absence changes nothing |
| Protected meaning changes or human priority remains unresolved | [Grilling](grilling.md) | Next bounded human question or accepted rule |
| A Decision result needs presentation | [Write](write.md) | Rendered result that does not change the disposition |

## Humour test shape

| Field | Meaning |
|---|---|
| Failure scenario | Smallest real-world case in which the candidate becomes ridiculous or fails the human intent |
| Candidate component | The safeguard, fallback, verification, recovery, or action part that produces that case |
| Condition zone | The conditions under which that component produces the case |
| Value-Gap effect | Whether the condition zone increases the gap between current observation and desired outcome |

## Operation

1. For indirect intent with no directly determined operation, state only the current observation, desired outcome, protected meaning, and unresolved ambiguity. Return Human Intent State, a factual gap, or a human-owned question. Do not invent facts, candidate options, a stage, a module, or authority.
2. Preserve protected meaning. An exact accepted option or bound fix remains exact. Equivalent free text preserves its meaning. Use the Grilling reference when wording changes or stays ambiguous.
3. Think selects the minimum useful representation without changing protected meaning or manufacturing completeness.

   | Need | Representation |
   |---|---|
   | Sequence | State machine or operation flow |
   | Conditional outcome | Decision table |
   | Repeated fields | Schema or mapping |
   | Dependencies or authority | Dependency or authority map |

4. Obtain Review findings only when a frozen candidate needs them. An indirect intent without a candidate does not manufacture a review surface.
5. Immediately before return, Humour tests each candidate after its selected safeguards, verification, fallback, and recovery are known. Use the Humour test shape for each candidate.
6. Invalidate a candidate only within a condition zone that increases the Value Gap. A candidate not invalidated is not thereby valid, approved, or preferred. Humour neither scores candidates nor vetoes generic risk.
7. Compare surviving candidates by their ability to close the stated Value Gap. Recommend only a clearly dominant candidate. Use the Grilling reference when a human-valued priority remains non-comparable.

## Returns

| Result | Consumer |
|---|---|
| Mechanical or evidentiary determination | Calling operation |
| Indirect intent with a factual gap | [Route](../stage-0-route.md) for Project Discovery or Research |
| Indirect intent with unresolved human meaning | [Grilling](grilling.md) |
| Indirect intent with sufficient facts | [Route](../stage-0-route.md) with Human Intent State |
| Resolved meaning or accepted semantic disposition | Calling operation |
| Invalidated option with failure scenario, component, and condition zone | [Grilling](grilling.md) |
| Unresolved human meaning or priority | [Grilling](grilling.md) |
| Clear recommendation | [Write](write.md) for presentation or admitted actor for execution |

## Limits

- The Review reference produces findings; Decision selects no facts that evidence cannot support.
- The Write reference may render a Decision result but may not change it.
- An admitted actor consumes the Decision result; it does not substitute a new semantic choice.
- Decision does not select a Route stage, module, terminal, or authority. Route consumes its Human Intent State and selects the next consumer.
