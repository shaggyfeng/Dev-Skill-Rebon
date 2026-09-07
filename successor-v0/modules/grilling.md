# Grilling

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | eligible human-owned Design decision exists in the current frontier |
| Input | `design.frontier.v0.1`: `task_id`, `scope_digest`, stable tickets, owners, prerequisites, evidence, options, dependents, exclusions, reopen conditions, `review_surface`, declared `artifact_kinds`, family context, authority source IDs, frozen subject, producer, consumer; one `grilling_session_id` is established at the first round and retained through scope refinement until that grill reaches terminal, with its immutable working-record target or explicit absence, ordered pending accepted rounds, matching unrecorded-round count, and reminder state `armed`, `awaiting_answer`, or `suppressed` |
| Output | `design.grill-round.v0.1`: eligible ticket IDs, exact numbered questions and options, `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, `review_phase`, `review_surface`, declared `artifact_kinds`, family context, authority source IDs, frozen subject, question digest, pre-answer findings, exact answers, post-answer findings, classified gap, canonical executable route, route evidence, deferred/blocked tickets, `path_finder_map` or `multi_party_packet` when selected, typed `publication_branch` with `human_disposition_digest` when durable publication is selected, optional `continuity_record_branch` with `operation=update|close`, `grilling_session_id`, immutable working-record target or explicit absence, ordered pending accepted rounds, matching count and reminder state, frozen digest, status, consumer |
| Authority | facilitation only |
| Failure | `clarification_required` or `frontier_blocked` |
| Consumer | `deep-decision-review`, frontier calculator, or `design.packet.compile` |

The grill-round artifact is phase-scoped: the pre-answer packet binds `answer=null` with pre-answer findings and carries no answers or post-answer findings; the answer packet adds the exact answers and post-answer findings; the final round result adds the classified gap, the canonical executable route with evidence, and the deferred or blocked ticket dispositions. Every packet retains the declared review surface, artifact kinds, family context, authority source IDs, and frozen subject, and each reviewer invocation receives the complete artifact for its current phase only.

## Operation

1. Select tickets whose prerequisites are resolved or explicitly excluded.
2. Inspect safely available facts; ask the human only for normative choices, priorities, acceptance boundaries, or authority decisions.
3. Construct one decision per question and the complete independent frontier in one numbered round.
4. Freeze the exact questions, mutually exclusive options, evidence, and `Not sure, help me narrowing it down` option with `answer=null`; bind `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, `review_phase=pre_answer`, `review_surface`, declared `artifact_kinds`, family context, authority source IDs, and the frozen subject, then send the frozen question packet to `deep-decision-review` before presenting it to the human.
5. Present the review findings and candidate fixes/options. The griller does not answer, recommend, steer, or resolve the decision.
6. In guided narrowing, ask one question at a time with options and retain `Not sure, help me narrowing it down` in every round until one option is admitted. A `Not sure` answer enters guided narrowing after post-answer review; it cannot classify, close, or resolve the current gap.
7. Defer a question that depends on another unresolved question in the same round.
8. Preserve the exact question packet, reviewer findings/options, evidence, and human answer.
9. After the human answers, freeze the exact question, options, answer, and prior review digest; retain `review_context=grill_infrastructure`, `review_kind=grill`, `review_state=one_shot`, `review_surface`, declared `artifact_kinds`, family context, authority source IDs, and the frozen subject, set `review_phase=post_answer`, and send the answer packet to `deep-decision-review` before gap classification or human disposition.
10. After post-answer deep-decision findings return, classify each frontier question and apply exactly one mapped route:

| Reviewer finding | Gap class | Mapped route | Required evidence | Consumer |
|---|---|---|---|---|
| all questions close and no gap remains | `whole_gap_closed` | terminal | shared mind-cleared result | shared terminal |
| all current gaps close and none reforms in another shape | `pointy` | `stateless_return` (plain) | closed current frontier and no re-formed gap | next frontier |
| the same question returns narrower | `straight` | `durable_publication` (with-docs) | narrower question and append-only candidate record binding | `domain-modeling` or frontier calculator |
| no gap closes and more dimensions appear | `expanding` | `path_finder` (path-finder) | destination, bounded dimensions, decision tickets, and dependency edges | `to-spec` or path-finder |
| an answer closes a gap but another actor owns the remaining decision | `multi-party` | `multi_party` | recipient, expertise, relationship, and required return facts | questionnaire or owning human |

