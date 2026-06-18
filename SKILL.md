---
name: html-explainer-harness
description: Generate a single self-contained, infographic-first HTML document that explains a topic, concept, or code for education, review, or team-sharing — it first asks who the audience is (role / business domain / level), then converts concepts into mind maps, flowcharts, charts, webtoon/comic narratives, schematics, and topic-specific custom visualizations instead of text paragraphs, and renders + screenshots the result for a human visual checkpoint. Runs as a Planner→Generator→Evaluator harness with anti-slop and plain-language gates. Trigger phrases — KO "HTML 학습 문서", "교육용 HTML", "리뷰 문서 생성", "인터랙티브 설명 문서", "시각적 HTML 자료", "HTML 인포그래픽 문서", "개념 설명 HTML 만들어줘", "코드 리뷰 문서 HTML". EN "interactive HTML explainer", "educational HTML doc", "visual learning document", "infographic HTML explainer", "make an HTML explainer for this concept", "single-file HTML teaching doc", "visual HTML doc for my team", "explain this code as a visual HTML page".
---

# html-explainer-harness

A harness that turns a topic, concept, or piece of code into a **single self-contained, infographic-first HTML
document** for education, review, or team-sharing. The defining identity is **infographic-first**: concepts are
converted into mind maps, flowcharts, diagrams, charts, comparison matrices, webtoon/comic narratives, schematics,
and **invented topic-specific visualizations** — not stacked text paragraphs. Before producing any HTML the skill
**interrogates the audience** (who / business domain / level) and adapts vocabulary, analogy material, depth, and
visualization complexity to it.

Output is ONE HTML file that opens directly in a browser with no build step (inline HTML/CSS/SVG/Canvas/JS; CDN
allowed, not required). It runs as a **Simplified** Planner→Generator→Evaluator harness on Opus 4.8, with a
**mandatory render + human visual checkpoint** because the visual-aesthetics channel cannot be verified from text
alone.

This harness ports Anthropic's *"Harness Design for Long-Running Application Development"* (role separation, file
communication, rubric evaluation, self-evaluation-bias prevention, context-reset policy) into the
infographic-HTML-explainer domain.

## This skill's core principle: aesthetics by the human, only the verifiable by the machine

An LLM cannot reliably judge "does this whitespace breathe", "is this contrast comfortable", "does this font feel
right" — a **sensory limitation**. So **the human looking at the rendered HTML and signing off is the load-bearing
visual checkpoint (P-1)**. The Evaluator reads a screenshot (which helps it score Visual Scannability), but the
human sign-off on visual feel is a separate, mandatory step the Evaluator cannot substitute for. A run that reaches
"the Evaluator will handle it" without a human looking has skipped a gate.

## Role separation (each a separate `Agent` call, communicating only via files — V1-1)

| Role | Prompt | Produces |
|------|--------|----------|
| Planner | `references/planner-prompt.md` | `spec.md` (the HTML-doc spec) |
| Generator | `references/generator-prompt.md` | the single HTML file + `generator_report.md` |
| Evaluator | `references/evaluator-prompt.md` | `critique.md` |

Each role prompt is **fully self-contained** — the subagent reads only its own prompt and the handoff files. The
rubric is in `references/rubric.md`, the score anchors in `references/evaluator-calibration.md`, the visualization
catalog + slop/non-slop contrast gallery in `references/infographic-patterns.md`, and the render/human checkpoint
procedure in `references/screenshot-checkpoint.md`.

## Activation flow (orchestrator) — Planner / Generator / Evaluator each a separate `Agent` call

Iteration cap = **3–5 rounds** (a range, not a single value — V1-6).

1. **AUDIENCE INTAKE (GATE — mandatory first).** Ask the user the **audience triple**: **who** (role/job),
   **business domain** (the analogy/example material), **level** (beginner / intermediate / expert). Confirm the
   triple back to the user. Do **NOT** proceed to any HTML — or even dispatch the Planner — until it is answered, or
   the user explicitly authorizes a sensible default (which is then recorded as an explicit assumption). A document
   built for an unknown reader is a failure.
