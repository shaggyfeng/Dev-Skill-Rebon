# Grilling

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | eligible human-owned Design decision exists in the current frontier |
| Input | `design.frontier.v0.1`: `task_id`, `scope_digest`, stable tickets, owners, prerequisites, evidence, options, dependents, exclusions, reopen conditions, producer, consumer |
| Output | `design.grill-round.v0.1`: eligible ticket IDs, exact numbered questions and options, `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, `review_phase`, question digest, pre-answer findings, exact answers, post-answer findings, classified gap, mapped route, route evidence, deferred/blocked tickets, frozen digest, status, consumer |
| Authority | facilitation only |
| Failure | `clarification_required` or `frontier_blocked` |
| Consumer | `deep-decision-review`, frontier calculator, or `design.packet.compile` |

The grill-round artifact is phase-scoped: the pre-answer packet binds `answer=null` with pre-answer findings and carries no answers or post-answer findings; the answer packet adds the exact answers and post-answer findings; the final round result adds the classified gap, the mapped route with evidence, and the deferred or blocked ticket dispositions. Each reviewer invocation receives the complete artifact for its current phase only.

## Operation

1. Select tickets whose prerequisites are resolved or explicitly excluded.
2. Inspect safely available facts; ask the human only for normative choices, priorities, acceptance boundaries, or authority decisions.
3. Construct one decision per question and the complete independent frontier in one numbered round.
4. Freeze the exact questions, mutually exclusive options, evidence, and `Not sure, help me narrowing it down` option with `answer=null`; bind `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, and `review_phase=pre_answer`, then send the frozen question packet to `deep-decision-review` before presenting it to the human.
5. Present the review findings and candidate fixes/options. The griller does not answer, recommend, steer, or resolve the decision.
6. In guided narrowing, ask one question at a time with options and retain `Not sure, help me narrowing it down` in every round until one option is admitted. A `Not sure` answer enters guided narrowing after post-answer review; it cannot classify, close, or resolve the current gap.
7. Defer a question that depends on another unresolved question in the same round.
8. Preserve the exact question packet, reviewer findings/options, evidence, and human answer.
9. After the human answers, freeze the exact question, options, answer, and prior review digest; retain `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, and set `review_phase=post_answer`; send the answer packet to `deep-decision-review` before gap classification or human disposition.
10. After post-answer deep-decision findings return, classify each frontier question and apply exactly one mapped route:

| Reviewer finding | Gap class | Mapped route | Required evidence | Consumer |
|---|---|---|---|---|
| all questions close and no gap remains | `whole_gap_closed` | terminal | shared mind-cleared result | shared terminal |
| all current gaps close and none reforms in another shape | `pointy` | plain | closed current frontier and no re-formed gap | next frontier |
| the same question returns narrower | `straight` | with-docs | narrower question and append-only candidate record binding | `domain-modeling` or frontier calculator |
| no gap closes and more dimensions appear | `expanding` | path-finder | destination, bounded dimensions, decision tickets, and dependency edges | `to-spec` or path-finder |
| an answer closes a gap but another actor owns the remaining decision | `multi-party` | multi-party | recipient, expertise, relationship, and required return facts | questionnaire or owning human |

11. Validate the mapped route, required evidence, and consumer before human disposition. A route not listed for the classified gap or missing its evidence or consumer returns `clarification_required`. Preserve unresolved ownership, dependencies, and successor tickets from the classification. Recalculate the frontier only after human disposition.

### Packet anatomy

Every grill packet renders with exactly six elements, in this order:

1. Title — an md heading naming the decision.
2. What it decides — subtitle plus one paragraph.
3. Background — subtitle plus one paragraph.
4. Question — subtitle plus the question and its numbered options as a short indented outline; the fixed fourth option `Not sure, help me narrowing it down` restates verbatim.
5. Findings — subtitle carrying the impact-first finding immediately beneath it, then the options table with exactly the columns Option, What's wrong, Why it matters, Example scenario, Bottom-line fix.
6. After choice — subtitle plus a short indented outline of the still-open items.

The presented packet is the reviewed packet; any post-review change is itself reviewed before presentation. For block-level rendering — headings, lead-ins, outline indentation lists, and tables — `stage-0-write.md` carries the structure-selection rules; open it before writing a packet when the block forms are not already in use.

## State machine

```text
frontier_open -> question_frozen
question_frozen -> pre_answer_review
pre_answer_review -> human_answer | clarification_required
human_answer -> post_answer_review
post_answer_review [answer=Not sure, help me narrowing it down] -> guided_narrowing
post_answer_review [answer!=Not sure, help me narrowing it down] -> gap_classification | clarification_required
gap_classification -> route_validation | clarification_required
route_validation -> human_disposition | clarification_required
human_disposition -> frontier_recalculated
frontier_recalculated -> question_frozen | terminal
guided_narrowing -> question_frozen
```

`pre_answer_review` requires the exact question/options digest and `answer=null`. `post_answer_review` requires the same question/options digest, the exact human answer, and the prior review digest. No classification or disposition runs from an unreviewed state.

## Invariants

- Facts and normative decisions remain distinct; the griller never answers for the human.
- Reviewer and facilitator remain separate; reviewer findings and candidate fixes/options precede the human answer, and answer review precedes gap classification.
- No branch closes by assumption and no ticket resolves from an unreviewed answer.
- The frozen question packet is reviewed before presentation; the answer packet is reviewed before human disposition.
- The presented packet is the reviewed packet; a post-review change is itself reviewed before presentation.
- Each classified gap has exactly one mapped route; route-specific evidence and the downstream consumer must validate before transition.
- The griller/controller selects only the mapped route; the deep reviewer cannot select or emit it.
- Under the `lead_ungoverned` governance profile, the human-disposition step resolves automatically: the controller applies the post-answer findings' `bottom_line_fix` for the human's admitted option — the human's standing authorization — and the loop proceeds to frontier recalculation. Under `pair` or `governance_unresolved`, the disposition is the human's. The reviewer never selects or applies a fix in any profile.
- A `Not sure, help me narrowing it down` answer never enters gap classification or closure; it preserves the reviewed packet and starts one guided-narrowing question.
- A retry changes the frontier, question scope, or bound evidence.

## Recovery

- Missing question/options or pre-answer review returns `clarification_required`; missing owner or meaning returns `clarification_required`; hidden dependencies block and split the ticket.
- Missing answer review blocks gap classification and human disposition.
- Missing or incompatible mapped route, route evidence, or consumer returns `clarification_required`.
- New dimensions create successor tickets; ownership conflict preserves each claim and routes to the owning human.
- Retry requires a changed frontier, answer, evidence set, or ticket scope.

## Completion

Completion is phase-scoped: the pre-answer packet completes with the frozen questions, options, evidence, `answer=null`, and pre-answer findings; the answer packet completes with the exact answers and post-answer findings; the final round result completes with the classified gap, mapped route with evidence and consumer, deferred or blocked/excluded tickets with routes, the current digest, and status. Deep-decision findings and human disposition are downstream states.