11. Normalize any source route label to its executable mapped route, then validate that route, its required evidence, and its consumer before human disposition. A route not listed for the classified gap or missing its evidence or consumer returns `clarification_required`. Preserve unresolved ownership, dependencies, and successor tickets from the classification. Recalculate the frontier only after human disposition.
12. After each accepted round, run the continuity-record reminder operation without changing the classified gap, mapped route, or human disposition.

The source labels `plain`, `with-docs`, `path-finder`, and `multi-party` are classification labels only; the mapped route column names the executable state. `plain` normalizes to `stateless_return`, `with-docs` to `durable_publication`, `path-finder` to `path_finder`, and `multi-party` to `multi_party` before route validation.

### Packet anatomy

Every grill packet renders with exactly six elements, in this order:

1. Title — an md heading naming the decision.
2. What it decides — subtitle plus one paragraph.
3. Background — subtitle plus one paragraph.
4. Question — subtitle plus the question and its numbered options as a short indented outline; the fixed fourth option `Not sure, help me narrowing it down` restates verbatim.
5. Findings — subtitle carrying the impact-first finding immediately beneath it, then the options table with exactly the columns Option, What's wrong, Why it matters, Failure scenario, Bottom-line fix.
6. After choice — subtitle plus a short indented outline of the still-open items.

The presented packet is the reviewed packet; any post-review change is itself reviewed before presentation. For block-level rendering — headings, lead-ins, outline indentation lists, and tables — `stage-0-write.md` carries the structure-selection rules; open it before writing a packet when the block forms are not already in use.

### Source-specific operations

`with-docs`, Path-finder, and multi-party are executable operations, not route labels.

#### With-docs publication

When `with-docs` is selected, bind a typed `publication_branch` containing `reviewed_packet_digest`, the exact reviewed questions, options, findings, answer, persistence request, governance profile, authority source IDs, `human_disposition_digest`, and the `domain-modeling` consumer before durable writing. If no repository or no persistence request is present, return the reviewed packet statelessly and create no durable record. If persistence is requested, pass the exact branch to Domain Modeling as `design.domain-input.v0.1`; Domain Modeling is the sole durable writer and receives the exact reviewed questions, options, findings, and answer without changing them. Under `pair`, publication binds the exact human answer. Under the current explicitly selected `lead_ungoverned`, publication binds the human's admitted option and the reviewer's recommended `bottom_line_fix` applied under that profile. Under `governance_unresolved`, do not publish. Qualified terms become glossary candidates and qualified hard-to-reverse decisions become ADR candidates; their normal human disposition gates still apply.

#### Continuity-record reminder

An accepted round is unrecorded when its exact question, reviewed options and findings, exact answer, resulting decision, and unresolved successor are not represented in the active grilling session's working decision record. Append every such round to the ordered pending set and make the count equal its length. Clear that set and reset the count only after Domain Modeling returns a successful write and read-back for the same `grilling_session_id` and record target.

When the count first reaches five while reminder state is `armed`, set it to `awaiting_answer`, bind the prompt to the current session, and ask once:

> Five grilling rounds have completed without updating a decision record. Should I create or update one working grilling record now, keep using that same record for later reminders, and remove it when grilling ends if no documentation is needed?

| Answer | Required transition |
|---|---|
| Yes | Bind one `continuity_record_branch` with `operation=update`, the `grilling_session_id`, ordered pending rounds, and existing record target or explicit absence; send it to Domain Modeling. Accept its first returned target as immutable for that session; every later result must match it. Do not admit another grill round until write and read-back succeed. A failure preserves the complete update state and barrier through bounded changed-state retry; exhausted recovery remains blocked. After success, clear the pending set, reset the count to zero, and set reminders to `armed`. Ask again only after five later accepted rounds remain unrecorded. |
| No | Set reminders to `suppressed` until this grilling session reaches its terminal. Do not ask again in that session. |

