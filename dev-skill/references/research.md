# research reference

---

**name:** `research`

**description:** Investigate a question against high-trust primary sources and capture the findings as a Markdown file in the repo. Use when the user wants a topic researched or docs/API facts gathered. Isolated-mode hosts may parallelize independent source packets per the mode table; every mode independently verifies primary-source claims and citations before staging the result.

In an isolated-mode UltraWork run, independent primary-source packets may use short-lived subagents followed by a separate citation-verification and synthesis card; otherwise use the host's native research workflow (see the mode table in `dev-skill/SKILL.md`). Do not run research concurrently with an implementation task when both depend on the same mutable seam or when the research result gates that implementation.

Its job:

1. Investigate against **primary sources** — official docs, source code, specs, first-party APIs — not secondary write-ups; follow every claim back to the source that owns it.
2. Write the findings to one Markdown file, citing each claim's source.
3. Save it where the repo already keeps such notes; match the existing convention, or put it somewhere sensible and say where.

---
