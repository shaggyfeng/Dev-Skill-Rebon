# improve-codebase-architecture reference

---

**name:** `improve-codebase-architecture`

**description:** Scan a codebase for deepening opportunities, present them as a visual HTML report, then grill through whichever one you pick.

**disable-model-invocation:** true

# Improve Codebase Architecture

Surface architectural friction and propose **deepening opportunities** — refactors turning shallow modules into deep ones — aiming at testability and AI-navigability. Informed by the project's domain model and built on shared vocabulary:

- Run `/codebase-design` for the architecture vocabulary (**module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, **locality**) and its principles (deletion test, interface-is-the-test-surface, one-adapter-vs-two). Use these terms exactly — no drift into "component", "service", "API", "boundary".
- `CONTEXT.md` domain language names good seams; ADRs in `docs/adr/` record decisions not to re-litigate.

## Process

### 1. Explore

**Scope before you scan — YAGNI.** Deepening pays off by easing future change, so weight recently-changed code. If the user named a direction (module, subsystem, pain point), take it and skip inference; otherwise walk back a stretch of `git log --oneline` to find hot spots — the files that keep coming up — and widen the net if changes are scattered. Read `CONTEXT.md` and area ADRs first.

In an isolated-mode UltraWork run, read-only exploration of disjoint seams may use short-lived subagents plus a synthesis card; otherwise use the host's native exploration. The human retains normative architecture decisions. Explore organically, noting friction:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where were pure functions extracted just for testability while the real bugs hide in how they're called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts are untested, or hard to test through their current interface?

Apply the **deletion test** to anything suspected shallow: deleting it should *concentrate* complexity, not just move it.

### 2. Present candidates as an HTML report

Write a self-contained HTML file to the OS temp directory (resolve from `$TMPDIR`, fall back to `/tmp` / `%TEMP%`), named `<tmpdir>/architecture-review-<timestamp>.html` so each run is fresh. Open it (`xdg-open` / `open` / `start`) and tell the user the absolute path. Nothing lands in the repo.

**Tailwind via CDN** for layout, **Mermaid via CDN** for graph-shaped diagrams (call graphs, dependencies, sequences), hand-built divs/SVG for editorial visuals (mass diagrams, cross-sections, collapse animations). Each candidate gets a **before/after visualisation** — be visual. Per candidate, render a card with: **Files** involved; **Problem** (why the current architecture causes friction); **Solution** (plain English, what changes); **Benefits** (in locality and leverage terms, and how tests improve); **Before/After diagram** (side-by-side, custom-drawn, shallowness → deepening); **Recommendation strength** badge — `Strong` / `Worth exploring` / `Speculative`. End with a **Top recommendation** section: which candidate first, and why.

Use CONTEXT.md vocabulary for the domain and `/codebase-design` vocabulary for architecture — "the Order intake module", not "the FooBarHandler" or "the Order service". **ADR conflicts**: surface a contradicting candidate only when the friction warrants revisiting the ADR, marked clearly ("contradicts ADR-0007 — but worth reopening because…"); don't list every theoretically-forbidden refactor.

See [HTML-REPORT.md](HTML-REPORT.md) for the scaffold, diagram patterns, and styling. Do NOT propose interfaces yet. After writing, ask: "Which of these would you like to explore?"

### 3. Grilling loop

Once the user picks a candidate, run `/grilling` to walk the decision tree — constraints, dependencies, the deepened module's shape, what sits behind the seam, which tests survive. Side effects inline as decisions crystallize, via `/domain-modeling`:

- Naming a deepened module after a concept not in `CONTEXT.md`? Add the term (create the file lazily).
- Sharpening a fuzzy term mid-conversation? Update `CONTEXT.md` right there.
- User rejects the candidate with a load-bearing reason? Offer an ADR — *"Want me to record this as an ADR so future reviews don't re-suggest it?"* — only when a future explorer would need the reason; skip ephemeral ("not worth it right now") and self-evident ones.
- Exploring alternative interfaces? Run `/codebase-design`'s sequential design-pass pattern.

