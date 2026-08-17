# to-tickets reference

---

**name:** `to-tickets`

**description:** Break a plan, spec, or the current conversation into a set of tracer-bullet tickets, each declaring its blocking edges. Save append-only local ticket records by default; external publication requires an explicit human request.

**disable-model-invocation:** true

# To Tickets

Break a plan, spec, or conversation into **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it. The issue tracker and triage vocabulary should have been provided — run `/setup-dev-skills` if not.

## Process

1. **Gather context** from the conversation; if the user passes a reference (spec path, issue number/URL), fetch and read its full body and comments.
2. **Explore the codebase** (optional, if not already done) — titles and descriptions use the domain glossary vocabulary and respect area ADRs. Look for prefactoring opportunities: "make the change easy, then make the easy change."
3. **Draft vertical slices** — tracer-bullet tickets where each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests), is demoable or verifiable alone, and fits a single fresh context window; prefactoring goes first. Give each ticket its **blocking edges** (the tickets that must complete before it can start; no blockers = can start immediately).

**Wide refactors are the exception to vertical slicing**: one mechanical change (rename a column, retype a shared symbol) whose blast radius fans across the codebase so no vertical slice can land green. Sequence it **expand–contract**: first *expand* — add the new form beside the old so nothing breaks; then *migrate* call sites in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, CI green batch-to-batch because the old form still exists; finally *contract* — delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even batches can't stay green alone, keep the sequence on a shared integration branch blocking a final integrate-and-verify ticket — green is promised only there.

4. **Quiz the user.** Present the breakdown as a numbered list — per ticket: **Title**, **Blocked by**, **What it delivers** (the end-to-end behaviour). Ask: is the granularity right (too coarse / too fine)? Are the blocking edges correct — each ticket depending only on genuine gates? Should any be merged or split? Iterate until approved.
5. **Publish only after explicit human approval.** The tracker configured by `/setup-dev-skills` decides the shape:

- **Local files** — one file per ticket under `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` in dependency order (blockers first); each "Blocked by" lists its dependencies. One ticket per file, never a combined file.
- **Real tracker (GitHub, Linear, …)** — one issue per ticket in dependency order so blocking edges reference real identifiers; use the platform's native blocking/sub-issue relationship where it exists. Apply the `ready-for-agent` label unless instructed otherwise — the tickets are agent-grabbable by construction.

Work the **frontier**: any ticket whose blockers are all done (a linear chain = top to bottom). Do NOT close or modify any parent issue.

<local-ticket-template>

# <NN> — <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective — not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles of the tickets that gate this one, or "None — can start immediately".

**Status:** ready-for-agent

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</local-ticket-template>

<issue-template>

## Parent

A reference to the parent issue on the tracker (omit if the source wasn't an existing issue).

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective — not layer-by-layer implementation.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- A reference to each blocking ticket, or "None — can start immediately".

</issue-template>

In either form, avoid file paths and code snippets — they go stale fast. Exception: a prototype snippet encoding a decision more precisely than prose (state machine, reducer, schema, type shape) may be inlined, noted as prototype-derived, trimmed to the decision-rich parts.

After approval, Work executes the tickets per the mode table in `dev-skill/SKILL.md` — [ultrawork-orchestration.md](ultrawork-orchestration.md) in isolated or sequential mode; native workflow otherwise.

---
