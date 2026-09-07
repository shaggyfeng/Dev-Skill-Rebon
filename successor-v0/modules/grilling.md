# Grilling

## Trigger and boundary

Run Grilling for an unresolved human-owned design, meaning, priority, trade-off, or authority question. A user naming Grilling is sufficient; an equivalent accepted decision is a constraint, not a new question.

Grilling owns the decision frontier, reviewed question presentation, exact human answer, and accepted-decision return. Review owns findings and Decision owns semantic disposition.

## Frontier terms

| Term | Meaning |
|---|---|
| Ready frontier | Unresolved questions whose controlling facts and accepted constraints are available |
| Independent questions | Questions whose answers do not change each other's options or authority |
| Pointy question | A question the human resolves by selecting an option or its reviewer fix without redefining it |
| Narrower question | The next question that resolves only the ambiguity left by the prior answer |

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Each option needs findings before the human answers | [Review](review.md) | Standards and Specification findings for each option |
| Options, facts, and human intent are ready for semantic disposition | [Decision](decision.md) | Surviving options, invalidations, and recommendation for presentation |
| Grilling needs the Human Intent State term | [Decision](decision.md) | Current observation, desired outcome, Value Gap, protected meaning, ambiguity, and hypothesis for the question |
| A controlling fact is missing | [Research](research.md) | Evidence or factual frontier for the next question |
| Human experience or value must be observed | [Prototype](prototype.md) | Bounded evidence or a human return |
| A packet needs rendering | [Write](write.md) | Rendered packet without changed questions or options |
| An accepted record needs an exact durable write | [Domain Modeling](domain-modeling.md) | Exact reviewed content at the caller-authorized location |

## Operation

1. Search accepted decisions for the same module and operation. Open a question only for a demonstrated conflict, missing retained behavior, or unresolved human meaning.
2. Form the ready frontier. Present independent, pointy questions together; otherwise ask one bounded question at a time.
3. Draft exactly three substantive mutually exclusive options plus a fourth option exactly: `Not sure, help me narrowing it down.`
4. Use the Review reference for Standards and Specification findings for each option. Mark them as reviewer findings.
5. Use the Decision reference with options, facts, Human Intent State, and findings. Present its surviving options, invalidations, and recommendation without changing their meaning.
6. Preserve the exact human answer. Use Review, then Decision, before recording the accepted rule or returning it to the caller.
7. A `Not sure, help me narrowing it down.` answer starts one narrower question with the same fourth option. It does not close the current question.
8. Return a missing controlling fact or factual fog to Research. Return an experience or value question to Prototype or the human. Multi-party participation receives a shareable packet and returns its exact answer here.

## Presentation

Every question uses these sections in order:

| Section | Content |
|---|---|
| What it decides | The exact rule or boundary the answer will settle |
| Background | Only accepted facts and source pointers needed to choose |
| Question | One bounded unresolved choice and its four options |
| Reviewer findings | Per-option findings from Standards and Specification review |
| After choice | Decision invalidations, surviving options, recommendation, and return effect |
| Exact answer and accepted rule | Unchanged human answer and the rule it accepts |

The Reviewer findings options table has exactly: Option, What's wrong, Why it matters, Failure scenario, Bottom-line fix. Use `Failure scenario`, never `Example scenario`. Link the relevant current source material when it supports an option. Use the Write reference when packet rendering is needed.

## Record reminder

After five accepted but unrecorded rounds, ask once whether to create or update one working grilling record. If yes, update that same record after each later group of five unrecorded rounds. If no, do not ask again in that grilling session. At session end, remove the working record when no durable documentation needs it. Domain Modeling performs any durable write without changing the reviewed question, options, findings, or answer.

## Returns

| Condition | Consumer |
|---|---|
| Accepted rule | Calling family or module |
| Invalidated option or unresolved priority | Next Grilling question |
| Missing controlling fact | Research |
| Human experience needed | Prototype or human |
| Multi-party answer | This Grilling session |
