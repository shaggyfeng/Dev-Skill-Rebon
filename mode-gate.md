# Mode gate

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Envelope fields | `contract_version`, `task_id`, `scope_digest`, `input_digest`, `output_digest`, `producer`, `consumer`, `authority`, `status`, `failure_route` |
| Input | `route.mode-evidence.v0.1` |
| Output | `development-execution-profile.v0.1` |
| Status | `complete`, `blocked`, `reopen`, or `invalidated` |
| Failure | `mode_not_admitted` when capability or required identity is missing |

## Inputs

Read trusted provider and host capability metadata without asking a provider question. Do not use model names or installed commands as host identity.

## Delegated profile

A delegated task may consume a parent-emitted `development-execution-profile.v0.1` only when its provider/host capability evidence is trusted, `route_status=admitted`, and `{task_id, scope_digest, mode, host, roadmap_checkbox_update, review_route, consumer}` match the dispatch. The successful parent dispatch is the capability evidence for `subagents=yes`. The delegated task does not rerun mode selection or test its own nested-subagent capability. A missing, stale, or mismatched inherited profile returns `mode_not_admitted` to the parent.

## Admission order

1. Validate trusted provider/host capability evidence in a supplied inherited profile; when its delegated-task bindings match, reuse it and emit the admitted profile.
2. Without a valid inherited profile, read provider metadata and capability evidence before asking for mode, host, or roadmap intent.
3. If provider evidence yields one capable mode candidate, announce the candidate and request confirmation. If no candidate exists or it is not confirmed, present the four mode choices.
4. Resolve the mode from confirmation or the selected four-choice value; do not auto-correct an unconfirmed value.
5. Resolve host after mode selection.
6. Resolve `roadmap_checkbox_update` after host selection and before implementation.
7. Validate the selected mode against provider, host, subagent, planning-tool, structured-output, and handoff capabilities.
8. Emit the admitted profile once all fields and `route_status=admitted` are valid.

## Mode selection

Show exactly these four choices:

| Choice | Delegation | Context handling | Required capability |
|---|---|---|---|
| `parallel-normal` | parallel bounded tasks | normal context | parallel task execution and usable subagents |
| `parallel-optimized` | parallel bounded tasks | optimized context | parallel task execution, usable subagents, structured output, bounded recovery |
| `sequential-normal` | one task at a time | normal context | sequential execution |
| `sequential-optimized` | one task at a time | optimized context and handoff | planning tools and durable handoff |

The selected value is the execution mode. Do not add a fifth choice, wizard, or automatic correction. Provider evidence may produce a candidate mode, but the candidate is not admitted until confirmed or explicitly selected.

If a trusted user-authored mode preference exists in agent memory, announce it and present the same four choices for confirmation or override. DevSkill stores no preference. On a persistence request, direct the host to save the confirmed mode outside this package.

## Capability validation

| Mode | Required capability |
|---|---|
| `parallel-normal` | parallel task execution and usable subagents |
| `parallel-optimized` | parallel task execution, usable subagents, structured output, and bounded recovery |
| `sequential-normal` | sequential execution |
| `sequential-optimized` | sequential execution, planning tools, and durable handoff |

On missing capability, return `mode_not_admitted`, identify the missing capability, and show the four choices again. Do not silently change the mode.

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
| Yes | `lead_ungoverned` | Continue until every goal explicitly set by the user in the current session is complete; a goal the user sets mid-session enters the scope as it is set | Run grilling with the reviewer, apply the recommended solution automatically, and do not add a user-confirmation stop |
| No | `pair` | Do not enable ungoverned continuation | Run grilling with the reviewer and user; a blocker that changes design meaning, authority, or scope, or crosses a permission boundary, pauses for the user's decision; a mechanical blocker is fixed and reported without stopping |
| Unclear | `governance_unresolved` | Do not enable ungoverned continuation; the session proceeds governed | Ask one bounded follow-up that restates that lead applies recommended fixes without stopping and pair pauses for decisions, and request an explicit lead or pair; never infer a profile |

Under `pair`, classify each blocker by its nature as it arises; never defer classification or batch decisions.

When the last in-scope goal completes, `lead_ungoverned` terminates: announce completion and stop. The agent never adds goals and never rolls into roadmap items from earlier sessions.

The governance question re-fires only at the user's next explicit continuation selection — after a `lead_ungoverned` termination, from the `governance_unresolved` state, or on the user's explicit profile change.

Permission and authority enforcement belongs to the agent setting, not this skill; it fires regardless of profile, and `pair`'s pause never overrides it. The skill never grants authority and adds no gate-checking rules. Continued execution, a reviewer recommendation, or a Rebon task status is never permission.

Announce the selected profile once — with the admitted profile when governance resolves during admission, and at the moment of resolution when it resolves after admission; internal re-entry stays silent.

## Admission result

Admission is complete only when these fields are present:

`{host, provider, mode, subagents, context_optimization, roadmap_checkbox_update, merge_pr_review, review_route, governance_profile, route_status}`

`route_status` must be `admitted` before repository work. Provider remains `unknown` when discovery is incomplete. Provider uncertainty blocks only a capability that cannot be verified. `governance_profile` is admitted as `unresolved`; it persists in the profile once resolved — `lead_ungoverned`, `pair`, or `governance_unresolved` — is announced once at resolution, revalidates at re-entry, and a changed profile invalidates the profile and reruns admission order.

Show the admitted host, provider, mode, subagent use, context optimization, roadmap-checkbox intent, and review route once.

When `host == rebon`, `route_status` remains non-admitted until `successor-v0/modules/rebon-host-adapter.md` admits the selected mode's native tool profile.

## Recovery

- Missing provider evidence leaves `provider=unknown`; missing host identity returns one host question.
- Missing or stale delegated-profile bindings return `mode_not_admitted` to the parent without child-side mode selection.
- Missing mode capability returns `mode_not_admitted` without changing the selected value.
- Changed host, provider, mode, roadmap-checkbox intent, or governance profile invalidates the profile and reruns admission order.

## Completion

Complete only when the profile fields are present, capability validation passes, the host adapter is admitted when applicable, and `route_status=admitted`.