2. **Content gathering.** Collect the topic / source material / code to be explained. Note any facts or code that
   must be represented accurately and where to verify them (for spec.md §6).
3. **Dispatch Planner** (`Agent`, `references/planner-prompt.md`) with the audience triple + content → produces
   `spec.md` (§2 audience spine, §3 visual & pedagogical intent + density N/M, §6 accuracy context, §8 Definition
   of Done). If the Planner emits `NEED_AUDIENCE`, relay the question and pause at step 1.
4. **Dispatch Generator** (`Agent`, `references/generator-prompt.md`) with `spec.md` → produces the single HTML
   file + `generator_report.md`, following concept → visual-representation → inline-implementation, the
   plain-language pass, and step-f self-evaluation. **Single continuous session** (Simplified — no sprints). Honor
   `HANDOFF_NEEDED: handoff.md` if context anxiety appears (start a fresh Generator session; do NOT compact).
5. **RENDER + SCREENSHOT (GATE — mandatory, P-1).** Render the HTML in a real browser via the **agent-browser**
   skill; capture full-page screenshots at **≥ 2 viewport widths** (mobile + desktop); capture the **console log**
   while exercising the interactions the report lists. Target: zero console errors. See
   `references/screenshot-checkpoint.md`. This step **cannot be silently skipped** — if browser automation is
   unavailable, ask the user to open the file so the human checkpoint still happens.
6. **HUMAN VISUAL CHECKPOINT (GATE — mandatory).** Surface the rendered result/screenshots to the user (inline the
   PNGs and/or `open` the file) and **STOP** for a visual sign-off on layout / contrast / whitespace / font "feel"
   — the channel the LLM cannot judge. **Record the sign-off.** If the user flags visual issues, do NOT pass; feed
   them into the next round's critique as Blocking Issues and re-run steps 4→5→6.
7. **Dispatch Evaluator** (`Agent`, `references/evaluator-prompt.md`) with `spec.md`, the HTML file, the
   screenshots, and the console log → runs all six adversarial probes → writes `critique.md` with PASS/FAIL.
8. **Loop control.** If `critique.md` = PASS → deliver the HTML file. If FAIL and rounds < cap → relay
   `critique.md` to a Generator round (Strategic Decision: REFINE / PIVOT / ESCALATE) and return to step 4
   (re-running steps 5–7). **A `BLOCKED` verdict (Evaluator could not PASS because the human visual checkpoint
   was missing/declined) is treated as FAIL for loop purposes** — re-loop after performing steps 5–6, do not
   deliver. **Respect the 3–5 cap, but do NOT reflexively stop at 3 if scores are still climbing**
   (V1-6 / V1-10 late-leap wisdom). On a `REDIRECT:` or `design_memo.md`, approve or amend it before the Generator
   pivots.
9. **Cap reached without PASS.** Deliver the best iteration with `critique.md` attached and an honest summary of
   remaining gaps. Quality over rushing (V1-7); the best round may not be the last (V1-11).

**User-confirmation gates (both mandatory, never silently skipped):** (a) audience confirmation at step 1;
(b) human visual checkpoint at step 6.

## Safety gates

1. **Audience-intake gate** (step 1) — no HTML before who/domain/level is answered or a default explicitly
   authorized and recorded.
2. **Render + human visual checkpoint gate** (steps 5–6, P-1) — render → screenshot → human sign-off. Cannot be
   weakened or skipped even in Simplified tier. The Evaluator cannot PASS without it (probe 5).
3. **Plain-language gate** and **anti-slop gate** — hard FAIL conditions in the rubric (jargon without an
   immediate everyday-analogy gloss; a generic-anywhere design). Judged in evaluator probes 2 and 3.

## File Handoff Contract (communicate only via files — V1-19)

