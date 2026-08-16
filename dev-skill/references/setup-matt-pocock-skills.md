# setup-matt-pocock-skills reference

---

**name:** `setup-matt-pocock-skills`

**description:** Configure this repo for the engineering skills — set up its issue tracker, triage label vocabulary, and domain doc layout. Run once before first use of the other engineering skills.

**disable-model-invocation:** true

# Setup Matt Pocock's Skills

Scaffold the per-repo configuration the engineering skills assume: **issue tracker** (GitHub default; local markdown supported out of the box), **triage labels** (strings for the canonical triage roles), and **domain docs** (where `CONTEXT.md` and ADRs live, plus consumer rules). A prompt-driven skill, not a deterministic script: explore, present, confirm, write.

## Process

### 1. Explore

Read the repo's starting state; don't assume:

- `git remote -v` and `.git/config` — GitHub? GitLab? Which repo?
- `AGENTS.md` / `CLAUDE.md` at the root — either exist? An `## Agent skills` section already there?
- `CONTEXT.md` and `CONTEXT-MAP.md` at the root; `docs/adr/` and any `src/*/docs/adr/`
- `docs/agents/` — prior output of this skill?
- `.scratch/` — a local-markdown tracker convention already in use?
- Is the `triage` skill installed (a `triage` folder alongside, or in available skills)? Decides whether Section B runs.
- Monorepo signals — `pnpm-workspace.yaml`, `workspaces` in `package.json`, populated `packages/*` with own `src/`. Present only in a genuinely large multi-package repo; absence means single-context (almost every repo).

### 2. Present findings and ask

Summarise present vs missing, then take sections in order — one section, one answer. Lead each with the recommended answer (acceptable in a word); give a one-line explainer only when the choice genuinely branches; skip a section exploration already settled (B without `triage`, C without monorepo signals).

