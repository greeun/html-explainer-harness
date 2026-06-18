# Screenshot + Human Visual Checkpoint (mandatory gate, P-1)

> This gate exists because of a **sensory limitation**: an LLM cannot reliably judge layout balance, color
> contrast, whitespace rhythm, or font "feel" from source code alone. So after the HTML is generated, it is
> **rendered in a real browser, screenshotted, and shown to a human for sign-off**. This gate **cannot be
> weakened or skipped** even in the Simplified tier. The Evaluator **cannot issue PASS** without a recorded
> screenshot it has read AND a recorded human sign-off.
>
> Orchestrator steps 5 (render + screenshot) and 6 (human visual checkpoint) run this procedure.

---

## Why this gate is load-bearing and permanent

Every other harness component is a candidate for removal as the model gets stronger (G-1). **This one is not.**
It compensates for a *sensory* limit, not a context or planning limit — so it does not disappear with a model
upgrade. As long as the LLM cannot *see* whether the whitespace breathes or the contrast is comfortable, the
human-looking-at-the-render step is permanently required. "The Evaluator will handle it" is **not acceptable** —
the Evaluator reads a screenshot, which helps, but the *human sign-off on visual feel* is a separate, mandatory
step the Evaluator cannot substitute for.

---

## Step 5 — Render + screenshot (machine)

Use the **agent-browser** skill (preferred over any built-in browser tooling) to render the single HTML file in
a real browser and capture evidence. Do this **once** per Generator round unless a new defect is suspected
(efficiency: do not make the human wait while the machine re-scans what it can already see).

1. **Open the file** at a real `file://` path (or serve it locally if it needs same-origin for any fetch). It
   must open with **no build step**.
2. **Capture screenshots at ≥ 2 viewport widths** — at minimum one mobile (~390px) and one desktop (~1280px+).
   Capture full-page (scrolled) screenshots, not just the first fold, so every visualization is in evidence.
3. **Exercise the interactions** the generator_report.md "How to run / view" section lists (click the node graph,
   change the playground input, follow the animated flow). **Capture the browser console log while doing so** —
   the target is **zero console errors**.
4. **Save artifacts** to the run directory with stable names, e.g. `screenshot-desktop.png`,
   `screenshot-mobile.png`, `console.log`. Note the paths — the Evaluator needs them for probe 5 and probe 6.

If the agent-browser skill is unavailable in the environment, **do not silently skip the gate** — tell the user
that browser automation is unavailable and ask them to open the file and confirm, so the human checkpoint (step 6)
still happens. A run with no rendered evidence at all cannot PASS.

---

## Step 6 — Human visual checkpoint (human sign-off)

After rendering, **surface the result to the user and STOP for sign-off.** This is the channel the LLM cannot
judge.

1. **Show the rendered output**: inline the desktop and mobile screenshots into the conversation (Read the PNGs),
   and/or open the file in the user's browser (`open <file>` on macOS) so they can scroll it themselves.
2. **Ask a focused visual question**, e.g.:
   > "Here's the rendered explainer at desktop and mobile widths. Does the visual feel work for you — layout
   > balance, color contrast, whitespace, font feel? Anything look off or templated?"
3. **Record the sign-off** (who approved, and any notes) so the Evaluator can cite it in probe 5. A bare "looks
   fine" with no specifics is acceptable as a sign-off but note it as such.
4. **If the user flags visual issues**, do **not** PASS. Feed the issues into the next Generator round's critique
   (they become Blocking Issues), and re-run steps 4→5→6.

The human sign-off and the audience confirmation (orchestrator step 1) are the two mandatory user-confirmation
gates of this skill. Neither may be silently skipped.

---

## What the Evaluator does with these artifacts (probe 5 + probe 6)

- **Probe 5 (sensory-limit checkpoint gate):** confirm a screenshot exists, was read by the Evaluator (it judges
  layout alignment, contrast, whitespace, font rendering *from the image*, feeding C2), AND that a human sign-off
  is recorded. Missing any of the three → **cannot PASS** (blocked).
- **Probe 6 (technical-integrity):** from the same render, confirm **zero console errors** during interactions,
  **responsive** behavior with no layout breakage across the ≥2 captured widths, and **semantic HTML with no
  broken/unclosed tags**. Cross-check the rendered DOM against the generator_report.md claims — do not trust the
  report alone ("does the interaction actually do what it says, or just toggle CSS?").

---

## Quick checklist (paste into the run notes)

- [ ] HTML opened from file path with no build step
- [ ] Desktop screenshot captured (full page)
- [ ] Mobile screenshot captured (full page)
- [ ] Interactions exercised; console log captured; **zero errors**
- [ ] Screenshots inlined / file opened for the user
- [ ] User asked the focused visual question and **signed off** (or flagged issues → next round)
- [ ] Screenshot paths + console log path + sign-off recorded for the Evaluator
