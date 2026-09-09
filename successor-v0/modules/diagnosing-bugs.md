# Diagnosing Bugs

## Trigger and boundary

Run for a bounded symptom, failed verification, regression, performance failure, or unexplained runtime behavior. A user naming Diagnosis is sufficient. Diagnosis produces evidence; Work owns the choice and execution of a fix.

## Diagnosis structure

| Part | Definition |
|---|---|
| Feedback loop | One command or repeatable action that drives the exact reported symptom and produces a pass/fail result |
| Tight loop | A feedback loop that is red-capable, deterministic or repeatably high-rate, fast, and runnable without unattended human interpretation |
| Minimal reproduction | The smallest input, configuration, caller, and step set that still makes the tight loop fail |
| Hypothesis | A distinct falsifiable cause with a prediction: if the cause is true, changing one named variable changes the result |
| Probe | One observation or intervention that tests one hypothesis prediction |
| Regression seam | The public behavior location where the real bug pattern can become a lasting test |

Return the reproduction, minimized case, hypotheses and predictions, probes, supported cause, bounded fix scope, regression seam, and any missing environment or permission. Diagnosis does not choose or apply the fix.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| A bounded symptom enters Work | [Work](../work.md) | Diagnosis evidence to the owning Work slice |
| A missing regression seam exposes an architecture problem | [Codebase Design](codebase-design.md) | Candidate seam or architecture return to Plan |
| Competing fix directions are semantic | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition to Work |

## Checkpoints

| When | Checkpoint | Allows |
|---|---|---|
| A bounded symptom and usable environment or permission exist | `reproduction_started` | Tight loop, minimized reproduction, hypotheses, and probes |
| Discriminating evidence supports a bounded cause | `diagnosis_returned` | Supported cause, fix scope, regression seam, or declared gap return |

## Operation

1. Redact secrets and irrelevant personal data.
2. At `reproduction_started`, create and run one Tight loop for the exact symptom. If no reproducing environment, captured artifact, or instrumentation permission exists, ask for one instead of hypothesizing.
3. Minimize the input while preserving the failure.
4. Form three to five distinguishable falsifiable hypotheses, each with a prediction. Test one variable at a time; prefer debugger inspection before logs and give temporary logs unique tags.
5. Establish a measurement baseline before performance changes.
6. Identify the smallest cause supported by discriminating evidence. Define a regression seam and test before the fix; if no seam exists, return the architecture finding.
7. Rerun the reproduction, remove temporary instrumentation, remove throwaways, and return the confirmed cause, bounded fix scope, and regression seam at `diagnosis_returned`.

## Returns

| Result | Consumer |
|---|---|
| Supported cause and bounded fix scope | [Work](../work.md) |
| Missing reproducing environment or permission | Human or calling family |
| Missing regression seam | [Plan](../plan.md) or [Codebase Design](codebase-design.md) |
| Competing semantic fix directions | [Review](review.md), then [Decision](decision.md) |
