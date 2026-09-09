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
| A completed Grill needs its final decision record | [Domain Modeling](domain-modeling.md) | Exact final decisions at the caller-authorized project location |
| A compatible host enters or advances a declared Grilling boundary | [Host Enforcement](host-enforcement.md) | Checkpoint-scoped entry or advance; `instruction-guided` when no interception exists |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| Ready frontier, options, and reviewer findings are available | `packet_reviewed` | Present the reviewed packet and await an exact human answer |
| The human gives an exact answer | `answer_received` | Retained accepted rule, narrower question, or final decision record |
| A completed Grill's exact final decisions and a caller-authorized project location are available | `record_ready` | Domain Modeling writes only that final decision record |

## Operation

1. Search accepted decisions for the same module and operation. Open a question only for a demonstrated conflict, missing retained behavior, or unresolved human meaning.
2. Form the ready frontier. Present independent, pointy questions together; otherwise ask one bounded question at a time.
3. Draft exactly three substantive mutually exclusive options plus a fourth option exactly: `Not sure, help me narrowing it down.`
4. Use the Review reference for Standards and Specification findings for each option. Mark them as reviewer findings.
5. At `packet_reviewed`, use the Decision reference with options, facts, Human Intent State, and findings. Present its surviving options, invalidations, and recommendation without changing their meaning.
6. At `answer_received`, preserve the exact human answer in the current Grill. Do not return it to a consuming operation before `record_ready`. Do not reopen an exact answer through Review or Decision.
7. A `Not sure, help me narrowing it down.` answer starts one narrower question with the same fourth option. It does not close the current question.
8. Return a missing controlling fact or factual fog to Research. Return an experience or value question to Prototype or the human. Multi-party participation receives a shareable packet and returns its exact answer here.
9. When no current or dependent narrower question remains, enter `record_ready` before a consuming operation begins. Use Domain Modeling to write the Final decision record.

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

## Final decision record

| Part | Content |
|---|---|
| Scoped question set | The questions this Grill resolved |
| Intent baseline | Current observation, desired outcome, and Value Gap for the resolved questions |
| Accepted rules | Exact final human answers |
| Supersession | Only a conflicting prior rule and its final replacement |
| Next consumer | The declared operation that consumes each rule |

## Record reminder

After five accepted but unrecorded rounds, ask once whether to create or update one working grilling record. If yes, update that same record after each later group of five unrecorded rounds. If no, do not ask again in that grilling session. At session end, `record_ready` writes the final decision record. Remove a working record only when it duplicates that final record. Domain Modeling performs any durable write without changing the reviewed question, options, findings, or answer.

## Returns

| Condition | Consumer |
|---|---|
| Accepted rule while the Grill remains open | This Grilling session |
| Final decision record | Declared consuming operation |
| Invalidated option or unresolved priority | Next Grilling question |
| Missing controlling fact | Research |
| Human experience needed | Prototype or human |
| Multi-party answer | This Grilling session |
