# Rebon Workflow visual display

Applies only when the development host is Rebon, with any selected provider, and Rebon Workflow support is available. In this admitted branch, all delegated DevSkill role work runs through visible Rebon Workflow runs. Codex, Claude, unknown hosts or providers, and any agent without Workflow support remain outside this reference and use their native workflow.

Read together with [ultrawork-orchestration.md](ultrawork-orchestration.md): UltraWork is the methodology and authority contract; the Workflow tool is its visible dispatch vehicle.

## Contents

- Admission and non-interference
- Script classes
- Mandatory visible dispatch
- Preflight display harness
- Kanban execution display
- Structured results and artifact completion
- Recovery and resumed display
- Authority and close stage

## Admission and non-interference

Confirm the Development Execution Profile before dispatch. If Rebon host identity, online-provider status, or Workflow support is unknown, ask the human before task-specific work and keep this reference `not_applicable` until admission is confirmed; never infer admission from a model name or an Agent tool.

The display adds no project authority, gate, task, or plan. Python remains runtime mutation authority, DevSkill remains development methodology, the reviewed applied plan remains the execution-board source, and humans retain every normative decision. A phase or card shown complete in the Workflow UI is orchestration evidence only.

## Script classes

Distinguish these script classes mechanically:

| Script class | When it may exist | Contents | Prohibitions |
| --- | --- | --- | --- |
| `preflight_display_harness` | Before the applied plan exists | Generic parameterized phases for Reader/Task Breaker → Planner → Deep Reviewer → sequential Applier groups | Item-specific execution cards, implementation work, or an execution plan |
| `kanban_execution_script` | Only after every preflight artifact validates | One visible board derived exactly from the reviewed applied plan | Invented cards, unresolved human decisions, or work outside the applied plan |

The execution-script gate blocks only `kanban_execution_script`; it never blocks the generic `preflight_display_harness` required to display the plan-producing roles. Preflight agents may not create or edit either script; the Rebon coordinator authors the generic harness and, after the gate validates, the execution script.

## Mandatory visible dispatch

Inside the admitted branch, dispatch every DevSkill role through a Workflow run: Reader/Task Breaker, Planner, Deep Reviewer, every sequential Applier group, estimators, partition readers, aggregators, assemblers, implementation/test/verification/integration/reconciliation cards, grill option analysis, facilitation, deep review, research verification, the code-review axes (Standards · Spec · Correctness), hard-code-review probes, and repair, split, recovery, and replacement-card roles. Never dispatch these as standalone `Agent` calls.

The coordinator may remain outside the visual run only for orchestration actions: freezing and digesting packets, validating returned artifacts, admitting dependencies and write leases, authoring scripts, consuming compact summaries, and recording mechanical workflow dispositions. Report, ledger, roadmap-evidence, report-index, or other project-artifact writing remains role work and must use visible close-stage cards. Coordinator actions must not hide substantive role work.

## Preflight display harness

Run the four stages sequentially and expose each as a visible phase. The harness is generic and receives the slice packet through arguments. Each role writes its stable artifact and returns a compact structured result containing its role, artifact path, packet digest, verdict or change summary, failure state, and next pointer. Use exact phase titles and role labels:

1. `Reader / Task Breaker`
2. `Planner`
3. `Deep Reviewer`
4. `Plan Applier / <group-id>` for each sequential group

A failed stage keeps the execution-script gate closed. Run bounded repair and the required dependent reruns through a visible recovery Workflow run; never hide repair in a standalone call or rerun the unchanged whole preflight.

## Kanban execution display

Encode the final applied plan one-to-one:

1. Set `meta.name` to the exact slice or plan-item ID; `meta.description` states the goal, admitted scope, principal risk, and verification boundary.
2. Set `meta.phases` to the applied-plan card groups in dependency order; every `phase(title)` uses the identical group title.
3. Set every worker `label` to its exact card ID and `phase` to its card-group title.
4. Use `pipeline()` for independent per-card stage flow; use `parallel()` only for a true barrier declared by the applied plan.
5. Display focused verification, relevant integration verification, and any admitted hard-code-review probe as explicit labeled phases.
6. Carry the card contract and context envelope in the worker prompt; carry prior results through structured prompt data, not control metadata.
7. Respect `effective_parallelism`, engine call limits, write-set isolation, the 350-model-token content-bearing write/edit ceiling, and the stage-size limits in `ultrawork-orchestration.md`.
8. Use `log()` to disclose bounded coverage, sampling, top-N limits, or no-retry behavior.
9. Run even a one-card slice through a Workflow run; small size is not a visibility exemption.

The display renders plan truth, not decoration: no invented display-only phase names, no merged cards for appearance, no fake barriers, no failed or absent result shown complete.

## Structured results and artifact completion

Every displayed role returns a structured envelope: `card_id`, `packet_digest`, status, artifact paths and digests, exact checks, failures, unresolved authority, and next pointer. A file-producing role is complete only after read-back or digest verification; a structured stage is complete only after a valid structured result is emitted.

Treat a `null` pipeline result, missing artifact, digest mismatch, invalid structured result, or throwing stage as failed in both the run result and the durable report. Text claiming success cannot override a failed envelope.

## Recovery and resumed display

- Resume a valid failed run with `resumeFromRunId` so completed stages replay from cache.
- After the same bounded stage fails twice, split that stage and display the narrower replacements; never launch it unchanged a third time.
- Repair a script-load error in the script and resume the same run.
- Preserve partial artifacts and invalidate only dependent later cards or writer fragments.
- Display recovery agents and replacement cards with traceable IDs and phases.
- If the reviewed plan changes materially, return to the visible preflight harness before building a replacement execution script.

## Authority and close stage

The Workflow run returns compact card evidence. It does not implicitly update roadmaps, publish decisions, mutate Python-owned runtime state, or satisfy a human gate. When existing authority admits close-stage artifact changes, the applied plan includes visible cards for the report, ledger, roadmap evidence, report index, or other project files. After those cards pass, the coordinator mechanically validates their evidence and records the final workflow disposition: exact run, applied-plan digest, card dispositions, verification evidence, and next pointer. Human answers and approvals remain outside the Workflow run.

Never let the Workflow tool or its agents invent cards, decide unresolved human questions, publish human-owned decisions, expand write sets, impersonate the human, or use Rebon subagents when the selected provider is local.
