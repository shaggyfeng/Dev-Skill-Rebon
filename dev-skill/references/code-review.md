# code-review reference

Three-axis review of the changes since a fixed point — the mandatory close-out of every Work slice, and the response to "review a branch / PR / changes since X".

---

**name:** `code-review`

**description:** Review the changes since a fixed point (commit, branch, tag, or merge-base) along three axes — Standards (repo coding standards), Spec (the originating issue/PRD), and Correctness (attacked with the hard-code-review contract). All three passes run in isolated role contexts, reported side by side. Use when a Work slice completes, implementation finishes, or the user asks to review a branch, PR, work-in-progress changes, or "review since X".

Axes of the diff between `HEAD` and a fixed point:

- **Standards** — conformance to this repo's documented coding standards.
- **Spec** — faithful implementation of the originating issue / PRD / spec.
- **Correctness** — where the code actually fails: assume wrong, hunt failure mechanisms, severity-order findings.

All three run in isolated role contexts so they don't pollute each other; dispatch per the mode table in `dev-skill/SKILL.md`. The issue tracker should have been provided — run `/setup-dev-skills` if `docs/agents/issue-tracker.md` is missing.

## Process

### 1. Pin the fixed point

The fixed point is whatever the user said — commit SHA, branch, tag, `main`, `HEAD~5`. If unspecified, ask. Capture once: `git diff <fixed-point>...HEAD` (three-dot = merge-base) and `git log <fixed-point>..HEAD --oneline`. Confirm the ref resolves (`git rev-parse`) and the diff is non-empty — a bad ref or empty diff fails here, not inside a review pass.

### 2. Identify the spec source

In order: (1) issue references in commit messages (`#123`, `Closes #45`, GitLab `!67`) fetched via `docs/agents/issue-tracker.md`; (2) a path the user passed; (3) a PRD/spec file under `docs/`, `specs/`, or `.scratch/` matching the branch or feature; (4) ask the user — if there's no spec, the Spec pass skips and reports "no spec available".

### 3. Identify the standards sources

Whatever documents how code should be written (`CODING_STANDARDS.md`, `CONTRIBUTING.md`). On top of them, the Standards axis always carries this **smell baseline** — Fowler code smells (_Refactoring_, ch.3) applying even when the repo documents nothing. Two binding rules: **the repo overrides** (a documented standard endorsing a flagged pattern suppresses the smell), and **always a judgement call** (each smell is a labelled heuristic, never a hard violation; skip anything tooling enforces). Each reads *what it is* → *how to fix*:

- **Mysterious Name** — a name that doesn't reveal what it does or holds. → rename; if no honest name comes, the design's murky.
- **Duplicated Code** — the same logic shape in multiple hunks/files. → extract the shared shape.
- **Feature Envy** — a method reaching into another object's data more than its own. → move it onto the data it envies.
- **Data Clumps** — the same few fields/params travelling together. → bundle into one type.
- **Primitive Obsession** — a primitive standing in for a domain concept. → give the concept its own type.
- **Repeated Switches** — the same switch/if-cascade on the same type recurring. → polymorphism, or one shared map.
- **Shotgun Surgery** — one logical change forcing scattered edits. → gather what changes into one module.
- **Divergent Change** — one module edited for unrelated reasons. → split by reason.
- **Speculative Generality** — abstraction/params/hooks for needs the spec doesn't have. → delete; inline until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation. → hide the walk behind one method.
- **Middle Man** — a class/function that mostly delegates onward. → cut it, call the target direct.
- **Refused Bequest** — a subclass ignoring most of what it inherits. → drop the inheritance, use composition.

### 4. Run all three passes in isolated roles

Each pass gets only the diff and its required documents; dispatch per the mode table — concurrent isolated passes where permitted, sequential fresh contexts otherwise.

**Standards brief** — include the diff command and commit list, the standards files found, **plus the smell baseline pasted in full** (the pass has no other access to it). Brief: "Report — per file/hunk where relevant — (a) every documented-standard violation: cite the standard (file + rule); (b) any baseline smell: name it and quote the hunk. Distinguish hard violations from judgement calls; a documented repo standard overrides the baseline; skip anything tooling enforces. Under 400 words."

**Spec brief** — include the diff command, commit list, and the spec contents or path. Brief: "Report: (a) requirements missing or partial; (b) behaviour not asked for (scope creep); (c) requirements that look implemented but wrong. Quote the spec line for each finding. Under 400 words."

Missing spec: skip the Spec pass and note it in the final report.

**Correctness brief** — the `hard-code-review.md` contract: the diff plus exact authority sources (original implementation for a port, minimal surrounding code for types), **no implementer reasoning**. Brief: "Assume the code is wrong; try to prove it. Every finding needs a mechanism. Severity-order within this axis. Compiling or passing tests is not evidence. Under 400 words."

### 5. Aggregate

Present the three reports under `## Standards`, `## Spec`, and `## Correctness` byte-for-byte (formatting-only metadata allowed). Never merge, rewrite, weaken, or rerank findings across axes — severity ordering lives inside the Correctness axis only. End with one line: total findings per axis and the worst issue *within each*. Never pick a winner across axes.

## Why three axes

A change can pass two axes and fail the third: standards-clean, spec-faithful code with a use-after-free (Correctness fail); exactly what the issue asked but breaking conventions (Standards fail); bug-free code implementing the wrong thing (Spec fail). Separate reporting stops one axis masking another. Correctness is always present; a diff with no memory/concurrency/port surface simply gets a shallower pass — error paths and contract violations apply to every diff.

---
