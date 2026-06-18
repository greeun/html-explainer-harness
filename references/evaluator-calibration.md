# Evaluator Calibration — html-explainer-harness

> Few-shot score anchors (V1-5, V1-8). Concrete **1 / 3 / 5** anchors per criterion. The Evaluator reads this
> file before scoring and aligns its scores to these anchors to prevent score drift. Article:
> "calibrating the evaluator using few-shot examples with detailed score breakdowns ... reduced score drift
> across iterations."
>
> **Running example topic:** a single-file HTML explainer of **React `useReducer`** for a **junior frontend
> developer, commerce business domain, beginner level**. The anchors describe what a 1/3/5 looks like for
> *this* doc; apply the same *judgment logic* to any other topic/audience (the anchor is an example of the
> logic, not specific to React).
>
> **Anchor density (intentional asymmetry):** the three 2×-weighted weak axes (C1, C2, C3) get **two** worked
> examples (A and B) per score level, because score drift is largest on the axes Claude is weak at. The two
> 1×-weighted axes (C4, C5) get at least one concrete example per level. Every criterion has anchors at 1, 3,
> and 5.
>
> **When tuning:** if a real run reveals a case the Evaluator missed, add a counter-example to that criterion's
> anchors at the appropriate level (P-3 step c).

---

## C1 — Infographic Richness · 2×

Are concepts converted into varied, fitting, topic-specific visuals — or stacked as text?

**1/5 — prose with one bullet list; concepts never become diagrams**
- Example A: the entire `useReducer` explanation is `<p>` paragraphs. "A reducer is a function that takes state
  and an action and returns new state" is stated in text; there is no diagram of the `(state, action) → state`
  shape anywhere. One `<ul>` lists three use cases. Zero visualizations.
- Example B: there is technically one chart (a bar chart of "lines of code"), but every other section — dispatch
  flow, state transitions, when-to-use — is prose. N = 1, far below N ≥ 6; M = 1.

**3/5 — a few visuals but low variety; the same form repeats**
- Example A: there is a flowchart for dispatch and a comparison table for `useState` vs `useReducer`, but the
  remaining four sections all use the **same rounded-card-box** layout to hold paragraphs. N ≈ 4 but M ≈ 2; the
  card-box is doing work a real visualization should do.
- Example B: six visuals exist (meets N) but five of them are bar charts of slightly different numbers — variety
  is thin (M ≈ 2), and the state-transition concept, which begs for a node graph, is shown as another bar chart.

**5/5 — each concept gets a fitting, distinct visual incl. one invented form**
- Example A: the state-transition concept is an **interactive node graph** (click "addToCart" and the active
  state node visibly moves); dispatch is a **flowchart** with an animated active path; the `useState`-vs-`useReducer`
  tradeoff is a **2×2 matrix** (simple↔complex × few↔many state transitions) with each approach plotted; "action →
  reducer → new state" is a **3-panel webtoon**; a **live cart playground** lets the reader change quantity and watch
  the state object update; "when to reach for it" is a **mind map**. N = 6, M = 6.
- Example B: includes an **invented topic-specific visualization** — the reducer's purity is shown as a sealed
  "function box" SVG where the same inputs visibly always drop out the same output, a device that only makes sense
  for *this* concept (reducer purity). Variety and fit are both high.

---

## C2 — Visual Scannability · 2×

Can the doc be grasped at a glance — hierarchy, scan path, whitespace, tidy visuals?

**1/5 — a wall of same-size text; no hierarchy; visuals cluttered**
- Example A: headings are the same size as body text, no spacing between sections, the node graph's labels
  overlap its edges, and two diagrams collide because neither has margin. The eye has no entry point.
- Example B: everything is centered and full-width with no max line length; the reader scrolls through an
  undifferentiated column and cannot tell where one concept ends and the next begins.

**3/5 — some hierarchy and spacing, but the scan path is unclear and visuals are cramped**
- Example A: headings exist and are larger, and there is some vertical spacing, but the flowchart is squeezed into
  a narrow column so its arrows bend awkwardly, and the comparison table has no visual emphasis on the recommended
  row. A reader can navigate but has to work.
