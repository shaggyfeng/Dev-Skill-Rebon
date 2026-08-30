# Diagnosing bugs

## Contract

| Field | Binding |
|---|---|
| `contract_version` | `devskill.module.v0.2` |
| Envelope | input and output each carry `runtime.module-envelope.v0.1` |
| Prerequisites | trigger matched; complete current input/envelope, authority, runtime file, producer, and consumer admitted |
| Trigger | applicable symptom, failed verification, regression, performance failure, or unexplained runtime behavior |
| Input | `work.diagnosis-request.v0.1`: `task_id`, `scope_digest`, frozen symptom, bounded environment, snapshot, safe evidence policy, producer, consumer |
| Output | `work.diagnosis-result.v0.1`: reproduction, observed symptom, minimized case, hypotheses/predictions, probes, supported cause, fix scope, regression seam, status, consumer |
| Authority | read, reproduce, and bounded temporary instrumentation only |
| Failure | `diagnosis_environment_required` or `diagnosis_blocked` |
| Consumer | `work.execution`, Work recovery, or architecture survey |

## Operation

1. Redact secrets and irrelevant personal data; never request a secret pasted into chat.
2. Loop gate: build and run one named command that catches this exact bug — red-capable, deterministic, fast, agent-runnable, already run once. "Runs without erroring" is not red-capable. No red-capable command, no hypothesising: a genuine no-loop case stops and asks for a reproducing environment, a captured artifact, or instrumentation permission, returning `diagnosis_environment_required`.
3. Minimisation: shrink the input while preserving the failure; minimisation completes when every remaining element is load-bearing.
4. Hypothesis gate: generate three to five ranked, distinguishable, falsifiable hypotheses before testing any, each with a stated prediction; no prediction, no hypothesis. Show them to the human before testing without blocking on an absent human.
5. Probe rules: each probe maps to one prediction and changes one variable at a time; debugger inspection before logs; every debug log carries a unique tag so cleanup is one search.
6. Establish a measurement baseline before performance changes.
7. Identify the smallest cause supported by discriminating evidence.
8. Regression gate: define the regression seam and write the regression test before the fix, at a correct seam. No correct seam is itself the finding: return an architecture result with the diagnosis, and the handoff happens after the fix. Remove temporary instrumentation unless explicitly retained.
9. Post-mortem: re-run the reproduction, remove instrumentation, delete throwaways, and state the confirmed hypothesis for the next debugger.
10. When a fix is requested, return the diagnosis to Work; Work compiles a new `work.execution-argument.v0.1` from the bounded fix scope and revalidates the lease before execution. Diagnosis never dispatches the fix.

## Parallel-normal dispatch

Independent read-only hypotheses and reproductions may run concurrently when they do not contend for the same runtime resource. One integrator compares all evidence and emits the diagnosis. No parallel durable mutation occurs during diagnosis.

## Invariants

- No cause without discriminating evidence and no fix before a red-capable signal unless its absence is proven.
- A diagnosis result cannot substitute for `work.execution-argument.v0.1`, a write-set lease, review, or execution authorization.
- Flaky evidence remains quantified and uncertain.
- Diagnosis does not authorize the durable fix.

## Recovery

- No red-capable loop and no reproducing environment, captured artifact, or granted instrumentation permission returns `diagnosis_environment_required`.
- Flaky signals use bounded repetitions and report rate; conflicting evidence adds one discriminating probe.
- Architecture that prevents a regression seam returns an architecture finding with the diagnosis; exhausted bounded probes return `diagnosis_blocked`.

## Completion

Complete only when the loop gate, hypothesis gate, probe rules, minimisation, regression gate, and post-mortem resolve as stated, the exact symptom is reproduced, causal evidence supports the diagnosis, fix scope and regression seam are bounded, and one consumer is named. An unreproduced or blocked diagnosis returns its declared failure route and cannot reach Completion.