Every later Yes updates the same record; a session or target mismatch returns `clarification_required` without clearing pending rounds. The reminder is orthogonal to `plain`, `with-docs`, Path-finder, and multi-party routing and never changes a selected option, Decision result, classified gap, or mapped route. At the grilling-session terminal, when a working-record target exists, send the same `continuity_record_branch` with `operation=close`, the bound session and target, and one closure disposition: `retain` when the documentation consumer is that target, `promote` when another documentation consumer is bound, or `remove` when no documentation consumer exists. A successful update performs the reset and rearm above; terminal ends the remaining session-local reminder state, and a new `grilling_session_id` starts armed with zero pending rounds.

#### Path-finder

Path-finder returns one backend-neutral typed map with `destination`, decision tickets, dependency edges, fog, exclusions, resolution state, frontier, and the `to-spec` consumer. It does not require GitHub, GitLab, local trackers, labels, commands, or repository bookkeeping. A destination without its bounded dimensions and dependency edges is incomplete.

#### Multi-party sharing

Multi-party returns a standalone, sharing-first packet for a participant who has not seen the grill. It carries sufficient context, the decision, reviewed options, direct answer fields, required return information, and a Grilling return binding. The visible packet uses plain language and contains no internal runtime jargon or unrelated safety ceremony. The recipient's answer re-enters Grilling as the exact answer packet and receives post-answer review before classification.

## State machine

The round transition matrix is canonical for Grilling:

| Current state | Guard or input | Next state |
|---|---|---|
| `frontier_open` | eligible question frontier | `question_frozen` |
| `question_frozen` | frozen question/options packet | `pre_answer_review` |
| `pre_answer_review` | findings current | `human_answer` |
| `pre_answer_review` | missing review or packet binding | `clarification_required` |
| `human_answer` | exact human answer packet | `post_answer_review` |
| `post_answer_review` | `answer=Not sure, help me narrowing it down` | `guided_narrowing` |
| `post_answer_review` | another exact answer and current review | `gap_classification` |
| `post_answer_review` | missing or stale answer review | `clarification_required` |
| `gap_classification` | classified gap | `route_validation` |
| `gap_classification` | missing classification | `clarification_required` |
| `route_validation` | validated mapped route | `terminal`, `human_disposition`, `stateless_return`, `path_finder`, or `multi_party` |
| `route_validation` | route evidence or consumer missing | `clarification_required` |
| `human_disposition` | `with-docs`, persistence requested, governance allowed | `durable_publication` |
| `human_disposition` | `with-docs`, no persistence requested | `stateless_return` |
| `human_disposition` | `with-docs`, governance unresolved | `clarification_required` |
| `human_disposition` | publication branch is not `with-docs` | `frontier_recalculated` |
| `stateless_return` | reviewed packet with no repository or persistence request | `frontier_recalculated` or `terminal` |
| `durable_publication` | exact Domain Modeling handoff | `domain_modeling_handoff` |
| `domain_modeling_handoff` | current result | `frontier_recalculated` |
| `domain_modeling_handoff` | missing or stale result | `clarification_required` |
| `path_finder` | complete backend-neutral map | `to_spec_return` |
| `to_spec_return` | current return binding | `terminal` |
| `multi_party` | recipient answer | `human_answer` |
| `frontier_recalculated` | open frontier remains | `question_frozen` |
| `frontier_recalculated` | no frontier remains | `terminal` |
| `guided_narrowing` | next bounded question | `question_frozen` |

The continuity-record reminder is an orthogonal session transition:

```text
accepted_round_unrecorded -> count_incremented
count_incremented [count < 5 or reminder=suppressed] -> current_grill_route
count_incremented [count = 5 and reminder=armed] -> record_prompt
record_prompt -> reminder_awaiting_answer
reminder_awaiting_answer [yes] -> continuity_record_pending
continuity_record_pending -> continuity_record_write
continuity_record_write [matching grilling session and target; write and read-back complete] -> continuity_record_handoff
continuity_record_handoff -> pending_cleared_and_count_zero_and_armed
pending_cleared_and_count_zero_and_armed -> current_grill_route
continuity_record_write [failed] -> continuity_update_blocked
continuity_update_blocked [changed state and retry remains] -> continuity_record_pending
continuity_update_blocked [retry exhausted] -> frontier_blocked
reminder_awaiting_answer [no] -> reminder_suppressed
grilling_session_terminal [record target exists] -> continuity_record_branch [operation=close]
continuity_record_branch [operation=close] -> continuity_record_closure
continuity_record_closure [retained, promoted, or removed] -> continuity_record_closure_handoff
continuity_record_closure [domain_authority_conflict] -> continuity_closure_blocked
continuity_closure_blocked [changed state and retry remains] -> continuity_record_branch [operation=close]
continuity_closure_blocked [retry exhausted] -> frontier_blocked
continuity_record_closure_handoff -> terminal
```

