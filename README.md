# html-explainer-harness

> Turn a topic, concept, or piece of code into a **single self-contained, infographic-first HTML document** for education, review, or team-sharing — built and quality-checked by a Planner → Generator → Evaluator agent harness.

**Language:** English · [한국어](./README.ko.md)

A [Claude Code](https://claude.com/claude-code) skill. It does **not** stack text paragraphs — it converts each concept into the visual that carries it best (mind map, flowchart, sequence diagram, chart, comparison matrix, webtoon-comic, schematic, or an *invented* topic-specific visualization), then renders the result and puts a human in the loop for the one thing an LLM cannot judge: visual feel.

---

## Why this exists

A strong model, left unpressured, drifts toward **AI slop**: purple gradients, emoji bullets, three identical cards, walls of prose. This skill applies the harness pattern from Anthropic's *"Harness Design for Long-Running Application Development"* to fight that drift. Three separated agents communicate **only through files**, so the agent that generates work is never the one that grades it — the structural fix for self-evaluation bias.

## What makes the output good

| Criterion | Weight | What it measures |
|-----------|:------:|------------------|
| **C1 Infographic Richness** | 2× | Concepts become varied, fitting, topic-specific visuals — not stacked text. |
| **C2 Visual Scannability** | 2× | Grasped at a glance: hierarchy, scan path, whitespace; connectors fully visible (no occlusion/clipping). |
| **C3 Anti-Slop** | 2× | A topic-specific visual language; none of the generic-anywhere slop patterns. *Hard gate.* |
| **C4 Learning Effectiveness** | 1× | Interaction, analogy, and examples build understanding. *Plain-language hard sub-gate.* |
| **C5 Content Accuracy** | 1× | Facts/concepts/code correct and checkable. *Floor: any C5 < 3 → FAIL.* |

The three 2× axes are weighted because they are exactly where an unpressured model is weakest.

## Key features

- **Audience-first.** Before any HTML, the skill asks **who** (role), **business domain** (the analogy source), and **level** (beginner / intermediate / expert). It never invents an audience. Vocabulary, analogies, depth, and visualization complexity all key off this.
- **Infographic-first.** The Generator's *first* thinking step is "which visual form carries this concept?" A text paragraph is the fallback, not the default. Density targets: **≥ 6 distinct visualizations, ≥ 4 distinct types**, every major section ≥ 1 visual.
- **Plain-language gate.** Pedantic jargon without an immediate everyday-analogy gloss is a hard FAIL — judged by "would a reader in this audience need a gloss?", not a banned-word list.
- **Anti-slop by contrast.** Slop is caught by a few-shot slop-vs-non-slop gallery ("strip the topic words away — could this design drop onto any other topic unchanged?"), not a keyword blocklist.
- **Mandatory human visual checkpoint.** The HTML is rendered in a real browser, screenshotted at ≥ 2 widths, and surfaced to a human for sign-off on layout / contrast / whitespace / feel. The Evaluator **cannot PASS** without it — a sensory limit a model upgrade never removes.
- **Single self-contained file.** Opens in a browser with no build step (inline HTML/CSS/SVG/Canvas/JS; CDN allowed, not required).

## How to use

In Claude Code, just describe what you want. The skill activates on phrases like:

- **EN:** "interactive HTML explainer", "educational HTML doc", "visual learning document", "infographic HTML explainer", "make an HTML explainer for this concept", "single-file HTML teaching doc", "explain this code as a visual HTML page"
- **KO:** "HTML 학습 문서", "교육용 HTML", "리뷰 문서 생성", "인터랙티브 설명 문서", "시각적 HTML 자료", "HTML 인포그래픽 문서", "개념 설명 HTML 만들어줘", "코드 리뷰 문서 HTML"

Example:

```
이 React useReducer 개념을 신입 프론트엔드 개발자(커머스 도메인, 입문)용
인터랙티브 HTML 설명 문서로 만들어줘.
```

The skill confirms the audience triple, builds the document, renders + screenshots it, stops for your visual sign-off, then runs the adversarial evaluation before delivering.

## Activation flow

```
1. Audience intake        (GATE — who / domain / level; never invented)
2. Content gathering
3. Dispatch Planner        →  spec.md (frozen spec: intent, density N/M, accuracy, Definition of Done)
4. Dispatch Generator      →  the single HTML file + generator_report.md
5. Render + screenshot     (GATE — real browser, ≥2 widths, console log)
6. Human visual checkpoint (GATE — you sign off on the visual feel)
7. Dispatch Evaluator      →  critique.md (6 adversarial probes, rubric, PASS/FAIL)
8. Loop (cap 3–5)          PASS → deliver · FAIL → a fresh Generator round with the critique
```

Each role is a **separate agent dispatch**; they share nothing but files (`spec.md`, `generator_report.md`, `critique.md`).

## Directory structure

```
html-explainer-harness/
├── SKILL.md                          # Orchestrator: activation flow, gates, iteration wisdom, tuning loop
└── references/
    ├── planner-prompt.md             # Planner agent prompt (writes spec.md; audience-intake step 0)
    ├── generator-prompt.md           # Generator agent prompt (concept → visual → inline implementation)
    ├── evaluator-prompt.md           # Evaluator agent prompt (6 adversarial probes + verdict logic)
    ├── rubric.md                     # 5 criteria, weights, verdict logic
    ├── evaluator-calibration.md      # Few-shot score 1/3/5 anchors per criterion (anti-leniency)
    ├── infographic-patterns.md       # Visualization catalog + slop/non-slop gallery + visual-detail rules
    └── screenshot-checkpoint.md      # Render → screenshot → human-sign-off procedure
```

## Tier & model guidance

Ships as **Simplified** (single continuous Generator + single end-of-run Evaluator, no sprint decomposition) — appropriate for Opus-class models where context anxiety is eliminated. On weaker models (e.g. Sonnet 4.5) switch to the **Full** tier (smaller chunks, firmer context resets). On any model, **the human visual checkpoint stays mandatory** — it compensates for a sensory limit, not a context limit.

## Credit

Implements the harness principles from Anthropic's *"Harness Design for Long-Running Application Development"* (2026): GAN-style role separation, file-based handoffs, rubric evaluation with few-shot anchors, context resets over compaction, and "every harness component encodes an assumption — keep only what is load-bearing." Built with the `skill-wizard-harness` meta-skill.
