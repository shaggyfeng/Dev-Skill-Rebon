# to-spec reference

---

**name:** `to-spec`

**description:** Turn the current conversation into a candidate spec — no interview, just synthesis of what you've already discussed. Publication requires an explicit human request.

**disable-model-invocation:** true

Synthesize the current conversation context and codebase understanding into a spec (PRD). Do NOT interview the user. The issue tracker and triage vocabulary should have been provided — run `/setup-dev-skills` if not.

## Process

1. Explore the repo if you haven't already; use the domain glossary vocabulary and respect ADRs in the area.

2. Sketch the test seams: prefer existing seams, use the highest seam possible, propose new seams only at the highest point — the fewer across the codebase the better, ideally one. Confirm the seams with the user.

3. Write the spec from the template below; save as an append-only local record by default; publish externally only after an explicit human request and approval of the final content.

<spec-template>

## Problem Statement

The problem, from the user's perspective.

## Solution

The solution, from the user's perspective.

## User Stories

A LONG, numbered list — extremely extensive, covering all aspects of the feature, each as:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

## Implementation Decisions

Decisions made: modules built/modified and their interfaces, technical clarifications, architectural decisions, schema changes, API contracts, specific interactions. No file paths or code snippets — they go stale. Exception: a prototype snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape) may be inlined within the relevant decision, noted as prototype-derived and trimmed to the decision-rich parts.

## Testing Decisions

What makes a good test here (external behavior, not implementation details), which modules will be tested, and prior art (similar tests in the codebase).

## Out of Scope

What is out of scope for this spec.

## Further Notes

Anything else about the feature.

</spec-template>

---
