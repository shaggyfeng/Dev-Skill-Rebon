# Mode gate

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Envelope fields | `contract_version`, `task_id`, `scope_digest`, `input_digest`, `output_digest`, `producer`, `consumer`, `authority`, `status`, `failure_route`, `retry_budget` |
| Input | `route.mode-evidence.v0.1` |
| Output | `development-execution-profile.v0.1` |
| Status | `complete`, `blocked`, `reopen`, or `invalidated` |
| Failure | `mode_not_admitted` when capability or required identity is missing |

## Inputs

Read trusted provider and host capability metadata without asking a provider question. Do not use model names or installed commands as host identity.

## Evidence-based suggestion

`route.mode-evidence.v0.1` binds trusted host/session metadata, the named adapter's documented environment-variable names and host configuration/probe results, normalized host and provider kinds, subagent availability, context capacity, workload request, display support, redacted evidence-source identifiers, contradictions, and a typed suggested mode/host. It never carries secret values, tokens, cookies, credentials, or full secret-bearing URLs.

### Evidence precedence and operation

1. Trust runtime host/session metadata first.
2. If a field remains unresolved, inspect only the named adapter's documented environment-variable names and host configuration or adapter probe. Do not enumerate arbitrary environment variables or infer locality from a model name, URL shape, or speed.
3. Normalize evidence into provider kind, host kind, usable subagents, context capacity, workload request, and display support. Record evidence sources and classifications, not secret contents.
4. Compile at most one suggested mode and one suggested host with the supporting capability evidence. Conflicting evidence fails closed; do not merge profiles.
5. When continuation activates governance, resolve the applicable governance profile before applying any suggestion. Under current explicitly selected `lead_ungoverned`, apply one valid evidence-supported suggestion without asking. Under `pair`, `governance_unresolved`, or ordinary governed execution, preserve human choice and require the human to select or confirm from the four modes.
6. Missing capability evidence, unresolved provider/session identity, or a conflicting suggestion returns the existing typed unresolved/admission failure. Never invent a suggestion or capability proof.

Provider discovery authorizes DevSkill routing only; it does not grant project-runtime authority, approval, publication, or human decision power.

## Delegated profile

A delegated task may consume a parent-emitted `development-execution-profile.v0.1` only when its provider/host capability evidence is trusted, `route_status=admitted`, and `{task_id, scope_digest, mode, host, roadmap_checkbox_update, review_route, consumer}` match the dispatch. The successful parent dispatch is the capability evidence for `subagents=yes`. The delegated task does not rerun mode selection or test its own nested-subagent capability. A missing, stale, or mismatched inherited profile returns `mode_not_admitted` to the parent.

## Admission order

1. Validate trusted provider/host capability evidence in a supplied inherited profile; when its delegated-task bindings match, reuse it and emit the admitted profile.
2. Without a valid inherited profile, read provider metadata and capability evidence before asking for mode, host, or roadmap intent.
3. Normalize the evidence and compile any typed suggestion before mode selection; do not apply it until the applicable governance profile is known.
4. If governance is `lead_ungoverned` and exactly one valid evidence-supported suggestion exists, apply that one suggestion. If evidence is unresolved, insufficient, or conflicting, return the typed unresolved/admission failure instead. Otherwise announce any valid candidate and request confirmation; if it is not confirmed, present the four mode choices.
5. Resolve the mode from confirmation or the selected four-choice value; do not auto-correct an unconfirmed value.
6. Resolve host after mode selection.
7. Resolve `roadmap_checkbox_update` after host selection and before implementation.
8. Validate the selected mode against provider, host, subagent, planning-tool, structured-output, and handoff capabilities.
9. Emit the admitted profile once all fields and `route_status=admitted` are valid.

## Admission representation

The mode-admission relation is one ordered, fail-closed representation:

```text
provider_evidence_bound
 -> suggestion_compiled | mode_not_admitted
suggestion_compiled
 -> governance_resolved | mode_not_admitted
governance_resolved
 -> mode_selected | mode_not_admitted
mode_selected
 -> host_selected | mode_not_admitted
host_selected
 -> roadmap_intent_bound | mode_not_admitted
roadmap_intent_bound
 -> capability_validated | mode_not_admitted
capability_validated
 -> admitted | mode_not_admitted
```

