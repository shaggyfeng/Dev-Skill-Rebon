# diagnosing-bugs reference

---

**name:** `diagnosing-bugs`

**description:** Diagnosis loop for hard bugs and performance regressions. Use when the user says "diagnose"/"debug this", or reports something broken/throwing/failing/slow.

# Diagnosing Bugs

A discipline for hard bugs — skip phases only when explicitly justified. Read `CONTEXT.md` (if it exists) for a mental model of the relevant modules, and check area ADRs.

**Redact before showing evidence.** Commands, outputs, logs, and captured artifacts are shown in this workflow: first replace API keys, passwords, cookies, authorization headers, private URLs, and unnecessary personal data with `<REDACTED>`. Build loops against environment variables or a local secret store so credentials stay out of captured output. When an artifact mixes credentials with diagnostic signal, show only signal-carrying lines. If redacted evidence is insufficient, say what's missing and ask for a safer capture — never request a secret pasted into chat.

## Phase 1 — Build a feedback loop

**This is the skill; everything else is mechanical.** With a **tight** pass/fail signal that goes red on _this_ bug, you will find the cause — bisection, hypothesis-testing, and instrumentation just consume it. Without one, no amount of staring at code saves you. Spend disproportionate effort here; be aggressive, creative, and unwilling to give up.

**Ways to construct one, roughly in order:**

1. **Failing test** at whatever seam reaches the bug — unit, integration, e2e.
2. **Curl / HTTP script** against a running dev server.
3. **CLI invocation** with a fixture input, diffing stdout against a known-good snapshot.
4. **Headless browser script** (Playwright/Puppeteer) — drives the UI, asserts on DOM/console/network.
5. **Replay a captured trace** — save a real request/payload/event log to disk; replay it through the code path in isolation.
6. **Throwaway harness** — minimal subset of the system (one service, mocked deps) exercising the bug path with a single call.
7. **Property/fuzz loop** — for "sometimes wrong output", run 1000 random inputs and look for the failure mode.
8. **Bisection harness** — bug between two known states (commit, dataset, version): automate "boot at state X, check, repeat" and `git bisect run` it.
9. **Differential loop** — same input through old vs new version (or two configs), diff outputs.
10. **HITL bash script** (last resort) — if a human must click, drive them via `scripts/hitl-loop.template.sh` so the loop stays structured; captured output feeds back.

Build the right loop and the bug is 90% fixed. Then **tighten** it, treating the loop as a product: faster (cache setup, skip unrelated init, narrow test scope); sharper signal (assert the specific symptom, not "didn't crash"); more deterministic (pin time, seed RNG, isolate filesystem, freeze network). A 30-second flaky loop barely beats no loop; a 2-second deterministic one is a debugging superpower.

**Non-deterministic bugs:** the goal is a **higher reproduction rate**, not a clean repro — loop the trigger, add stress, narrow timing windows, inject sleeps sequentially; parallelize requests only as the mode table permits.

**When you genuinely cannot build a loop:** stop and say so explicitly, list what you tried, and ask the user for (a) access to a reproducing environment, (b) a captured artifact (HAR, log dump, core dump, timestamped screen recording), or (c) permission for temporary production instrumentation. Do **not** proceed to hypothesising without a loop.

**Completion criterion — a tight loop that goes red.** Phase 1 is done when you can name **one command** (script path, test invocation, curl) you have **already run at least once** (show the invocation and redacted output) that is:

- [ ] **Red-capable** — drives the actual bug code path and asserts the **user's exact symptom**; can go red on this bug, green once fixed. Not "runs without erroring" — it must catch *this* bug.
- [ ] **Deterministic** — same verdict every run (flaky bugs: a pinned high reproduction rate).
- [ ] **Fast** — seconds, not minutes.
- [ ] **Agent-runnable** — unattended; a human in the loop only via `scripts/hitl-loop.template.sh`.

Catching yourself reading code to build a theory before this command exists? **Stop — jumping straight to a hypothesis is the exact failure this skill prevents.** No red-capable command, no Phase 2.

## Phase 2 — Reproduce + minimise

Run the loop; watch it go red. Confirm: the loop produces the failure mode the **user** described (not a nearby different failure — wrong bug, wrong fix); it reproduces across runs (or at a high enough rate for non-deterministic bugs); and you've captured the exact symptom (error message, wrong output, slow timing) for later fix verification.

**Minimise:** shrink to the **smallest scenario that still goes red** — cut inputs, callers, config, data, steps **one at a time**, re-running after each cut. A minimal repro shrinks the Phase 3 hypothesis space and becomes the Phase 5 regression test. Done when **every remaining element is load-bearing** — removing any one makes the loop go green. Do not proceed until you have reproduced *and* minimised.

## Phase 3 — Hypothesise

Generate **3–5 ranked hypotheses** before testing any — single-hypothesis generation anchors on the first plausible idea. Each must be **falsifiable** with a stated prediction: *"If <X> is the cause, then <changing Y> will make the bug disappear / <changing Z> will make it worse."* No prediction = a vibe — discard or sharpen. **Show the ranked list to the user before testing** (domain knowledge re-ranks instantly, or rules hypotheses out); don't block on it — proceed with your ranking if the user is AFK.

## Phase 4 — Instrument

Each probe maps to a specific Phase 3 prediction; **change one variable at a time**. Tool preference: (1) debugger/REPL inspection where supported — one breakpoint beats ten logs; (2) targeted logs at the boundaries that distinguish hypotheses; never "log everything and grep". **Tag every debug log** with a unique prefix (`[DEBUG-a4f2]`) so cleanup is a single grep — untagged logs survive, tagged logs die. **Perf branch:** for performance regressions logs are usually wrong — establish a baseline measurement (timing harness, `performance.now()`, profiler, query plan), then bisect. Measure first, fix second.

## Phase 5 — Fix + regression test

Write the regression test **before the fix** — but only at a **correct seam**: one where the test exercises the **real bug pattern** as it occurs at the call site. A too-shallow seam (single-caller test for a multi-caller bug; a unit test that can't replicate the triggering chain) yields false confidence. **If no correct seam exists, that itself is the finding** — note it: the architecture is preventing the bug from being locked down; flag it for Phase 6. With a correct seam: (1) turn the minimised repro into a failing test there; (2) watch it fail; (3) apply the fix; (4) watch it pass; (5) re-run the Phase 1 loop against the original un-minimised scenario.

## Phase 6 — Cleanup + post-mortem

Required before declaring done:

- [ ] Original repro no longer reproduces (re-run the Phase 1 loop)
- [ ] Regression test passes (or the seam absence is documented)
- [ ] All `[DEBUG-...]` instrumentation removed (grep the prefix)
- [ ] Throwaway prototypes deleted (or moved to a clearly-marked debug location)
- [ ] The hypothesis that proved correct is stated in the commit/PR message — the next debugger learns from it

**Then ask: what would have prevented this bug?** When the answer involves architectural change (no good test seam, tangled callers, hidden coupling), hand off to `/improve-codebase-architecture` with the specifics — after the fix is in, not before; you have more information now than when you started.

---
