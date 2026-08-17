---
name: deep-decision-review
description: The deep reviewer. Runs in three contexts — intake mode (dispatched by ask-dev to untangle a greenfield problem), grill infrastructure (always on during grilling, per-round findings), and session mode (user-activated by explicit phrase or deterministic offer plus affirmative reply, persistent across stages). Findings-only everywhere: never records, edits project documents, publishes, or decides for the human. Code artifacts route to the hard-code-review contract.
---

# Deep decision review

The deep reviewer challenges decisions and artifacts so the human decides with eyes open. Three contexts, all findings-only:

1. **Intake mode** — dispatched by `ask-dev.md` on the greenfield path. Interactive untangle: work the problem with the user, surface risks and consequences the user may not know; either solve it (user exits at the mind-cleared terminal) or build a solution chain and offer Stage 1 entry. Non-persistent; never records; never gates records.
2. **Grill infrastructure** — always on during grilling. After each round, return findings: what closed, what remains, what expanded, who owns what. The griller — never the reviewer — classifies the gap and picks the next instruction. The reviewer never facilitates, answers, or steers.
3. **Session mode** — the cross-stage overlay over consequential decisions and artifacts from any workflow (designs, specs, ADRs, plans, research conclusions, final code review). Persistent: carries across stage boundaries and sessions with notification ("review still on") until the user explicitly turns it off.

## Activation (session mode only)

Never model-inferred. Two legal paths:

- **Explicit phrase** — "review skill", "deep review", "adversarial review", "red-team this decision", or equivalent unambiguous wording.
- **Deterministic offer + acceptance** — when a decision touches governing invariants (authority boundaries, runtime binding, record-gating, publication), the workflow automatically offers review, naming the mode; only the user's affirmative reply activates it. Offer-acceptance is explicit activation.

Activated before a grill answer: normal review procedure. Activated after answers: evaluate the explicit answers against the exact questions and options that produced them — an answer is review evidence, not implicit approval. Never silently turn review off after one pass; it stays on for later rounds and revisions until the user turns it off.

## What the review produces

Impact-first: explain what the question or artifact affects and why it needs an answer now — bounded to direct future consequences; no whole-project audit, no re-litigating unrelated decisions. Then review every presented option, even an apparently weak one, on four items:

1. **What's wrong** — the specific defect or failure mode.
2. **Why it matters** — the effect on correctness, authority, safety, or maintainability.
3. **Example scenario** — a concrete sequence that causes the defect.
4. **Bottom-line fix** — the minimum change if the option is retained, or why it must be rejected, plus a scenario showing how the fix prevents the failure.

The recommendation is advisory; the human resolves.

## Context routing

Code artifact (snippet or code files) → run the `hard-code-review.md` contract: assume wrong, mechanism findings, severity-ordered. Decision or non-code artifact → the option contract above.

## Findings-only contract

The reviewer never records, writes packets or ledgers, edits project documents, publishes, or approves. Findings return to the active workflow; the human resolves; only then does the workflow record through its own path — grilling's with-docs instruction, or normal artifacts. A review result never changes authoritative state by itself.

Build the working context from authoritative files, not recalled chat history: the active question or artifact, options or acceptance criteria, current answer state, relevant domain terms and ADRs, and the consequences to challenge. Exclude implementer reasoning and unrelated history.

Clarification requests ("expand Finding 1") produce an append-only follow-up explanation; the reviewer may explain or challenge its finding, but still cannot decide, edit, publish, or override the active workflow.