| File | Author | Contents |
|------|--------|----------|
| `spec.md` | Planner | Audience spine (§2), visual & pedagogical intent + density N/M (§3), accuracy context (§6), Definition of Done (§8) |
| `generator_report.md` | Generator | Strategic Decision + deliverable + density evidence + audience adaptation + plain-language pass + self-verification + how-to-view |
| `critique.md` | Evaluator | Verdict + rubric scores + 6 probe results + Blocking Issues + Iteration Quality Note + optional `REDIRECT:` |
| `design_memo.md` | Generator | PIVOT proposal + cited critique.md evidence (awaiting Evaluator/orchestrator approval) |
| `handoff.md` | Active role | Remaining-work spec on context anxiety (NOT compaction) |

There is **no `sprint_contract.md`** — the Simplified tier removes the sprint construct entirely (V2-1).

## Context reset policy (reset ≠ compaction — V1-21 / V1-22)

On Opus 4.8 context anxiety is largely eliminated, so a single continuous Generator session is expected to complete
one HTML document without reset. But the Generator prompt still carries the anxiety-detection triggers
(re-summarizing prior sections, reaching for "in conclusion" before done, section depth dropping mid-document,
"for brevity" in a section spec.md says should be detailed, skipping the self-evaluation). On any of these the
Generator finishes the current section cleanly, writes `handoff.md`, and a **fresh Generator session** resumes.
**Never compact** — compaction preserves the anxiety state; only a fresh session clears it.

**Orchestrator-visible triggers:** a Generator round that starts re-summarizing, drops section depth, reaches for
closing jargon before the doc is complete, or skips its self-evaluation → expect/honor `HANDOFF_NEEDED: handoff.md`
and start a fresh Generator session rather than compacting.

## Iteration wisdom

- **Set the cap as a range, not a single value (V1-6).** Simplified = **3–5 total Evaluator rounds**. But pure
  frontend-design generation benefits from **5–15 iterations** of internal exploration — within a single generation
  round the Generator may explore multiple visual directions before submitting. The 3–5 is the *Evaluator-pass*
  ceiling, not a limit on internal exploration.
- **Do not pick the low end (V1-10).** In Anthropic's case study the Dutch Art Museum design had a **breakthrough
  (late leap) at iteration 10.** Cutting too early misses the breakthrough. If scores are still climbing at round 3,
  extend toward 5.
- **Be generous with wall-clock time (V1-7).** A round is not failed for taking time (the case study tolerated up to
  ~4 hours). Rich visualization takes time; do not artificially rush.
- **An intermediate iteration may beat the final one (V1-11).** The Evaluator records strengths a prior iteration
  had ("iteration N's state-graph visual was stronger than the current one" is valid, important feedback). The best
  round may not be the last — deliver the best, not the latest.

## V1 vs V2 model guidance (basis for the Simplified default — V2-3 / V2-4)

This skill ships as **Simplified** by default. The table records why, and how the tier shifts with the model.

