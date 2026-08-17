# prototype reference

---

**name:** `prototype`

**description:** Build a throwaway prototype to answer a design question. Use when the user wants to sanity-check whether a state model or logic feels right, or explore what a UI should look like.

# Prototype

A prototype is **throwaway code that answers a question**; the question decides the shape.

## Pick a branch

Identify the question from the prompt, surrounding code, or by asking:

- **"Does this logic / state model feel right?"** → [LOGIC.md](LOGIC.md): a tiny interactive terminal app driving the state machine through cases hard to reason about on paper.
- **"What should this look like?"** → [UI.md](UI.md): preferably one self-contained HTML file with radically different variations on one route, switchable through a visible control — openable directly, no build or server.

The branches produce very different artifacts; picking wrong wastes the prototype. If the question is ambiguous and the user is unreachable, default to whichever matches the surrounding code (backend module → logic; page/component → UI) and state the assumption at the top of the prototype.

## Rules that apply to both

1. **Throwaway from day one, marked as such.** Locate the code near what it prototypes for (context stays obvious) but named so a casual reader sees it's not production. For throwaway UI routes, obey the project's routing convention — invent no new top-level structure.
2. **One command to run** — via the project's existing task runner (`pnpm <name>`, `python <path>`, …). The user starts it without thinking.
3. **No persistence by default.** State lives in memory; persistence is what the prototype is *checking*, not depending on. If the question involves a database, hit a scratch DB or a clearly named "PROTOTYPE — wipe me" local file.
4. **Skip the polish** — no tests, no error handling beyond runnable, no abstractions. Learn fast.
5. **Surface the state**: after every action (logic) or variant switch (UI), print or render the full relevant state.
6. **Capture it when done.** Fold any validated decision into the real code; commit the prototype itself to a throwaway branch (out of main) as a **primary source**, leaving a context pointer on the implementation issue; capture the verdict and its question in the issue or a commit. Main keeps only the validated decision. For a browser/layout question, keep the self-contained HTML runnable and linked from the accepted decision until implementation completes; delete it only after the decision and evidence are preserved in a durable record.

### Reference: prototype/LOGIC.md

# Logic Prototype

A tiny interactive terminal app letting the user drive a state model by hand, for questions about **business logic, state transitions, or data shape** — things that look reasonable on paper but feel wrong once pushed through real cases ("does this state machine handle X then Y", "can this data model represent…", "I want to feel out the API", anything where the user presses buttons and watches state change). For "what should this look like" — wrong branch, use [UI.md](UI.md).

## Process

