# Mode gate

## Contract

Mode Gate alone owns provider and host evidence, suggested mode and host, human confirmation, governance, roadmap intent, capability validation, admission, and mode re-entry. Missing required evidence or capability returns `mode_not_admitted` before task work.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| The selected host is `rebon` and native capability must validate | [Rebon host adapter](successor-v0/modules/rebon-host-adapter.md) | Native capability confirmation for admission or `mode_not_admitted` |
| Admission confirms an optimized mode | [Context Optimization](successor-v0/modules/context-optimization.md) | Context-fit operation after admission; it never decides the mode |
| Admission completes | [Route](successor-v0/stage-0-route.md) | One family, overlay, or Route terminal from the admitted profile |
| An admitted governance problem changes design meaning, authority, or scope | [Grilling](successor-v0/modules/grilling.md) | Accepted rule or next question under the selected governance profile |

## Inputs

Read trusted provider and host capability metadata without asking a provider question. Do not use model names or installed commands as host identity.

## Evidence-based suggestion

Use trusted host or session metadata, then the named adapter configuration and documented environment variables. Establish provider, host, usable subagents, context capacity, workload, and display support without exposing secret values.

### Evidence precedence and operation

1. Trust runtime host/session metadata first.
2. If a field remains unresolved, inspect only the named adapter's documented environment-variable names and host configuration or adapter probe. Do not enumerate arbitrary environment variables or infer locality from a model name, URL shape, or speed.
3. Normalize evidence into provider kind, host kind, usable subagents, context capacity, workload request, and display support. Record evidence sources and classifications, not secret contents.
4. Compile at most one suggested mode and one suggested host with the supporting capability evidence. Conflicting evidence fails closed; do not merge profiles.
5. Present one evidence-supported host and mode suggestion for human confirmation before mode selection. Governance never bypasses this confirmation.
6. Missing capability evidence, unresolved provider/session identity, or a conflicting suggestion returns the existing typed unresolved/admission failure. Never invent a suggestion or capability proof.

Provider discovery authorizes DevSkill routing only; it does not grant project-runtime authority, approval, publication, or human decision power.

## Delegated profile

A delegated task inherits its parent's admitted host, mode, and relevant task boundary. It does not rerun mode selection or prove nested subagent capacity. A changed host, mode, scope, or consumer returns to Mode Gate.

## Admission order

1. Validate trusted provider/host capability evidence in a supplied inherited profile; when its delegated-task bindings match, reuse it and emit the admitted profile.
2. Without a valid inherited profile, read provider metadata and capability evidence before asking for mode, host, or roadmap intent.
3. Normalize the evidence and compile one suggested host and mode.
4. Ask the human to confirm the suggestion. If the human declines or evidence is unresolved, ask mode first from the four choices, then host. Do not inspect provider evidence again.
5. Resolve `roadmap_checkbox_update` after host selection and before implementation; resolve governance only when the user names or accepts roadmap or session-goal continuation.
6. Derive the review route from the task and roadmap or merge intent.
7. Validate the selected mode against its applicable provider, host, subagent, planning-tool, structured-output, and handoff capabilities.
8. Announce the admitted host, provider, mode, subagent use, Context Optimization use, roadmap intent, and review route once. Use the matching Runtime reference after admission.

## Admission representation

```text
trusted evidence
  -> suggested host and mode
  -> confirmed suggestion | explicit mode choice
  -> host choice when required
  -> roadmap or governance choice when relevant
  -> capability validation
  -> admitted | mode_not_admitted
```

No state silently advances. Conflicting evidence, unresolved identity, missing required capability, or an invalid host-mode pairing returns `mode_not_admitted` and asks only for the missing decision.

## Mode selection

Show exactly these four choices:

| Choice | Delegation | Context handling | Required capability |
|---|---|---|---|
| `parallel-normal` | parallel bounded tasks | normal context | parallel task execution and usable subagents |
| `parallel-optimized` | parallel bounded tasks | optimized context | parallel task execution, usable subagents, structured output, bounded recovery |
| `sequential-normal` | one task at a time | normal context | sequential execution |
| `sequential-optimized` | one task at a time | optimized context and handoff | planning tools and durable handoff |

The selected value is the execution mode. Provider evidence may suggest one, but it is not admitted until confirmed or selected. `Not sure, help me narrowing it down.` is a decision-support choice, not a fifth mode: ask one mode-fitting question at a time and retain the same choice until a mode is selected.

Context Optimization runs only in the two optimized modes through the Runtime reference. A normal-mode context failure returns here with an optimized mode suggested; it never silently becomes an optimized run.

