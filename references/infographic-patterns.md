# Infographic Patterns — visualization catalog + slop/non-slop contrast gallery

> This file **educates the Generator by example**. It is read by the Generator (to pick a fitting visual
> form for each concept) and by the Evaluator (to judge "generic-anywhere vs topic-specific" in the
> Anti-Slop probe). The goal of the catalog is to make a *text paragraph the fallback, not the default*:
> for every concept, the Generator first asks "which of these forms carries this idea best?"
>
> All visuals are built **inline** (HTML / CSS / SVG / Canvas / JS) in ONE self-contained file. CDN is
> allowed (e.g. a charting lib) but no build step. Generated raster images are allowed only when inline
> vector/markup genuinely cannot express the concept (e.g. a photographic reference).

---

## Part A — Visualization-type catalog

Pick the form by what the concept *is*, not by what is easy. Prefer variety: do not let one form
(especially the card-box) repeat across most sections.

| Type | The concept shape it fits | Inline build approach | Example use |
|------|---------------------------|-----------------------|-------------|
| **Mind map / concept map** | A central idea that branches into sub-ideas; "when to use / what relates to what" | SVG nodes + connector paths, or nested CSS flex with connector lines | "All the places `useReducer` shows up" radiating from a center node |
| **Flowchart / decision tree** | A process with steps and branches; "if X then Y" | SVG boxes + arrowheads, or HTML boxes with CSS-drawn connectors; JS to highlight the active path | A dispatch flow: `action → reducer → next state` with a branch on action type |
| **State / node graph** | A finite set of states and the transitions between them | SVG nodes + directed edges; **make it interactive** — click a node, the graph animates the transition it teaches | A state machine where clicking "submit" visibly moves the active state |
| **Chart (bar / line / area / scatter)** | Quantitative comparison or trend over a dimension | Inline `<svg>` plotting, `<canvas>`, or a CDN chart lib (Chart.js / lightweight). Label axes. | Render-count before vs after memoization; latency across input sizes |
| **Comparison matrix / 2×2** | A tradeoff along two axes, or feature-by-option comparison | CSS grid table with axis labels; for a tradeoff use an actual 2×2 quadrant with the options plotted | "Sync vs async × simple vs complex" with each approach placed in a quadrant |
| **Comparison table** | Several options across several attributes | Semantic `<table>` with a clear visual rhythm (zebra by meaning, not decoration); highlight the recommended row | `useState` vs `useReducer` vs external store across 5 attributes |
| **Schematic / annotated diagram** | The internal structure of a thing; "what's inside and how it's wired" | SVG with labeled callout lines pointing to parts | Anatomy of a reducer function: its `(state, action)` inputs labeled, its return labeled |
| **Step-by-step / sequence** | An ordered walkthrough where order matters | Numbered horizontal or vertical timeline; each step its own panel; optional "next" interaction | A worked example traced one step at a time |
| **Webtoon / comic narrative** | A cause→effect story that benefits from characters/scenes | A panel strip (CSS grid of panels), each panel an SVG/illustrated scene with a speech line; keep it on-topic, not decorative | "The action knocks on the reducer's door → reducer computes → hands back a brand-new state object" as 3 panels |
| **Worked example / live playground** | A concept best learned by manipulating it | An actual interactive widget: input → JS computes → output updates live; the reader changes a value and sees the concept move | A cart-quantity input that dispatches and shows the resulting state object update in real time |
| **Invented topic-specific visualization** | A concept with no off-the-shelf form — invent one that *only makes sense for this concept* | Whatever inline tech fits; this is the C1 5/5 move | Recursion drawn as a nested self-similar SVG that visibly contains a smaller copy of itself |

**Density targets the Generator must hit** (defaults; the Planner may adjust in spec.md with rationale):
- **N ≥ 6** distinct visualizations in the document.
- **M ≥ 4** distinct visualization *types* (e.g. flowchart + chart + 2×2 + node-graph = 4 types).
- **Every major section ≥ 1 visual.** A text-only section needs a stated exception reason in spec.md /
  generator_report.md, or the Evaluator flags it.

**Audience tuning of complexity:** a *beginner* audience gets fewer simultaneous elements per visual, one idea
per visual, and analogies from their business domain; an *expert* audience can take denser schematics, more
edges in a graph, and less hand-holding. The visual *type* serves the concept; the visual *density* serves the level.

---

## Part B — Slop vs non-slop contrast gallery (educates by example)

The Anti-Slop criterion (C3) is judged by **contrast**, not a banned-word checklist. The question is always:
**"Strip the topic words away — could this exact design be dropped onto any other topic unchanged?"**
If yes, it is slop (generic-anywhere). If the design *encodes something about this specific subject*, it is non-slop.

Below, each pair shows the slop default Claude reaches for and the topic-specific alternative that defeats it.

### 1. The hero section
- **SLOP** — a full-width purple→indigo gradient banner, a giant centered headline, a generic "Get started"
  vibe, maybe a floating decorative blob. Could be a SaaS landing page for anything.
- **NON-SLOP** — a header that *is* a small instance of the topic. For a state-machine explainer, the header
  is the state graph itself at rest, with the title set in its empty space. The opening visual already teaches.

