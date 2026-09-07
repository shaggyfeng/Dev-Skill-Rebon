# Research

## Trigger and boundary

Run Research when a controlling fact, source model, professional anchor, or path through factual fog is missing. A user naming Research is sufficient; existing useful research notes must be checked first. Ordinary lookup stops once it supplies the needed fact; Research is for a deep source-backed understanding.

Research owns source evidence, knowledge-model construction, Professional Anchor and Positioning, Lexical Uplift, and Pathfinding. It does not choose human values, assess risk, recommend an option, or write final project decisions.

## Note and model structure

Research notes live under `.dev-skill/research/`, one Markdown note per bounded question. A reusable note contains:

| Part | Content |
|---|---|
| Question and source boundary | The exact factual question and sources allowed to answer it |
| Claims and citations | Each claim with the source that owns it |
| Knowledge model | Terms, relations, constraints, and evidence limits needed for the current use |
| Professional Anchor | What competent practice requires for this task, grounded in the model and sources |
| Lexical Uplift | Sourced terminology that improves lookup without replacing the user's wording |
| Pathfinding frontier | Destination, known facts, unknown controlling facts, dependencies, excluded paths, and next evidence question |
| Limits and next use | What remains unsupported and the caller that can use the result |

Positioning locates the relevant discipline, standard, primary record, or applied practice. It does not choose a human value. Structural coverage distinguishes exact support, compatible narrowed support, supporting-only material, absence, conflict, unverified material, and not-applicable material.

## Runtime references

| When | Load | Return or use |
|---|---|---|
| Source alternatives or evidence boundaries create a semantic choice | [Review](review.md), then [Decision](decision.md) | Findings and semantic disposition outside Research |
| Missing information is human experience or value | [Grilling](grilling.md) or [Prototype](prototype.md) | Next human question or bounded experience evidence |
| An eligible Work or Release outcome may add or remove a reusable note | [Review](review.md), then [Decision](decision.md) | Factual disposition before note mutation |

## Operation

1. Check `.dev-skill/research/` first. Reuse a note only when its knowledge points and structure answer the current question.
2. Define the controlling question, source boundary, and the missing fact or model. Prefer primary sources; ask the human to provide a book, paper, or narrower direction when the required source is unavailable.
3. Build a knowledge model: terms, relations, constraints, evidence limits, and a Professional Anchor describing what competent practice requires.
4. Apply Lexical Uplift: use the professional vocabulary needed to search, compare, and name the actual problem without inventing authority.
5. When the task is under fog, Pathfinding maps the visible frontier: destination, known facts, unknown controlling facts, dependencies, excluded paths, and the next evidence question. It supplies a path, not a decision.
6. Use the matching Runtime reference when source alternatives create a semantic choice or human experience or value is missing.
7. Return only the evidence, its limits, the knowledge model, and the next factual frontier to the caller.

## Returns

| Condition | Return |
|---|---|
| Controlling fact and model established | Evidence and knowledge model to caller |
| Factual path remains under fog | Pathfinding frontier to caller |
| Source alternatives need a choice | [Review](review.md), then [Decision](decision.md) |
| Human experience or value is missing | [Grilling](grilling.md) or [Prototype](prototype.md) |
| Required source unavailable | Focused human source or direction request |

## Notes

An eligible completed Work or Release outcome may contribute a reusable research note only through the Review and Decision reference. Notes that no longer support a live knowledge model are removed through that same path.
