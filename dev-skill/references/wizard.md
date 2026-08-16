# wizard reference

**name:** `wizard`

**description:** Guide a human through a manual procedure that the agent cannot perform itself, such as a credential setup, unfamiliar dashboard, one-off migration, or irreversible cutover.

A wizard is a temporary, stage-by-stage procedure, used only for actions requiring the human's clicks, account access, legal choice, or physical confirmation. If the project's governed controller or an admitted local tool can safely perform a step, use that instead.

## Process

1. **Scope the procedure.** Read the repository; list every manual stage, captured value, destination, secrecy class, and irreversible action. Show the ordered stage list to the human before writing it.
2. **Map each stage.** Concrete UI paths, URLs, commands, or copy locations. Verify unknown third-party details from primary documentation; never invent dashboard steps.
3. **Author a temporary script or checklist** in the repository's native platform. Each stage: one focused task, a confirmation gate, an explicit output, an idempotent write location. Hidden input for secrets; never write secrets to logs or append-only project records.
4. **Verify and hand off.** Run syntax/static checks without performing irreversible actions; trace that every stage-1 value is captured at its declared destination. Tell the human how to run it and what evidence to return.

## Project adaptation

In this Windows/Python project, prefer a small PowerShell or Python wizard over an unportable Bash-only script. Any state mutation still goes through the Python Controller; a wizard may prepare a bounded input or guide a human, but cannot publish templates, authorize execution, or write governed artifacts directly. Keep the wizard temporary unless the human explicitly requests a repeatable, documented setup path.
