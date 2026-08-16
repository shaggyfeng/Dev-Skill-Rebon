# implement reference

Preserved detailed guidance from the original consolidated development skill.

---

**name:** `implement`

**description:** "Implement a piece of work based on a spec or set of tickets."

**disable-model-invocation:** true

Implement the work described by the user in the spec or tickets.

Work executes per the mode table in `dev-skill/SKILL.md`: in isolated mode, plan-driven implementation follows [ultrawork-orchestration.md](ultrawork-orchestration.md); in sequential mode, the same stages run one fresh context at a time with handoff-doc packets; in parallel mode, native dispatch with bounded writes and envelopes per card.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, run the /code-review three-axis close-out (Standards · Spec · Correctness) on the work. When the slice touches important code being merged, /hard-code-review also fires per its pre-merge trigger.

Commit only when version control is active and the accepted project workflow authorizes a commit. Otherwise preserve the verified working-tree artifacts and report their exact paths.

---