### 2. Section bullets
- **SLOP** — 🚀 emoji bullets, ✅ / ⚡ / 💡 sprinkled as decoration in front of list items.
- **NON-SLOP** — a real list rhythm: numbered steps where order matters, or a small inline icon *drawn from the
  topic's own schematic* (the reducer glyph, the node shape) so the marker carries meaning, not mood.

### 3. The "three feature cards" row
- **SLOP** — three identical rounded cards in a row, each an emoji + a bold title + two lines of grey text,
  evenly spaced. The single most common AI-slop tell. Could describe any product's "features."
- **NON-SLOP** — if there really are three things to compare, use a **comparison matrix or 2×2** that places them
  *in relation to each other* (so the layout itself says something), not three interchangeable boxes. If they are
  steps, use a sequence. The structure should encode the relationship, not flatten it into equal cards.

### 4. Color palette
- **SLOP** — the default purple/indigo/violet gradient, or a generic dark-mode + neon-cyan accent, applied with
  no reason rooted in the subject.
- **NON-SLOP** — a palette derived from the topic. A database explainer might use a cool ink-on-paper schematic
  palette; a thermodynamics explainer a heat-map gradient that *is* the concept; a finance explainer the muted
  green/red of ledgers. The color means something here.

### 5. Decoration
- **SLOP** — floating gradient blobs, abstract swooshes, glassmorphism cards, particles — decoration that carries
  zero information and would suit any page.
- **NON-SLOP** — every visual element does pedagogical work. Whitespace and a tidy schematic *are* the aesthetic;
  there is no purely decorative layer. If an element teaches nothing, it is cut.

### 6. Typography
- **SLOP** — one default sans at one size for everything, no scale, headings barely larger than body.
- **NON-SLOP** — a deliberate type scale that builds hierarchy and a scan path; code in a real mono; labels and
  annotations sized to read as annotations. Type does hierarchy work, not just text-display work.

### 7. Explaining a process
- **SLOP** — three paragraphs of prose describing the steps, maybe an ordered list.
- **NON-SLOP** — an actual flowchart or animated sequence where the steps are *spatial* and you can follow the
  arrows; the prose, if any, supports the diagram rather than replacing it.

**How the Generator uses this gallery:** before submitting, re-read Part B and check each section against the
slop side. If any section matches a SLOP description, convert it to its NON-SLOP form. **How the Evaluator uses
it:** in the Anti-Slop probe, name which contrast pair a flagged region matches (e.g. "matches pair 3, the
identical-three-cards slop"), with a screenshot region or DOM snippet as evidence.

---

## Part C — Visual-detail rules (a connector that is drawn must be SEEN)

A correct concept rendered with a broken detail still fails the reader. The most common detail failure in these
docs is an **occluded or clipped connector**: a mind-map edge, a flowchart arrow, a sequence lifeline, or a
between-card "연동 / →" label that is **hidden behind the very cards it links** (drawn under a sibling, clipped by
an `overflow:hidden` parent, or buried by a wrong stacking order). If you draw a relationship, the relationship
must be visible end to end — arrowhead included. Honor these rules and verify them on the rendered page, not in
the markup:

1. **Draw order / stacking.** In SVG, later elements paint on top — draw connector paths and arrowheads **after**
   the node/card rects (or in a dedicated overlay `<g>` appended last), never before. In HTML/CSS, give the
   connector a `z-index` (and a positioned context) **above** the cards, or route it through the gap between
   them; do not let a card's background plate cover it.
2. **Reserve the gap.** Horizontally/vertically adjacent cards that have a connector or label between them need a
   real gap sized for it. A connector squeezed into a 2px seam, or a label set with a negative margin that tucks
   under the next card, reads as "missing." Size the gap to the connector, not the connector to the leftover.
3. **Mind the clip.** `overflow:hidden` on a card/figure (often added for rounded corners) will amputate any
   connector, badge, arrowhead, or tooltip that extends past the card edge. Put the connector OUTSIDE the clipped
   box, or remove the clip on the element that must bleed.
4. **No silent overlap.** Labels, badges, stamps, legends, and captions must not sit on top of text or each other
   in a way that hides either. Two things in the same pixels = at least one is unreadable.
5. **Edges reach their endpoints.** An arrow must visibly touch (or clearly point at) both the source and the
   target, with its arrowhead intact — not stop short, not vanish under a node, not overshoot off-canvas.
6. **Anchor labels unambiguously.** A label riding a connector (a "연동", "→", or relationship caption between two
   cards) must read as clearly attached to ONE thing. Either center it ON the connector at the line's midpoint —
   with an opaque backing chip/plate so the line emerges cleanly from BOTH sides of the label — or pin it to a
   node's edge/centre. Do NOT leave it floating at an in-between offset (a few px above the line, halfway down a
   card's body) where the reader cannot tell whether it belongs to the line or to the card. When a label and its
   connector share a gap, align both to the same centre line so the attachment is obvious at a glance. A label
   that is *visible* but *ambiguously anchored* still fails the reader — they read the picture but misread the
   relationship.

**How the Generator uses Part C:** after building any visual with connectors/overlays (mind map, flowchart,
node graph, sequence, schematic, between-card labels), open the RENDERED page at desktop AND mobile widths and
confirm every connector is fully visible end-to-end. This is part of the step-f self-evaluation, not an optional
polish. **How the Evaluator uses Part C:** the visual-detail check in the technical-integrity probe (and C2
Visual Scannability) flags any occluded/clipped/overlapping connector or label from the screenshots, citing the
region.