1. **State the question.** Before code, write the state model and question in one paragraph (README or top-of-file comment). A prototype answering the wrong question is pure waste; the explicit question is checkable later, watched or AFK.
2. **Pick the language** — whatever the host project uses; ask if there's no obvious runtime; match existing tooling, add no new package manager.
3. **Isolate the logic in a portable module.** Put the question-answering logic behind a small, pure interface liftable into the real codebase later — the TUI is throwaway, the module isn't. Shape by question: a pure reducer `(state, action) => state` for discrete events; an explicit state machine when "which actions are legal now" is part of the question; pure functions over a plain type when there's no implicit current state; a class/module with a clear method surface when the logic owns ongoing state. Pick what fits the question, not what's easiest to wire to a TUI. Keep it pure — no I/O, no terminal code, no `console.log` control flow; the TUI imports it, nothing flows back. This is what lifts into the real module when the question is answered.
4. **Build the smallest TUI that exposes the state.** On every tick, clear the screen (`console.clear()` / `print("\033[2J\033[H")` / equivalent) and re-render the whole frame — one stable view, not growing scrollback. Frame order: (1) current state, pretty-printed and diff-friendly (one field per line or formatted JSON; **bold** field names/headers, **dim** minor context like timestamps/IDs/derived values; native ANSI codes `\x1b[1m`/`\x1b[2m`/`\x1b[0m` are fine, no styling library); (2) keyboard shortcuts at the bottom (`[a] add user  [d] delete  [t] tick  [q] quit`), key bold, description dim. Behaviour: initialize one in-memory state object and render the first frame; read one keystroke/line at a time, dispatch to a mutating handler; re-render fully after every action (replace, don't append); loop until quit. The whole frame fits one screen.
5. **One command to run** — add a script to the existing task runner (`package.json`, `Makefile`, `justfile`, `pyproject.toml`); `pnpm run <prototype-name>`, never a remembered path. No task runner: put the command in the README.
6. **Hand it over** — give the run command; the user drives. The interesting moments are "wait, that shouldn't be possible" / "huh, I assumed X would differ" — bugs in the *idea*, the whole point. Add actions on request; prototypes evolve.
7. **Capture the answer and the prototype** per the [SKILL](SKILL.md): the validated reducer/machine/function set lifts into the real module (decision absorbed); the TUI shell rides to the throwaway branch as primary source.

## Anti-patterns

- No tests — a prototype needing tests isn't a prototype.
- No real database — in-memory store unless the question is about persistence.
- No generalizing — no "what if X later"; one question.
- No logic/TUI blur — a reducer referencing `console.log`, prompts, or escape codes is no longer portable; the TUI is a thin shell over a pure module.
- Don't ship the TUI shell — it's optimized for hand-driving; the logic module is the keepable bit.

### Reference: prototype/UI.md

# UI Prototype

**Several radically different UI variations** on a single route, switchable from a floating bottom bar; the user flips between variants in the browser, picks one (or steals bits from each), and throws the rest away. For logic/state questions — wrong branch, use [LOGIC.md](LOGIC.md). Right shape: "what should this page look like", "a few dashboard options before committing", "try a different settings layout" — anytime the user would otherwise spend a day choosing between three vague mental mockups.

## Two sub-shapes — strongly prefer A

Variants are easier to judge **butting against the rest of the app** — real header, sidebar, data, density. A throwaway route alone is a vacuum where every variant looks fine.

- **Sub-shape A — adjust an existing page (default).** The route exists; variants render on it, gated by `?variant=`. Existing data fetching, params, and auth stay — only rendering swaps. Something that doesn't yet have a page but would naturally live inside one (a new dashboard section, settings card, flow step) is still A: mount the variants inside the host page.
- **Sub-shape B — a new page (last resort).** Only for a genuinely homeless prototype (a new top-level surface, an unembeddable flow). Create a throwaway route per the project's routing convention, named obviously as a prototype; same `?variant=` pattern. First sanity-check that no existing page can host it — an empty route hides design problems a populated one would expose.

Both sub-shapes use the identical floating bottom bar.

## Process

1. **State the question and pick N.** Default **3 variants**; cap at 5 — beyond that, difference becomes noise. Write the one-line plan at the prototype's location or top-of-file comment ("Three variants of the settings page, switchable via `?variant=`, on the existing `/settings` route").
2. **Generate radically different variants.** Each honors the page's purpose and data, the project's component/styling system (Tailwind, shadcn, MUI, plain CSS, whatever), and exports a clear name (`VariantA`, `VariantB`, `VariantC`). Variants must be **structurally different** — layout, information hierarchy, primary affordance, not just colors. Three tweaked card grids is wallpaper; if two drafts come out similar, redo one with explicit "do not use a card grid" guidance.
3. **Wire them together** with a single switcher on the route:

```tsx
// pseudo-code — adapt to the project's framework
const variant = searchParams.get('variant') ?? 'A';
return (
  <>
    {variant === 'A' && <VariantA {...data} />}
    {variant === 'B' && <VariantB {...data} />}
    {variant === 'C' && <VariantC {...data} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </>
);
```

Sub-shape A keeps all existing data fetching above the switcher; only the rendered subtree changes. Sub-shape B mounts the same switcher on the throwaway route under `/prototype/<name>`.

4. **Build the floating switcher** — a small fixed bottom-centre bar: left arrow (previous, wraps), variant label (key plus exported name, e.g. `B — Sidebar layout`), right arrow (next, wraps). Clicking updates the URL search param via the framework router (`router.replace` / `navigate`) so variants are shareable and reload-stable; `←`/`→` keys also cycle, never intercepted while an `<input>`, `<textarea>`, or `[contenteditable]` is focused; visually distinct from the page (high-contrast pill, subtle shadow) so it's obviously not part of the evaluated design; hidden in production builds (`process.env.NODE_ENV !== 'production'` or equivalent) so a stray merge can't ship it. One shared component, located wherever shared UI lives.
5. **Hand it over** — surface the URL and variant keys. The telling feedback is usually "header from B with sidebar from C" — that's the actual design they want.
6. **Capture the answer and clean up** per the [SKILL](SKILL.md): fold the winner into real code, move the rest to the throwaway branch — sub-shape A folds the winner into the existing page and drops losing variants plus switcher from main; sub-shape B promotes the winner to a real route and drops the throwaway route and switcher. The full variant set is the primary source (branch, not bin) — leftover variant components rot fast and confuse the next reader.

## Anti-patterns

- Variants differing only in color or copy — that's a tweak; real variants disagree about structure.
- Too much shared code between variants — a shared `<Header>` is fine; a shared `<Layout>` defeats the point; each variant should be free to throw out the layout.
- Wiring variants to real mutations — read-only is fine; if a variant must mutate, point it at a stub. The question is "what should it look like", not "does the backend work".
- Promoting prototype code directly to production — it was written under prototype constraints; rewrite properly when folding in.

---