| State | Required representation | Success binding |
|---|---|---|
| `provider_evidence_bound` | trusted provider/session metadata, named-adapter evidence, normalized provider and host kinds, and redacted evidence sources | one evidence-backed suggestion candidate or a typed unresolved/admission failure |
| `suggestion_compiled` | at most one suggested mode and one suggested host with supporting capability evidence and no contradictions | governance resolution before suggestion application |
| `governance_resolved` | `lead_ungoverned`, `pair`, or `governance_unresolved` with its continuation rule | confirmed or explicitly selected mode from the four canonical choices |
| `mode_selected` | exactly one of `parallel-normal`, `parallel-optimized`, `sequential-normal`, or `sequential-optimized` | host resolution |
| `host_selected` | trusted `rebon` or `non-rebon` identity, or one answered host question | roadmap-checkbox and review intent resolution |
| `roadmap_intent_bound` | `roadmap_checkbox_update`, `merge_pr_review`, and `review_route` | selected-mode capability validation |
| `capability_validated` | provider, host, execution, subagent, planning-tool, structured-output, durable-handoff, and task-specific capability checks applicable to the selected mode | `development-execution-profile.v0.1` with `route_status=admitted` |
| `admitted` | `{host, provider, mode, subagents, context_optimization, roadmap_checkbox_update, merge_pr_review, review_route, governance_profile, route_status}` and the shared envelope | Route task-specific work may begin |

Any missing, stale, contradictory, or mismatched binding returns `mode_not_admitted` or the existing typed unresolved/admission failure; no state silently advances and no fifth mode is introduced.

## Mode selection

Show exactly these four choices:

| Choice | Delegation | Context handling | Required capability |
|---|---|---|---|
| `parallel-normal` | parallel bounded tasks | normal context | parallel task execution and usable subagents |
| `parallel-optimized` | parallel bounded tasks | optimized context | parallel task execution, usable subagents, structured output, bounded recovery |
| `sequential-normal` | one task at a time | normal context | sequential execution |
| `sequential-optimized` | one task at a time | optimized context and handoff | planning tools and durable handoff |

The selected value is the execution mode. Do not add a fifth choice, wizard, or automatic correction. Provider evidence may produce a candidate mode, but the candidate is not admitted until confirmed or explicitly selected.

All four modes use the shared deterministic read-admission contract in `SKILL.md` before model content reads. `parallel-normal` and `parallel-optimized` partition oversized targets into persisted evidence tasks before synthesis. `sequential-normal` and `sequential-optimized` process one bounded coverage unit at a time and emit a current `runtime.handoff.v0.1` between units. Optimized modes additionally run `optimized.preworkflow`; normal modes do not.

If a trusted user-authored mode preference exists in agent memory, announce it and present the same four choices for confirmation or override. DevSkill stores no preference. On a persistence request, direct the host to save the confirmed mode outside this package.

## Capability validation

| Mode | Required capability |
|---|---|
| `parallel-normal` | parallel task execution and usable subagents |
| `parallel-optimized` | parallel task execution, usable subagents, structured output, and bounded recovery |
| `sequential-normal` | sequential execution |
| `sequential-optimized` | sequential execution, planning tools, and durable handoff |

On missing capability, return `mode_not_admitted`, identify the missing capability, and show the four choices again. Do not silently change the mode.

When deterministic read admission proves that a sequential task requires more than one bounded coverage unit, the selected host must provide an approved location or native mechanism for the read-frontier handoff. Missing handoff persistence returns `mode_not_admitted` for that task; it does not permit an oversized direct read.

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

`route_status` must be `admitted` before repository work. `provider=unknown` is allowed only in a pre-admission evidence record; an admitted profile requires resolved provider/session identity. Unresolved provider identity, conflicting evidence, or an invalid suggestion returns the typed unresolved/admission failure and cannot reach `route_status=admitted`. `governance_profile` is admitted as `unresolved`; it persists in the profile once resolved — `lead_ungoverned`, `pair`, or `governance_unresolved` — is announced once at resolution, revalidates at re-entry, and a changed profile invalidates the profile and reruns admission order.

Show the admitted host, provider, mode, subagent use, context optimization, roadmap-checkbox intent, and review route once.

When `host == rebon`, `route_status` remains non-admitted until `successor-v0/modules/rebon-host-adapter.md` admits the selected mode's native tool profile.

## Recovery

- Missing provider/session identity or conflicting evidence returns the typed unresolved/admission failure; it cannot produce an admitted profile or invented suggestion.
- Missing host identity returns one host question and remains non-admitted.
- Missing mode capability returns `mode_not_admitted` without changing the selected value.
- Conflicting or insufficient provider evidence returns the typed unresolved/admission failure; it never produces an invented suggestion or silently changes the selected mode.
- Changed host, provider, mode, roadmap-checkbox intent, or governance profile invalidates the profile and reruns admission order.

## Completion

Complete only when the profile fields are present, capability validation passes, the host adapter is admitted when applicable, and `route_status=admitted`.