If a trusted user-authored mode preference exists in agent memory, announce it and present the same four choices for confirmation or override. DevSkill stores no preference. On a persistence request, direct the host to save the confirmed mode outside this package.

## Capability validation

| Mode | Required capability |
|---|---|
| `parallel-normal` | parallel task execution and usable subagents |
| `parallel-optimized` | parallel task execution, usable subagents, structured output, and bounded recovery |
| `sequential-normal` | sequential execution |
| `sequential-optimized` | sequential execution, planning tools, and durable handoff |

On missing capability, return `mode_not_admitted`, identify the missing capability, and show the four choices again. Do not silently change the mode.

`sequential-optimized` requires a usable continuation location. Missing continuation capability returns `mode_not_admitted`; it does not permit an oversized direct read.

## Host

| Evidence | Host |
|---|---|
| Trusted Rebon runtime metadata | `rebon` |
| Trusted non-Rebon runtime metadata | `non-rebon` |
| No trusted host identity | ask one host question; remain non-admitted until answered |

An installed command, provider, model, endpoint, or Agent capability does not establish host identity.

## Roadmap checkbox

Resolve `roadmap_checkbox_update` as `yes` or `no` before implementation. Ask one question only when the task does not resolve it. `yes` selects implementation/acceptance checklist-close review. `no` selects design or implementation review.

Resolve `merge_pr_review` as `yes` or `no` before a merge-PR review. `yes` selects `checklist_close` independently of `roadmap_checkbox_update`.

Resolve `review_route` as `grill`, `implementation_logic`, or `checklist_close` from the Route trigger precedence. An unresolved review route remains non-admitted.

## Governance profile

Resolve the governance profile only when the user names or accepts roadmap or session-goal continuation; never on internal re-entry or checkpoints. Ask exactly:

> Are you the project tech lead and do you know the project design well enough to guide the junior developer?

| Answer | Profile | Continuation | Problem handling |
|---|---|---|---|
| Yes | `lead_ungoverned` | Continue until every goal explicitly set by the user in the current session is complete; a goal the user sets mid-session enters the scope as it is set | Use the Grilling reference with the reviewer, apply the recommended solution automatically, and do not add a user-confirmation stop |
| No | `pair` | Do not enable ungoverned continuation | Use the Grilling reference with the reviewer and user; a blocker that changes design meaning, authority, or scope, or crosses a permission boundary, pauses for the user's decision; a mechanical blocker is fixed and reported without stopping |
| Unclear | `governance_unresolved` | Do not enable ungoverned continuation; the session proceeds governed | Ask one bounded follow-up that restates that lead applies recommended fixes without stopping and pair pauses for decisions, and request an explicit lead or pair; never infer a profile |

Under `pair`, classify each blocker by its nature as it arises; never defer classification or batch decisions.

When the last in-scope goal completes, `lead_ungoverned` terminates: announce completion and stop. The agent never adds goals and never rolls into roadmap items from earlier sessions.

The governance question re-fires only at the user's next explicit continuation selection — after a `lead_ungoverned` termination, from the `governance_unresolved` state, or on the user's explicit profile change.

Permission and authority enforcement belongs to the agent setting, not this skill; it fires regardless of profile, and `pair`'s pause never overrides it. The skill never grants authority and adds no gate-checking rules. Continued execution, a reviewer recommendation, or a Rebon task status is never permission.

Announce the selected profile once — with the admitted profile when governance resolves during admission, and at the moment of resolution when it resolves after admission; internal re-entry stays silent.

## Admission result

Admission requires resolved host, provider, mode, capability fit, and review route. It also requires a roadmap answer before implementation and a governance answer only for session or roadmap continuation. `route_status=admitted` is required before repository work.

When the host is Rebon, admission also requires the Rebon adapter to confirm the selected native tool path.

## Recovery

- Missing provider/session identity or conflicting evidence returns the typed unresolved/admission failure; it cannot produce an admitted profile or invented suggestion.
- Missing host identity returns one host question and remains non-admitted.
- Missing mode capability returns `mode_not_admitted` without changing the selected value.
- Conflicting or insufficient provider evidence returns the typed unresolved/admission failure; it never produces an invented suggestion or silently changes the selected mode.
- Changed host, provider, mode, roadmap-checkbox intent, or governance profile invalidates the profile and reruns admission order.

## Completion

Complete only when the profile fields are present, capability validation passes, the host adapter is admitted when applicable, and `route_status=admitted`.
