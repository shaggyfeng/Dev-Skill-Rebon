# handoff reference

**name:** `handoff`

**description:** Create a redacted, portable handoff for a sequential fresh-context pass.

Use a handoff only when something must travel: a new harness, a new directory, a future session, or a side task that cannot continue in the current context. It is not the default replacement for an ordinary compact or checkpoint.

Write it to the project's approved temporary or handoff directory. Never place authoritative state in the handoff — point to the controller state, append-only journal, accepted plan, ADRs, tickets, tests, and artifacts. Never copy secrets, API keys, tokens, credentials, or unnecessary personal data.

Include:

- destination and user-authorized scope;
- accepted decisions and their source records;
- current packet, dependency status, and exact next action;
- open decisions and the human gate blocking them;
- files changed, tests run, failures, and the next deterministic command;
- suggested skill references for the next pass;
- assumptions that must not be silently promoted to facts.

The next pass reads the handoff, verifies the pointed-to state, and reports any mismatch before acting. A handoff never authorizes a new publication, mutation, or normative decision.