**Section A — Issue tracker.** The tracker is where issues live; `to-tickets`, `triage`, and `to-spec` read and write it. Default posture: designed for GitHub — a GitHub remote proposes GitHub, a GitLab remote proposes GitLab; otherwise (or by preference) offer **GitHub** (Issues via `gh`), **GitLab** (Issues via [`glab`](https://gitlab.com/gitlab-org/cli)), **Local markdown** (files under `.scratch/<feature>/` — good for solo or remote-less repos), or **Other** (Jira, Linear, … — user describes the workflow in one paragraph, recorded as freeform prose). Record the choice in `docs/agents/issue-tracker.md`. The GitHub/GitLab templates carry a "PRs as a request surface" flag defaulted **off** — leave it off and don't raise it; a user wanting external PRs in the triage queue can flip it later.

**Section B — Triage label vocabulary.** Skip entirely if `triage` isn't installed. Otherwise one question: "Keep the default triage labels? (recommended: **yes**)". Defaults are the canonical roles with label strings equal to their names: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. On yes, write as-is; on no (tracker already uses other names, e.g. `bug:triage`), collect the overrides so `triage` applies existing labels instead of duplicating.

**Section C — Domain docs.** Default **single-context** (root `CONTEXT.md` + `docs/adr/`) — fits almost every repo; write without asking. Offer **multi-context** (root `CONTEXT-MAP.md` pointing to per-context `CONTEXT.md` files) only on monorepo signals, then confirm the layout.

### 3. Confirm and edit

Show drafts of: the `## Agent skills` block for the chosen `CLAUDE.md`/`AGENTS.md`; and the contents of `docs/agents/issue-tracker.md`, `domain.md`, and `triage-labels.md` (last only when `triage` is installed). Let the user edit before writing.

### 4. Write

File selection: edit `CLAUDE.md` if it exists, else `AGENTS.md`; if neither exists, ask which to create — never pick, and never create the other when one already exists. Update an existing `## Agent skills` block in place; don't overwrite surrounding user edits.

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout — "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

Include the `### Triage labels` sub-block (and write `docs/agents/triage-labels.md`) only when `triage` is installed and Section B ran. Write the docs files from this skill's seed templates — [issue-tracker-github.md](./issue-tracker-github.md), [issue-tracker-gitlab.md](./issue-tracker-gitlab.md), [issue-tracker-local.md](./issue-tracker-local.md), [triage-labels.md](./triage-labels.md), [domain.md](./domain.md); for "other" trackers, write `docs/agents/issue-tracker.md` from scratch from the user's description.

### 5. Done

Tell the user setup is complete and which skills now read these files; they can edit `docs/agents/*.md` directly later — re-run only to switch trackers or restart from scratch.

### Reference: setup-matt-pocock-skills/domain.md

# Domain Docs

How the engineering skills consume this repo's domain documentation.

**Before exploring, read:** `CONTEXT.md` at the root (or `CONTEXT-MAP.md` if it exists — it points at one `CONTEXT.md` per context; read each relevant one), and `docs/adr/` ADRs touching the area (in multi-context repos, also `src/<context>/docs/adr/`). If these files don't exist, **proceed silently** — don't flag absence or suggest creating them; `/domain-modeling` (reached via `/grilling`'s with-docs instruction and `/improve-codebase-architecture`) creates them lazily.

**File structure.** Single context (most repos): root `CONTEXT.md`, `docs/adr/` (e.g. `0001-event-sourced-orders.md`), `src/`. Multi-context (root `CONTEXT-MAP.md`): system-wide decisions in root `docs/adr/`; each `src/<context>/` carries its own `CONTEXT.md` and `docs/adr/`.

**Use the glossary's vocabulary.** When output names a domain concept (issue title, refactor proposal, hypothesis, test name), use the term as defined in `CONTEXT.md` — don't drift to synonyms the glossary avoids. A concept missing from the glossary is a signal: either you're inventing language the project doesn't use (reconsider), or there's a real gap (note it for `/domain-modeling`).

**Flag ADR conflicts** explicitly rather than silently overriding: _"Contradicts ADR-0007 (event-sourced orders) — but worth reopening because…"_

### Reference: setup-matt-pocock-skills/issue-tracker-github.md

# Issue tracker: GitHub

Issues and PRDs for this repo live as GitHub issues. Use the `gh` CLI for all operations.

## Conventions

- **Create an issue**: `gh issue create --title "..." --body "..."`. Heredoc for multi-line bodies.
- **Read an issue**: `gh issue view <number> --comments`, filtering comments by `jq` and also fetching labels.
- **List issues**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` with appropriate `--label` and `--state` filters.
- **Comment**: `gh issue comment <number> --body "..."`
- **Labels**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

Infer the repo from `git remote -v` — `gh` does this automatically inside a clone.

## Pull requests as a triage surface

**PRs as a request surface: no.** _(Set to `yes` if this repo treats external PRs as feature requests; `/triage` reads this flag.)_ When `yes`, PRs run through the same labels/states via the `gh pr` equivalents: **read** — `gh pr view <number> --comments` plus `gh pr diff <number>`; **list external** — `gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`, keeping only `authorAssociation` `CONTRIBUTOR` / `FIRST_TIME_CONTRIBUTOR` / `NONE` (drop `OWNER`/`MEMBER`/`COLLABORATOR`); **comment/label/close** — `gh pr comment`, `gh pr edit --add-label`/`--remove-label`, `gh pr close`. GitHub shares one number space across issues and PRs — resolve a bare `#42` with `gh pr view 42`, falling back to `gh issue view 42`.

## When a skill says "publish to the issue tracker"

Create a GitHub issue.

## When a skill says "fetch the relevant ticket"

Run `gh issue view <number> --comments`.

## Wayfinding operations

Used by `/grilling` (path-finder instruction). The **map** is a single issue with **child** issues as tickets.

- **Map**: a single issue labelled `wayfinder:map`, holding the Notes / Decisions-so-far / Fog body. `gh issue create --label wayfinder:map`.
- **Child ticket**: an issue linked to the map as a GitHub sub-issue (`gh api` on the sub-issues endpoint). Where sub-issues aren't enabled, add the child to a task list in the map body and put `Part of #<map>` at the top of the child body. Labels: `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Once claimed, assigned to the driving dev.
- **Blocking**: GitHub's **native issue dependencies** — the canonical, UI-visible representation. Add an edge with `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`, where `<blocker-db-id>` is the blocker's numeric **database id** (`gh api repos/<owner>/<repo>/issues/<n> --jq .id`, _not_ the `#number` or `node_id`). GitHub reports `issue_dependencies_summary.blocked_by` (open blockers only — the live gate). Without dependencies, fall back to a `Blocked by: #<n>, #<n>` line at the top of the child body. Unblocked = every blocker closed.
- **Frontier query**: list the map's open children (`gh issue list --state open`, scoped to the map's sub-issues / task list), drop any with an open blocker (`issue_dependencies_summary.blocked_by > 0`, or an open issue in the `Blocked by` line) or an assignee; first in map order wins.
- **Claim**: `gh issue edit <n> --add-assignee @me` — the session's first write.
- **Resolve**: `gh issue comment <n> --body "<answer>"`, then `gh issue close <n>`, then append a context pointer (gist + link) to the map's Decisions-so-far.

### Reference: setup-matt-pocock-skills/issue-tracker-gitlab.md

# Issue tracker: GitLab

Issues and PRDs for this repo live as GitLab issues. Use the [`glab`](https://gitlab.com/gitlab-org/cli) CLI for all operations.

## Conventions

- **Create**: `glab issue create --title "..." --description "..."` (heredoc for multi-line; `--description -` opens an editor).
- **Read**: `glab issue view <number> --comments`; `-F json` for machine-readable output.
- **List**: `glab issue list -F json` with `--label` filters.
- **Comment**: `glab issue note <number> --message "..."` — GitLab calls comments "notes".
- **Labels**: `glab issue update <number> --label "..."` / `--unlabel "..."` (comma-separated or repeated).
- **Close**: `glab issue close` takes no closing comment — post the explanation first via `glab issue note`, then close.
- **Merge requests**: PRs are "merge requests" — `glab mr create` / `view` / `note`, same shape as `gh pr ...` with `mr` and `note`/`--message`.

Infer the repo from `git remote -v` — `glab` does this automatically inside a clone.

## Merge requests as a triage surface

**MRs as a request surface: no.** _(Set to `yes` if this repo treats external merge requests as feature requests; `/triage` reads this flag.)_ When `yes`, MRs run the same labels/states via `glab mr` equivalents: **read** — `glab mr view <number> --comments` plus `glab mr diff <number>`; **list external** — `glab mr list -F json`, keeping only MRs whose author is not a project member/owner; **comment/label/close** — `glab mr note`, `glab mr update --label`/`--unlabel`, `glab mr close`. Unlike GitHub, GitLab numbers issues and MRs separately — `#42` is unambiguous once you know which surface is meant.

## When a skill says "publish to the issue tracker"

Create a GitLab issue.

## When a skill says "fetch the relevant ticket"

Run `glab issue view <number> --comments`.

## Wayfinding operations

Used by `/grilling` (path-finder instruction). The **map** is a single issue with **child** issues as tickets.

- **Map**: a single issue labelled `wayfinder:map` holding the Notes / Decisions-so-far / Fog body — `glab issue create --label wayfinder:map`. (On tiers with native epics, an epic may hold the map; a labelled issue works everywhere.)
- **Child ticket**: an issue with `Part of #<map>` at the top of its description and labels `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Once claimed, assigned to the driving dev.
- **Blocking**: GitLab's **native blocking link** — canonical and UI-visible — via the `/blocked_by #<n>` quick action posted as a note (`glab issue note <child> --message "/blocked_by #<blocker>"`). Native links are Premium/Ultimate; on free tiers fall back to a `Blocked by: #<n>, #<n>` description line. Unblocked = every blocker closed.
- **Frontier query**: `glab issue list -F json` scoped to the map's children, drop any with an open blocker (a native `blocked_by` link to an open issue — `glab api projects/:id/issues/:iid/links` — or an open issue in the `Blocked by` line) or an assignee; first in map order wins.
- **Claim**: `glab issue update <n> --assignee @me` — the session's first write.
- **Resolve**: `glab issue note <n> --message "<answer>"`, then `glab issue close <n>`, then append a context pointer (gist + link) to the map's Decisions-so-far.

### Reference: setup-matt-pocock-skills/issue-tracker-local.md

# Issue tracker: Local Markdown

Issues and specs (PRDs) for this repo live as markdown files in `.scratch/`.

## Conventions

- One feature per directory: `.scratch/<feature-slug>/`
- The spec is `.scratch/<feature-slug>/spec.md`
- Implementation issues: one file per ticket at `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` — never a single combined tickets file
- Triage state is a `Status:` line near the top of each issue file (see `triage-labels.md` for the role strings)
- Comments and conversation history append under a `## Comments` heading at the bottom of the file

## When a skill says "publish to the issue tracker"

Create a new file under `.scratch/<feature-slug>/` (creating the directory if needed).

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path; the user normally passes the path or issue number directly.

## Wayfinding operations

Used by `/grilling` (path-finder instruction). The **map** is a file with one **child** file per ticket.

- **Map**: `.scratch/<effort>/map.md` — the Notes / Decisions-so-far / Fog body.
- **Child ticket**: `.scratch/<effort>/issues/NN-<slug>.md`, numbered from `01`, question in the body. `Type:` records the ticket type (`research`/`prototype`/`grilling`/`task`); `Status:` records `claimed`/`resolved`.
- **Blocking**: a `Blocked by: NN, NN` line near the top; unblocked = every listed file `resolved`.
- **Frontier**: scan `.scratch/<effort>/issues/` for files that are open, unblocked, unclaimed; first by number wins.
- **Claim**: set `Status: claimed` and save before any work.
- **Resolve**: append the answer under `## Answer`, set `Status: resolved`, append a context pointer (gist + link) to the map's Decisions-so-far in `map.md`.

### Reference: setup-matt-pocock-skills/triage-labels.md

# Triage Labels

The issue-tracker triage state machine was archived with the dev-skill rebuild (see `.scratch/dev-skill-pre-rebuild-archive/triage.md`); `triage.md` is now the brownfield maintenance survey and does not drive these states. The label mapping below remains available as a tracker convention for projects that keep a manual issue-triage flow.

| Label in mattpocock/skills | Label in our tracker | Meaning |
| --- | --- | --- |
| `needs-triage` | `needs-triage` | Maintainer needs to evaluate this issue |
| `needs-info` | `needs-info` | Waiting on reporter for more information |
| `ready-for-agent` | `ready-for-agent` | Fully specified, ready for an AFK agent |
| `ready-for-human` | `ready-for-human` | Requires human implementation |
| `wontfix` | `wontfix` | Will not be actioned |

When a skill mentions a role (e.g. "apply the AFK-ready triage label"), use the corresponding label string. Edit the right-hand column to match your vocabulary.