| Model class | Context anxiety | Recommended tier | Notes |
|-------------|-----------------|------------------|-------|
| **Sonnet 4.5** | Strong — wraps up prematurely | Full (V1) | Smaller chunks, aggressive Evaluator, firm context resets. Keep the visual checkpoint. |
| **Opus 4.5** | Largely eliminated | Simplified (V2) | Multi-hour coherent sessions; sprint decomposition droppable. |
| **Opus 4.6** | Eliminated; better planning/long-context/debugging | Simplified or single-session | 2+ hour builds sustainable; re-examine each component, drop what's not load-bearing. |
| **Opus 4.8** (this skill's target) | **Eliminated** | **Simplified** | Single continuous Generator + single end-of-run Evaluator; no sprints. **The human visual checkpoint stays mandatory** — it compensates for a sensory limit, not a context limit, so a model upgrade never removes it. |

Evaluator value is **not** a fixed yes/no (V2-3): it depends on the task-vs-model boundary. Here the Evaluator
still earns its place on Opus 4.8 because the weak axes (visualization richness, scannable craft, anti-slop) are
exactly where an unpressured strong model still drifts toward slop — and because it reads the screenshot the human
also signs off on.

## Principles (from the source article)

- **Every harness component encodes an assumption** about what the model cannot do on its own (G-1). On a model
  upgrade, stress-test each assumption **one at a time** and remove the ones no longer load-bearing. **Radical
  simplification (removing everything at once) failed; methodical one-at-a-time succeeded (G-2).** The
  human-visual checkpoint (P-1) is the exception — it is a sensory limit, so it never becomes droppable.
- **The harness's role shifts, it does not shrink, as the model improves (G-5).** A stronger model lets you drop
  sprints, not the rubric or the human gate; the structure moves to where the new weak edge is.
- Anthropic, *"Building Effective Agents"* — **"find the simplest solution possible, and only increase complexity
  when needed."** (G-3) This is why the skill defaults to Simplified and adds structure only where a gate is
  load-bearing.

## Evaluator tuning workflow (operating procedure — P-3 / G-4 / V1-20)

An uncalibrated Evaluator is too lenient. Treat its first runs as drafts. Run (a)–(d) as an operating procedure;
skipping any one is a defect.

(a) **Read the critique logs of completed runs.** Place each `critique.md` side by side with the actual HTML
    output (open it, look at the screenshots), and for every rubric score ask: *would a picky human instructional
    designer + a slop-hating design director have given the same score?*

(b) **Identify divergence patterns.** Typical drift: lenient scoring (passing generic/shallow visuals as adequate);
    judging anti-slop with a banned-word scan instead of the contrast-pair logic; missing a text-only section;
    trusting the generator_report.md over the rendered DOM; passing unglossed jargon; counting the same card-box
    six times as "six visualizations."

(c) **Update `evaluator-prompt.md` or `evaluator-calibration.md` with concrete counter-examples.** Add the missed
    case as a new anchor at the right criterion/level (e.g. "I scored this hero 4/5 but it's a generic gradient
    banner → add it to the C3 1/5 anchors" or "I missed that §4 was text-only → strengthen the density probe").

(d) **Re-run on the SAME input and confirm the Evaluator now catches the prior miss.** If it still misses, return
    to (c). Stop tuning when the Evaluator's verdicts correlate with a careful human expert pass and every blocking
    issue it raises is reproducible.

## Slop vs non-slop (teach the Generator by example)

Anti-slop is judged by **contrast**, not a banned-word list — "strip the topic words away; could this exact design
drop onto any other topic unchanged?" The full contrast gallery lives in `references/infographic-patterns.md`
Part B and is read by both the Generator (to build non-slop) and the Evaluator (to judge it). Slop tells the skill
exists to defeat: purple/indigo gradient heroes, emoji bullets, identical 3-column card rows, generic hero sections,
decorative gradient blobs/particles. The non-slop move is a **topic-specific visual language** — a palette, motif,
and layout that could only belong to this subject.

**Motif lock (forces cross-run diversity).** Anti-slop alone stops a *generic* look but does not stop two different
docs from converging on the same house style. To prevent that, the Planner's `spec.md` §3 carries a **MOTIF LOCK**:
it brainstorms ≥3 distinct candidate motifs, **commits ONE** as a frozen instruction (palette direction + recurring
visual vocabulary + a single register), and adds an **anti-convergence ban** (the generic default for this topic,
plus any earlier doc's motif, are forbidden). The Generator builds inside the committed motif; the Evaluator's
anti-slop probe checks adherence section by section. So variety is not left to chance — it is decided, and frozen,
before generation starts. Two topics get two committed worlds; the same topic re-run should pick a different candidate.

## Closing guide (on model upgrade)

Every structure here (separate roles, the rubric, the gates, the Evaluator) exists to compensate for a current
model weakness. On an upgrade (G-1, G-5), experiment with removing components **one at a time** — does coherence
hold without a separate Evaluator pass? does quality hold at the low end of the iteration range? **Keep only what
is load-bearing.** But the **human visual checkpoint (P-1) is permanent**: as long as the LLM cannot see beauty,
balance, and rhythm, the gate where the user looks at the render stays load-bearing no matter how strong the model
gets.