- Example C (ambiguous connector label): a between-card "연동 / →" label is fully visible (not occluded) but sits at
  an in-between vertical offset — a few px above the connector line, halfway down the card body — so the reader can't
  tell whether it labels the line or floats over a card. Visible but ambiguously anchored (Part C rule 6) → a C2
  deduction, not a 5; the fix is to centre it ON the line with a backing chip or pin it to a node.
  **Detection note:** a ~5px offset is invisible in a full-page screenshot — it is only catchable from a ZOOMED crop
  of the label region. If you judged "the chips look centred" from a thumbnail/full-page shot, you have NOT checked
  this; zoom in before clearing it. This is exactly the offset a human caught by eye that a full-page evaluator pass
  missed — treat connector-label anchoring as a zoom-required check.
- Example B: hierarchy is okay on desktop but the 2×2 matrix is cramped — labels truncated, quadrants uneven — so
  the one visual that should be instantly readable takes effort to parse.

**5/5 — instant hierarchy, clear scan path, generous whitespace, tidy aligned visuals**
- Example A: a strong type scale makes the section titles the obvious entry points; each visualization sits in its
  own breathing room, aligned to a consistent grid; the node graph's nodes are evenly spaced with non-crossing
  edges; the recommended table row is clearly emphasized. The eye is guided top-to-bottom without friction.
- Example B: whitespace is used as a structural device (concepts separated by air, not rules), the scan path is
  obvious within three seconds of looking, and every visual is tidy and aligned at both mobile and desktop widths.

---

## C3 — Anti-Slop · 2× (hard gate)

Strip the topic away — is this design generic-anywhere, or topic-specific? Judge by contrast against
`infographic-patterns.md` Part B, **not** a banned-word scan.

**1/5 — generic slop kit; could be any topic**
- Example A: a purple→indigo gradient hero with a giant centered title and a floating decorative blob; below it,
  three identical rounded cards (🛒 / ⚙️ / ✅ emoji + bold title + grey text); the rest is default sans at one
  size. Matches slop pairs 1, 2, 3, 4, 5 in infographic-patterns.md. This could be a landing page for anything.
- Example B: dark mode + neon-cyan accent applied with no reason rooted in reducers; glassmorphism cards float
  over a particle background. The "cool" aesthetic carries zero information about the subject. **Hard-gate FAIL.**

**3/5 — mostly restrained but leans on one or two slop tells**
- Example A: the layout is clean and the diagrams are real, but section bullets still use emoji markers (🔹) as
  decoration, and the intro is a generic three-identical-card row before the good content starts. One or two tells
  remain (pairs 2 and 3).
- Example B: the palette is fine and most visuals are topic-specific, but the hero is still a generic gradient
  banner with the title floating over it (pair 1) rather than an instance of the topic. Close, not clean.

**5/5 — a coherent topic-specific visual language; zero slop tells**
- Example A: the palette is ink-blue-on-paper, evoking a wiring schematic, because the doc treats a reducer as a
  pure circuit (inputs → sealed box → output). That motif recurs: the dispatch flow, the function-box SVG, and the
  node graph all share the schematic line-weight and palette. Strip the React words and the design still *reads as*
  "a deterministic machine" — it could only belong to this subject.
- Example B: the header is the state graph itself at rest with the title in its negative space (pair 1 non-slop);
  list markers are the small reducer glyph (pair 2 non-slop); the three approaches are a 2×2, not three cards
  (pair 3 non-slop). No emoji, no gradient blob, no decoration that teaches nothing. Zero tells.

---

## C4 — Learning Effectiveness · 1× (carries the plain-language hard sub-gate)

Do interaction, analogies, examples, and flow actually build understanding? Is all jargon glossed?

**1/5 — definitions dumped; jargon unglossed; flow jumps around**
- Example: the doc opens with "A reducer enforces *referential transparency* and must be *idempotent*" with no
  gloss, gives no example, and presents "when to use" before the reader knows what a reducer is. A beginner is lost
  by the third sentence. **Unglossed pedantic jargon → plain-language hard-gate FAIL regardless of other strengths.**

