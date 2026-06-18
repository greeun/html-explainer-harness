# Rubric — html-explainer-harness

> The Evaluator scores the single-file HTML explainer document against these five criteria.
> Each criterion is scored **1–5**. Three criteria carry **2× weight** because they are the axes
> Claude is weakest on by default for visual/creative HTML generation; two carry **1×** because
> Claude already handles them adequately. Read `evaluator-calibration.md` for the concrete 1/3/5
> score anchors before scoring — they prevent score drift (V1-5/V1-8).
>
> Criteria are written as **qualities, not references** (V1-4). No "museum-quality" / "Apple-like" /
> brand names appear here — those, if ever useful, live in `spec.md` §3 design intent only.

---

## The five criteria

| # | Criterion | Weight | What it measures |
|---|-----------|--------|------------------|
| **C1** | **Infographic Richness** | **2×** | Concepts are converted into mind maps / diagrams / flowcharts / charts / comparison matrices / webtoon-comic narratives / schematics / invented topic-specific visualizations — not stacked text paragraphs. Measures *variety of visualization types*, *fit of each visual to the concept it carries*, and *creative reach* (not cliché box-stacking). |
| **C2** | **Visual Scannability** | **2×** | The document can be grasped at a glance: clear visual hierarchy, an obvious scan path, deliberate layout, type scale that guides the eye, generous whitespace, and visualizations that are tidy and aligned rather than cluttered or overlapping. |
| **C3** | **Anti-Slop** | **2×** | No template/cliché design. The document carries a **topic-specific visual language** — a palette, motif, and layout that could only belong to this subject. **Mandatory hard gate** (see Verdict Logic). |
| **C4** | **Learning Effectiveness** | 1× | Interaction, fitting analogies, and worked examples genuinely promote understanding; the explanation flow is cognitively natural (builds from familiar to new). **Plain-language sub-gate:** no pedantic / non-everyday jargon (e.g. 직교성 "orthogonality", 멱등성 "idempotency") without an immediate everyday-analogy gloss. The plain-language sub-gate is a **hard FAIL** independent of the C4 score. |
| **C5** | **Content Accuracy** | 1× | Facts, concepts, and code are correct; nuances stated correctly; claims checkable against the sources named in `spec.md` §6. Includes **internal consistency** — a before/after or AS-IS "painful today" example must not contradict a TO-BE feature the doc itself describes (an AS-IS pain the new system solves as a named feature is a logic error even if each line is individually true). Enforced as a hard floor (any C5 < 3 → FAIL). |

### Weighting rationale (must appear verbatim-equivalent here and in evaluator-prompt.md)

C1 / C2 / C3 are weighted **2×** because Claude already scores well on **C4 (Learning Effectiveness)**
and **C5 (Content Accuracy)** by default — pedagogical structure, fitting analogies, and technical
correctness are baseline Claude strengths. What Claude lacks *without explicit pressure* is **rich and
varied topic-specific visualization (C1), scannable visual craft (C2), and slop-free originality (C3)**.
Under no pressure Claude defaults to prose plus generic box layouts; the 2× weights exist to push against
exactly that default. Weighting C4/C5 at 2× would steal pressure from the weak axes the harness exists to fix.

---

## Scoring guide (per criterion)

| Score | Meaning |
|-------|---------|
| 5 | Exceeds expectations — a picky instructional designer / design director would be impressed. |
| 4 | Meets expectations — solid, professional, no notable weakness on this axis. |
| 3 | Acceptable but noticeably weak — needs improvement before delivery. |
| 2 | Below expectations — significant gaps. |
| 1 | Unacceptable — fundamental problems on this axis. |

Anchor each score to the concrete examples in `evaluator-calibration.md`. Do not invent a private scale.

---

## Verdict Logic (V1-17)

```
All criteria ≥ 4 AND all adversarial probes clean        → PASS
Any 2×-weighted criterion (C1 / C2 / C3) < 4             → FAIL
Any 1×-weighted criterion (C4 / C5) < 3                  → FAIL
Plain-language gate triggered (jargon w/o gloss)         → FAIL   (hard gate, independent of C4 score)
Anti-slop gate triggered (generic-anywhere design)       → FAIL   (hard gate, independent of C3 score)
Any Definition-of-Done item from spec.md unverified      → FAIL
Human visual checkpoint not performed / declined         → cannot PASS (blocked, per P-1)
```

The two hard gates (plain-language, anti-slop) can each force a FAIL even when the numeric score on the
related criterion is otherwise passing. They are gates, not weights. The human-visual-checkpoint requirement
is a **block**, not a score: without a recorded screenshot read by the Evaluator AND a recorded human
sign-off, the Evaluator cannot issue PASS regardless of every other score.

---

## Calibration Checkpoint

After scoring, **if every criterion is ≥ 4**, re-evaluate through two lenses before issuing PASS, and add
any new findings to the critique:

1. **Picky instructional-designer lens** — "Would a learner in the declared audience actually *understand
   faster* because of these visuals, or are they decoration? Is any jargon sneaking through unglossed? Is the
   build-up cognitively natural?"
2. **Design-director-who-hates-slop lens** — "Strip the topic words away. Could this exact layout, palette,
   and card structure be dropped onto any other topic unchanged? If yes, the visual language is generic-anywhere,
   not topic-specific — re-examine C3 even if it scored 4."

A surface-level completion that does not achieve deep functionality is a FAIL, not a partial pass — e.g. an
"interactive" node graph whose nodes only change color but never actually demonstrate the state transition
they claim to teach, or a section heading that exists but whose visual is a placeholder box.