### Reference: improve-codebase-architecture/HTML-REPORT.md

# HTML Report Format

One self-contained HTML file in the OS temp directory; Tailwind and Mermaid from CDNs. Mermaid for graph-shaped relationships; hand-built divs and inline SVG for editorial visuals. Mix the two — leaning on Mermaid for everything looks generic.

## Scaffold

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review — {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer: dashed seam lines, hand-drawn arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

**Header:** repo name, date, compact legend (solid box = module, dashed = seam, red arrow = leakage, thick dark box = deep module). No intro paragraph — straight into candidates.

**Candidate card** — diagrams carry the weight; prose is sparse, plain, glossary-exact. Each candidate is one `<article>`: short **Title** naming the deepening ("Collapse the Order intake pipeline"); **Badge row** (strength: `Strong` emerald / `Worth exploring` amber / `Speculative` slate, plus dependency-category tag: `in-process` / `local-substitutable` / `ports & adapters` / `mock`); **Files** in `font-mono text-sm`; **Before/After diagram** — the centrepiece, two columns side by side; **Problem** and **Solution** — one sentence each; **Wins** — bullets ≤6 words ("Tests hit one interface", "Pricing logic stops leaking", "Delete 4 shallow wrappers"); optional one-line amber **ADR callout**. No explanation paragraphs — if a diagram needs one, redraw the diagram.

## Diagram patterns — pick what fits, mix them, vary the look

- **Mermaid graph** (workhorse for dependencies/call flow): `flowchart`/`graph` when the point is "X calls Y calls Z, and look at the mess"; wrap in a Tailwind card; `classDef` colours leakage edges red and the deep module dark; sequence diagrams for "before: 6 round-trips; after: 1".

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

- **Hand-built boxes-and-arrows** (when Mermaid's layout fights you): modules as bordered `<div>`s, arrows as absolutely-positioned inline SVG `<line>`/`<path>` — the way to get an "after" of one thick-bordered deep module with greyed internals.
- **Cross-section** (layered shallowness): stacked horizontal bands (`h-12 border-l-4`); before = 6 thin layers each doing nothing, after = 1 thick band with the consolidated responsibility.
- **Mass diagram** (interface as wide as implementation): two rectangles per module — interface surface vs implementation; before = nearly equal heights (shallow), after = short interface, tall implementation (deep).
- **Call-graph collapse**: before = nested-boxes call tree; after = the tree collapsed into one box with now-internal calls faded inside.

## Style guidance

Lean editorial, not corporate-dashboard; generous whitespace; serif optional for headings. Colour sparingly: one accent (emerald or indigo), red for leakage, amber for warnings. Diagrams ~320px tall so before/after sits side by side unscrolled. Module labels `text-xs uppercase tracking-wider` — schematic, not UI. Only scripts: Tailwind CDN and the Mermaid ESM import; otherwise static. **Top recommendation**: one larger card — candidate name, one sentence why, anchor link.

## Tone

Plain, concise; architectural nouns and verbs straight from `/codebase-design`. **Use exactly:** module, interface, implementation, depth, deep, shallow, seam, adapter, leverage, locality. **Never substitute:** component, service, unit (module) · API, signature (interface) · boundary (seam) · layer, wrapper (module). Fitting phrasings: "Order intake module is shallow — interface nearly matches the implementation" · "Pricing leaks across the seam" · "Deepen: one interface, one place to test" · "Two adapters justify the seam: HTTP in prod, in-memory in tests". Wins bullets name the gain in glossary terms ("locality: bugs concentrate in one module", "leverage: one interface, N call sites") — never "easier to maintain" or "cleaner code". No hedging, no throat-clearing; if a sentence could be a bullet, make it one; if a bullet could be cut, cut it.

---