**3/5 — has examples and a rough flow, but analogies are generic and one jargon term slips through with a weak gloss**
- Example: there is a worked example and the order is roughly familiar-to-new, but the analogy is the generic
  "think of it like a function" (not drawn from the declared commerce domain), and "idempotent" appears glossed only
  as "it's idempotent, meaning idempotent operations" — a circular, non-everyday gloss. Borderline on the sub-gate;
  flag it.

**5/5 — cognitively natural build-up, audience-domain analogies, an interaction to test understanding, all jargon avoided or glossed on the spot**
- Example: the doc builds from a familiar commerce scenario (a shopping cart the junior dev has seen), introduces
  "dispatch" as "telling the cart what happened — 'the user added 2 of item X'", renders the state change live as
  the reader edits the quantity, and never uses a pedantic term without an immediate everyday gloss (e.g.
  "*pure* — same inputs always give the same output, like a vending machine that always returns the same snack for
  the same code"). A beginner finishes able to reason about it.

---

## C5 — Content Accuracy · 1× (hard floor: any C5 < 3 → FAIL)

Are facts, concepts, and code correct and checkable against spec.md §6 sources?

**1/5 — a factual or code error that would mislead the reader**
- Example: the doc says a reducer "mutates state in place and returns it" and the code does `state.count++; return
  state;` — directly wrong (reducers must return a new object) and would teach a beginner a bug. **Any such error
  → FAIL.**

**3/5 — broadly correct but one imprecise/overgeneralized claim and a missing source on a checkable claim**
- Example A: the concepts are right, but the doc overstates "you should always use `useReducer` instead of
  `useState`" (an overgeneralization the React docs do not support), and a performance claim ("reducers are faster")
  is stated with no source even though spec.md §6 named the docs to cite.
- Example B (internal-consistency miss): every individual statement checks out against §6, but the AS-IS "the old
  way is painful" example contradicts a TO-BE feature the same doc describes — e.g. a school-system explainer frames
  "fixing one notice (공지) line is slow today → must ask outside → wait → stuck" as the AS-IS pain while its own
  feature list lists 공지사항 등록·수정/관리 as a NAMED feature of the new admin. Each line is individually true, but
  the narrative is internally inconsistent: the example implies that exact capability is *missing today and unique to
  the new system*, when the doc itself presents it as a managed feature. The right AS-IS example is a genuine gap —
  editing a STATIC page (school intro / principal's greeting / facilities) that today needs re-development. Flag it
  and do not score C5 a 5.
  **Do NOT excuse this by reasoning "the pain still maps to a general TO-BE benefit" (e.g. '학교가 직접 콘텐츠
  등록·수정' makes editing easier overall, so a notice fits).** That rationalization is the exact miss this anchor
  exists to prevent. The test is NARROW: take the concrete NOUN in the pain example (a 공지/notice, a FAQ, a 연혁
  entry) and look it up in the new system's NAMED feature list. If it is there as a dedicated feature, the
  "can't-do-this-today" framing is wrong for THAT noun — swap it for a noun that is genuinely a static/legacy gap.
  A general related benefit existing does not redeem a pain example whose specific subject is itself a shipped feature.

**5/5 — every fact/concept/code correct and checkable; nuances stated correctly**
- Example: the reducer code is correct and idiomatic, the "when to prefer it" guidance matches the cited React
  documentation including the nuance that it is about *complexity of state logic*, not raw speed, and every checkable
  claim points to a source named in spec.md §6.

---

## Cautions when scoring (preventing miscalibration)

- **Do not judge C3 with a banned-word scan.** The presence of the word "gradient" is not a FAIL; a purple gradient
  blob that carries no topic meaning is. Judge by the contrast-pair logic in infographic-patterns.md Part B:
  "generic-anywhere vs topic-specific."
- **Count C1 in the DOM, do not trust the report.** Enumerate each visualization with its DOM location and type;
  N and M are literal counts.
- **The plain-language sub-gate (C4) and the anti-slop gate (C3) can force a FAIL even when the numeric score looks
  passing.** They are gates, not weights.
- **Attach evidence to every score.** C1 = the enumerated visualization list; C2 = screenshot regions showing
  hierarchy/clutter; C3 = the matched contrast pair + screenshot/DOM snippet; C4 = quoted passages (analogy +
  jargon sweep); C5 = the claim quoted against its spec.md §6 source.