Every transition retains the exact packet, digest, route evidence, and consumer.

`pre_answer_review` requires the exact question/options digest and `answer=null`. `post_answer_review` requires the same question/options digest, the exact human answer, and the prior review digest. No classification or disposition runs from an unreviewed state.

## Invariants

- Facts and normative decisions remain distinct; the griller never answers for the human.
- Reviewer and facilitator remain separate; reviewer findings and candidate fixes/options precede the human answer, and answer review precedes gap classification.
- No branch closes by assumption and no ticket resolves from an unreviewed answer.
- The frozen question packet is reviewed before presentation; the answer packet is reviewed before human disposition.
- The presented packet is the reviewed packet; a post-review change is itself reviewed before presentation.
- Each classified gap has exactly one mapped executable route; source labels are normalized before transition and route-specific evidence and the downstream consumer must validate before transition.
- The griller/controller selects only the mapped route; the deep reviewer cannot select or emit it.
- Under the `lead_ungoverned` governance profile, the human-disposition step resolves automatically only after the human's option is admitted: the controller applies the post-answer finding's `bottom_line_fix` for that option. The reviewer never answers, selects the option, or applies a fix in any profile.
- A `with-docs` branch reaches Domain Modeling through `domain_modeling_handoff` only after the typed human-disposition and governance checks; the exact reviewed packet and publication branch remain bound through that handoff.
- Path-finder maps are backend-neutral and require destination, bounded dimensions, dependency edges, fog, exclusions, resolution state, frontier, and a `to-spec` consumer; its executable return route is `to_spec_return`.
- Multi-party packets stand alone, use plain language, carry the Grilling return binding, and re-enter this module on the recipient's answer.
- A route name without its executable operation and validated evidence cannot complete the round; `terminal` is the executable terminal for `whole_gap_closed`, and `stateless_return` is the executable plain branch.
- A `Not sure, help me narrowing it down` answer never enters gap classification or closure; it preserves the reviewed packet and starts one guided-narrowing question.
- A retry changes the frontier, question scope, or bound evidence.
- The continuity-record count equals the ordered pending-round set length; a prompt is emitted only from `armed` at five, `awaiting_answer` prevents repeat presentation, a successful same-session same-target update clears the set and resets the count, and a No suppresses further prompts until the session terminal.
- One `grilling_session_id` and at most one working-record target remain bound through every round, compaction, re-entry, update, and closure; a pending Yes write is a barrier to the next round.

## Recovery

- Missing question/options or pre-answer review returns `clarification_required`; missing owner or meaning returns `clarification_required`; hidden dependencies block and split the ticket.
- Missing answer review blocks gap classification and human disposition.
- Missing or incompatible mapped route, route evidence, or consumer returns `clarification_required`.
- New dimensions create successor tickets; ownership conflict preserves each claim and routes to the owning human.
- Retry requires a changed frontier, answer, evidence set, or ticket scope.
- A failed continuity-record update preserves `awaiting_answer`, update intent, pending rounds, record target, count, and the next-round barrier through bounded changed-state retry; exhausted recovery remains `frontier_blocked`.
- A failed continuity-record close preserves the session, target, close intent, and intended disposition through Domain Modeling recovery; closure completes only from `continuity_record_closure_handoff`.

## Completion

Complete the current phase only when its own contract holds: `pre_answer` requires the frozen question/options packet, `answer=null`, current pre-answer findings, and the human-answer consumer; `post_answer` requires the exact answer, prior review binding, current post-answer findings, and the classification consumer; the final phase requires the classified gap, one executable route with evidence and consumer, and every selected `publication_branch`, `continuity_record_branch`, `path_finder_map`, or `multi_party_packet` complete. A continuity update additionally requires a matching session and target, represented pending rounds, successful read-back, cleared pending set, zero count, and `armed` state. A session terminal with a record target additionally requires a confirmed retain, promote, or remove result. Stateless branches explicitly prove that no repository or persistence request existed.
