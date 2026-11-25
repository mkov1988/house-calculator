# UI Design Spec: Investment Growth Calculator

**Version:** 1.0
**Date:** March 14, 2026
**Status:** Draft — UI Consensus (UXD-Led Review)
**Companion to:** `investment-calculator-spec.md` (Feature Spec v2.0)

---

## Expert Panel — UI Design Debates

Same team, same hierarchy. **Sam (UXD) leads with final call on all visual and interaction decisions.** Sam weights Dana's (Financial Advisor) input most heavily on anything that affects user comprehension, trust, or potential misinterpretation.

### The Cast

| Handle | Role | Reports To | Incentive / Bias |
|--------|------|-----------|-----------------|
| **Sam (UXD)** | UX Design Lead | — | Driving user value and usability |
| **Dana (FA)** | Financial Advisor SME | Sam | Financial accuracy, trust, preventing misinterpretation |
| **Alex (PM)** | Product Manager | Sam | Ship fast, reuse existing patterns, minimize new components |
| **Jordan (Dev)** | Frontend Developer | Sam | Reuse CSS tokens, minimize new JS, stay in Chart.js |
| **Jamie (UXR)** | UX Researcher | Sam | User mental models, progressive disclosure, accessibility |
| **Taylor (Marketing)** | Growth / Marketing | Sam | Visual impact, shareability, screenshot-worthy moments |

---

## Debate 1: Section Layout — Same Grid or New Pattern?

**Context:** The existing mortgage section uses a `grid-template-columns: clamp(300px, 25vw, 400px) 1fr` layout — sticky input panel on the left, scrolling output panel on the right. Do we repeat this?

**Alex (PM):** Same pattern. Users already understand it. Jordan can reuse CSS. Ship faster.

**Jordan (Dev):** Agree from a code perspective — I can literally wrap the new section in the same `.app-container` grid class with a new ID. But there's a problem: the existing left panel is sticky with `position: sticky; top: 1.33rem`. If we have two sections with two sticky panels, the second panel's sticky context won't work correctly — sticky positioning is relative to the scroll container, and we'd need a new scroll context for the second section.

**Jamie (UXR):** I have a different concern. Users just finished a left-panel/right-panel experience. Scrolling into an identical layout for a different tool will feel repetitive and may cause "tool fatigue" — they may assume they're still in the mortgage section. We need a visual break.

**Dana (FA):** The investment calculator has fewer inputs than the mortgage section (4 core vs. 11). A full sticky left panel for 4 sliders feels oversized and unbalanced. The input:output ratio is different here — this tool is more chart-forward and less input-heavy.

**Taylor (Marketing):** The chart IS the product in this section. If I could design this for screenshots, I'd put the chart at the top with inputs below or overlaying it.

**Sam (UXD) — FINAL CALL:** Dana nailed it. The mortgage section is input-heavy (11 fields, two scenario boxes, advanced settings). The investment section is chart-forward with only 4 core inputs. Different content shapes need different layouts.

Here's the layout:

```
┌─────────────────────────────────────────────────────────────────────┐
│  ══════════ SECTION DIVIDER / VISUAL BREAK ════════════════════════ │
│  "Investment Growth Calculator" section header                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─ SUMMARY BANNER ──────────────────────────────────────────────┐  │
│  │  📈 Projected Total US Stock Market value: $847,214 after 25y │  │
│  │     $647,214 in estimated gains on $200,000 contributed       │  │
│  └───────────────────────────────────────────────────────────────┘  │
│  ┌─ DISCLAIMER (always visible) ─────────────────────────────────┐  │
│  │  Projections use historical averages...                        │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌─ INPUT BAR (horizontal, compact) ────────────────────────────┐  │
│  │  [$10,000 ═══●══]  [$500/mo ══●════]  [25 yrs ════●═]       │  │
│  │  Initial Invest.    Monthly Contrib.   Time Horizon           │  │
│  │                                                               │  │
│  │  FUND TYPES: [● Total US Stock] [● Bonds] [● HYSA]           │  │
│  │              [○ S&P 500] [○ Intl] [○ Target Date]             │  │
│  │                                                               │  │
│  │  ▸ Customize Assumptions                                      │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌─ STAT CARDS (side by side) ──────────────────────────────────┐  │
│  │  ┌── What You Put In ──┐  ┌── What You Could Get ──────┐     │  │
│  │  │  $200,000 total     │  │  $847,214 projected        │     │  │
│  │  │  $10K initial       │  │  $647,214 est. gains       │     │  │
│  │  │  $500 × 300 months  │  │  4.2x growth multiple      │     │  │
│  │  └─────────────────────┘  └────────────────────────────┘     │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌─ CHART (full-width, hero element) ───────────────────────────┐  │
│  │  Investment Growth Over Time                            ℹ️    │  │
│  │  ┌─────────────────────────────────────────────────────────┐  │  │
│  │  │    $900K ┤                                          ╱   │  │  │
│  │  │    $800K ┤                                       ╱╱     │  │  │
│  │  │    $700K ┤                                    ╱╱╱       │  │  │
│  │  │    $600K ┤                                ╱╱╱╱          │  │  │
│  │  │    $500K ┤                           ╱╱╱╱╱              │  │  │
│  │  │    $400K ┤                      ╱╱╱╱╱                   │  │  │
│  │  │    $300K ┤                ╱╱╱╱╱╱    ╱╱ Bonds            │  │  │
│  │  │    $200K ┤          ╱╱╱╱╱╱    ╱╱╱╱╱╱ HYSA              │  │  │
│  │  │    $100K ┤    ╱╱╱╱╱╱    ╱╱╱╱╱                          │  │  │
│  │  │         ┤╱╱╱╱╱╱╱╱╱╱╱╱╱╱ - - - - - - - Your Contribs   │  │  │
│  │  │         └───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───   │  │  │
│  │  │             0   3   5   8  10  13  15  18  20  23  25   │  │  │
│  │  └─────────────────────────────────────────────────────────┘  │  │
│  │  ● Total US Stock  ● Bonds  ● HYSA  --- Your Contributions   │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌─ DATA TABLE (collapsed by default) ──────────────────────────┐  │
│  │  ▸ Show Year-by-Year Breakdown                                │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

Key differences from the mortgage section:
- **Full-width layout** — no left/right split. Inputs are in a horizontal bar, not a sticky column.
- **Chart is the hero** — largest element on screen, full-width, center stage.
- **Inputs are compact** — horizontal row of 3 sliders + pill selector, not a tall vertical panel.
- **Visual break** between sections is unambiguous.

**✅ DECISION:** Full-width layout. Horizontal input bar. Chart as hero element. No sticky left panel.

---

## Debate 2: Section Divider — How to Transition Between Sections

**Taylor (Marketing):** Big gradient divider. Eye-catching. "Part 2" energy.

**Jamie (UXR):** The divider needs to signal a cognitive context switch. Users are done thinking about mortgage tradeoffs; now they're thinking about investment growth. The section header copy matters as much as the visual treatment.

**Dana (FA):** The section header should frame the tool's purpose. Not "Investment Calculator" alone — that's too generic. Something like "Investment Growth Calculator — How does your money grow over time?" This primes the user with the right mental model.

**Sam (UXD) — FINAL CALL:** Here's the divider spec:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                         ─── ◆ ───                                   │
│                                                                     │
│             Investment Growth Calculator                             │
│  How does your money grow over time across different fund types?    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

- **Top rule:** A thin horizontal line with a centered diamond ornament (CSS `::before` pseudo-element). Provides visual closure to the mortgage section.
- **Section title:** `var(--text-2xl)`, `font-weight: 800`, matching the app's existing header hierarchy.
- **Subtitle:** `var(--text-base)`, `var(--text-secondary)`. Frames the purpose.
- **Spacing:** `6rem` top margin, `3rem` bottom margin. Generous whitespace signals "new context."
- **Background:** Stays `var(--bg)` — no gradient. The divider earns attention through typography and space, not color noise.
- **Dark mode:** Diamond and rule use `var(--border)`. Title/subtitle use existing text tokens.

**✅ DECISION:** Typographic divider with diamond ornament. Generous whitespace. Purpose-framing subtitle. No gradient.

---

## Debate 3: Input Bar — Horizontal Compact Layout

**Alex (PM):** Three sliders in a row with fund pills below. Clean.

**Jordan (Dev):** The existing slider + number input pattern takes ~80px height per input. Three in a row at `clamp(300px, 25vw, 400px)` each needs at least 900px viewport. Works on desktop; on tablet we stack to two columns, on mobile we stack to one.

**Jamie (UXR):** The existing input pattern has the label on top, slider below, number input to the right. If we compress three of these side-by-side, the labels need to be ultra-clear because users are scanning horizontally now instead of vertically. Each input needs:
1. A concise label (e.g., "Initial Investment" not "Starting Lump Sum Amount")
2. The dollar/year value prominently displayed
3. The slider below

**Dana (FA):** The "Customize Assumptions" toggle is critical for the fee education flow. It should NOT feel hidden. I'd like it styled as a distinct, inviting element — not just a plain text link.

**Sam (UXD) — FINAL CALL:**

```
Desktop (≥ 1024px):
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  Initial Investment     Monthly Contribution   Time Horizon  │
│  $ [10,000      ]      $ [500           ]     [25    ] yrs  │
│  [═══════●═══════]      [═══●════════════]     [════●═════]  │
│                                                              │
│  Fund Types                                                  │
│  [● Total US Stock] [● Bonds] [● HYSA]                      │
│  [○ S&P 500] [○ International] [○ Target Date 2050]          │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  ⚙ Customize Assumptions — adjust returns, fees,    │    │
│  │    and contribution growth                      ▾    │    │
│  └──────────────────────────────────────────────────────┘    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

Input bar specs:
- **Container:** Same `.card` styling as existing components — `var(--surface)`, `var(--border)`, `var(--radius)`, `var(--shadow)`.
- **3-column grid on desktop:** `grid-template-columns: 1fr 1fr 1fr`, `gap: 2rem`.
- **Tablet (768–1023px):** Collapses to 2-column grid (Initial + Monthly on row 1, Time Horizon on row 2).
- **Mobile (<768px):** Single column stack. Same vertical layout as existing mortgage inputs.
- **Number inputs:** Positioned ABOVE sliders (not beside them). Larger text `var(--text-lg)` so the value is the dominant visual. This differs from the mortgage section where the value is small — here, with only 3 inputs, each value can be bigger.
- **Fund type pills:** Rounded pill buttons. Active pills are filled with the fund's chart color. Inactive pills are outlined with `var(--border)`. Click to toggle. Each pill shows a small colored dot matching the chart line color to create a visual link.
- **Customize Assumptions:** Full-width box below the pills. Subtle background `var(--accent-light)` to invite discovery. Gear icon (⚙). Descriptive text, not just a label. Expands an accordion panel with per-fund rate sliders, per-fund ER sliders, and the annual contribution increase slider.

**✅ DECISION:** Horizontal 3-column input bar on desktop, responsive stacking. Values displayed large above sliders. Fund pills with color dots. Inviting Customize toggle.

---

## Debate 4: Fund Type Pills — Visual Design

**Taylor (Marketing):** Bright, branded, candy-like. Each fund gets a distinct, bold color.

**Sam (UXD):** We need 6 colors that are all distinct, all accessible (WCAG AA on both light and dark backgrounds), and that don't clash with the existing S1 blue / S2 purple from the mortgage section.

**Jamie (UXR):** The pill color MUST match the chart line color exactly. Any disconnect between "the green pill" and "the green line on the chart" breaks the mental model. Also, we need a colorblind-safe palette. At minimum, ensure the 3 default funds pass deuteranopia simulation.

**Dana (FA):** The HYSA pill should feel visually "lesser" — not dull, but lower energy. Users should intuitively see it as the baseline. Same idea as how a control group looks different from test groups in a study.

**Sam (UXD) — FINAL CALL:**

**Color Palette (6 funds):**

| Fund | Color Name | Hex (Light) | Dark Mode Adjusted | Visual Weight |
|------|-----------|-------------|-------------------|---------------|
| Total US Stock Market | Indigo | `#4f46e5` | `#818cf8` | Strong — primary fund |
| S&P 500 | Blue | `#2563eb` | `#60a5fa` | Strong |
| International | Teal | `#0d9488` | `#2dd4bf` | Medium |
| Bonds | Amber | `#d97706` | `#fbbf24` | Medium |
| Target Date 2050 | Rose | `#e11d48` | `#fb7185` | Medium |
| HYSA (Baseline) | Slate | `#64748b` | `#94a3b8` | Muted — intentionally lower energy |

This palette:
- **Passes** WCAG AA contrast on `var(--surface)` in both light and dark modes.
- **Passes** deuteranopia and protanopia simulation for the 3 default funds (indigo, amber, slate).
- **HYSA is intentionally muted** per Dana's guidance — users intuitively read it as the baseline.
- **Avoids** the existing S1 blue (`#3b82f6`) and S2 purple (`#7c3aed`) to prevent confusion with mortgage scenarios. The closest overlap is Indigo (`#4f46e5`) which is distinct enough (deeper, warmer) and only appears in the investment section below the divider.

**Pill states:**

```
Active:   [●═══════════]  Filled background (fund color), white text
Inactive: [○ ── ── ── ──]  Outlined border (--border), text color, no fill
Hover:    Light background tint of fund color (8% opacity)
Focus:    2px outline ring (fund color, 40% opacity) — keyboard accessible
```

**✅ DECISION:** 6-color accessible palette. HYSA muted as baseline. Pills match chart lines exactly. Colorblind-safe defaults.

---

## Debate 5: Summary Banner — Projection Language and Visual Design

**Taylor (Marketing):** The existing mortgage section's "Bottom Line" header pattern works great. Big number, bold, gradient footer. Can we do a similar hero treatment?

**Dana (FA):** The existing mortgage section shows a hard number and says "Scenario 2 wins by $688,816." That framing implies certainty. For investment projections, the banner must feel probabilistic, not deterministic. I want the word "projected" in the dominant visual, not buried in fine print.

**Jamie (UXR):** Users anchor on the first large number they see. If the banner says "$847,214" in 2xl bold and "projected" is in small text, they'll remember the number and forget the qualifier. The projection language needs to be part of the visual hierarchy, not an afterthought.

**Sam (UXD) — FINAL CALL:**

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  Projected Portfolio Value                                   │
│                                                              │
│  $847,214                after 25 years                      │
│  in Total US Stock Market                                    │
│                                                              │
│  $647,214 in estimated gains  ·  $200,000 contributed        │
│  ────────────────────────────────────────────────────────────│
│  vs. $183,424 projected in High-Yield Savings                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

Typography and hierarchy:
- **"Projected Portfolio Value"** — `var(--text-sm)`, `font-weight: 600`, `text-transform: uppercase`, `letter-spacing: 0.5px`, `color: var(--text-secondary)`. Functions as an eyebrow label. The word "Projected" is the first word you read.
- **"$847,214"** — `var(--text-2xl)`, `font-weight: 800`, `color: var(--text)`. The hero number. Large but contextually qualified by the eyebrow label above it.
- **"after 25 years"** — same line as the number, `var(--text-lg)`, `font-weight: 400`, `color: var(--text-secondary)`. Provides time context immediately.
- **"in Total US Stock Market"** — `var(--text-base)`, `font-weight: 500`. Identifies which fund. Uses the fund's pill color.
- **Gains / Contributions line** — `var(--text-sm)`, separated by a midpoint (·). Grounds the number.
- **HYSA comparison** — Only shown when HYSA is in selected funds. `var(--text-sm)`, `color: var(--text-secondary)`. Subtle but present. Uses "projected" again.
- **Animated counter** — On value change, the hero number animates using the same counter effect as the existing mortgage banner. Duration: 400ms ease-out.
- **Background:** `var(--surface)` card with a 3px left border in the leading fund's color. No gradient fill — this section uses restraint to build trust, not excitement.
- **Dark mode:** All tokens already defined. No special treatment needed.

**✅ DECISION:** Eyebrow-first hierarchy puts "Projected" before the number. Left-border accent (not gradient fill). "Estimated gains" language throughout. HYSA comparison only when selected.

---

## Debate 6: Disclaimer Row — Present but Not Obnoxious

**Dana (FA):** Non-negotiable: always visible, never dismissed, never behind a click.

**Alex (PM):** Fine, but it shouldn't take up more vertical space than one line on desktop.

**Sam (UXD) — FINAL CALL:**

```
┌──────────────────────────────────────────────────────────────┐
│  ⓘ  Projections use historical averages and do not guarantee │
│     future results. Actual returns vary, including negative   │
│     years. Not financial advice.                              │
└──────────────────────────────────────────────────────────────┘
```

- **Styling:** `var(--text-sm)`, `color: var(--text-secondary)`, `line-height: 1.4`.
- **Container:** No border, no background. Just an ⓘ icon and text. Sits directly below the banner card with `0.5rem` gap.
- **On desktop:** Fits on 1–2 lines depending on viewport width.
- **On mobile:** Wraps to 2-3 lines. Still always visible.
- **Never:** Bold, red, alarming, or attention-stealing. It's a footnote, not a warning. Calm authority.

**✅ DECISION:** One-line footnote style with info icon. Always visible. Calm, not alarming.

---

## Debate 7: The Chart — Visual Treatment

**Jordan (Dev):** Chart.js is already loaded. I can reuse the same configuration patterns from the mortgage charts: line datasets, tooltip callbacks, responsive config. The contributions dashed line is just another dataset with `borderDash: [8, 4]`.

**Sam (UXD):** The chart is the hero element of this section. I want it to feel like a significant visual moment, not just a repeat of the mortgage charts. Here are my requirements:

**Chart container:**
- Full-width inside the content area.
- Min height: `400px` desktop, `300px` mobile (taller than existing mortgage charts at `320px`).
- `var(--surface)` card with `var(--shadow-md)`. Generous padding: `2rem`.

**Grid and axes:**
- Y-axis: formatted as currency (`$100K`, `$200K`, etc.). Uses `var(--text-secondary)` color.
- X-axis: "Year 0, 5, 10, 15, 20, 25." Abbreviated to every 5th year to reduce clutter.
- Grid lines: subtle, `rgba(0,0,0,0.04)` in light mode, `rgba(255,255,255,0.06)` in dark.

**Fund lines:**
- Line width: `2.5px` (slightly thicker than mortgage charts at `2px`).
- Point dots: hidden by default, visible on hover.
- Smooth tension: `0.3` (gentle curve, not angular).

**Contributions dashed line:**
- Color: `var(--text-secondary)` at 50% opacity.
- Dash pattern: `[8, 4]`.
- Line width: `1.5px`.
- Always visible, NOT in the clickable legend (can't be toggled off).
- Label in legend area: "Your Contributions" with dashed line icon.

**Dana (FA):** I want the space between the contributions line and each fund line to be visually meaningful. Can we fill the area between the contributions line and the selected lines with a very subtle tint? This makes the "gains" literally visible as colored space.

**Jamie (UXR):** That's a great affordance — it turns an abstract concept ("gains") into a visible, scannable shape. But only do it for the leading fund (highest value), otherwise the overlapping fills create visual noise.

**Sam (UXD) — FINAL CALL:**

- **Yes to the fill** — but only between the contributions line and the **leading fund** line. Fill uses the leading fund's color at **6% opacity** (very subtle). This creates a "gains area" that is visible but doesn't overpower the line chart.
- **On hover:** The tooltip shows a small inline comparison: "Year 15: $312,400 projected · $100,000 contributed · $212,400 in gains."
- **Chart title:** "Investment Growth Over Time" with the ℹ️ info icon to its right.
- **ℹ️ tooltip (volatility):** On click/tap, a popover appears (not a native title tooltip — those are too small and not mobile-friendly). Popover has a max-width of `360px`, uses `var(--surface)` background with `var(--shadow-md)`. Dismissable by clicking outside or pressing Escape.

```
┌─ Chart Info Popover ──────────────────────────────┐
│                                                   │
│  This chart shows projected growth based on       │
│  historical average annual returns. Actual        │
│  year-to-year returns vary significantly —        │
│  including negative years. Historically,          │
│  longer time horizons have reduced the impact     │
│  of short-term volatility.                        │
│                                                   │
│  Returns shown are nominal (not adjusted          │
│  for inflation).                                  │
│                                                   │
└───────────────────────────────────────────────────┘
```

**✅ DECISION:** Full-width hero chart. Subtle gains fill for leading fund only. Contributions line always visible and non-toggleable. ℹ️ popover (not native tooltip) for volatility context.

---

## Debate 8: Stat Cards — Layout and Content

**Alex (PM):** Side-by-side cards, same pattern as the mortgage cashflow cards.

**Dana (FA):** The growth multiple deserves visual prominence. When a user sees "4.2x" they immediately grasp compound interest more than seeing "$647,214 in gains." It's the most memorable number on the page.

**Taylor (Marketing):** 100% agree — "4.2x" is the number people text their friends. It should be the largest number on the card.

**Sam (UXD) — FINAL CALL:**

```
Desktop (side by side):
┌── What You Put In ────────┐  ┌── Projected Returns (Total US Stock) ─┐
│                           │  │                                        │
│  $200,000                 │  │            4.2×                        │
│  total contributed        │  │        growth multiple                 │
│                           │  │                                        │
│  $10,000 initial          │  │  $847,214 projected value              │
│  + $500/mo × 25 years     │  │  $647,214 estimated gains              │
│                           │  │                                        │
└───────────────────────────┘  └────────────────────────────────────────┘
```

- **Left card:** "What You Put In" — header uses `var(--text-sm)`, uppercase, secondary color. Hero number is total contributions in `var(--text-xl)`, `font-weight: 700`. Breakdown below in `var(--text-sm)`.
- **Right card:** "Projected Returns" — header includes the leading fund name in the fund's color. Hero number is the **growth multiple** in `var(--text-2xl)`, `font-weight: 800`, fund color. The "×" symbol creates instant visual impact. Projected value and gains below in `var(--text-base)`.
- **Card styling:** Same `.card` class as existing components. Equal height via `align-items: stretch` on the flex container.
- **Mobile:** Stack vertically. Right card stays on top (the result is more interesting than the input).
- **When annual contribution increase is active:** Left card's breakdown changes to "escalating from $500/mo (+3%/yr)."
- **Language:** Right card header says "Projected Returns" not "What You Get Back" — Dana's influence. The word "projected" appears in the card, reinforcing that this is a projection.

**✅ DECISION:** Growth multiple as hero number on the returns card. Side by side desktop, stacked mobile (returns card first on mobile). Projection language in card header.

---

## Debate 9: Fee Impact Card — Design When Advanced Is Open

**Dana (FA):** This card is the single most educational element after the contributions line. When a user cranks the expense ratio to 1% and sees "$540,000 in fees over 30 years," it should be a genuine "holy shit" moment. But delivered calmly — we inform, we don't panic.

**Taylor (Marketing):** This is the most shareable stat in the whole tool. Can we make it screenshot-friendly?

**Jamie (UXR):** Warning: if the fee impact card looks like an error or alert, users will feel punished for exploring Advanced mode. It needs to feel like a helpful insight, not a scolding.

**Sam (UXD) — FINAL CALL:**

```
When expense ratio ≤ 0.5% (typical index fund):
┌──────────────────────────────────────────────────────────────┐
│  💡 Fee impact                                               │
│                                                              │
│  At 0.03% annual fees, your estimated fee cost on            │
│  Total US Stock Market is $2,814 over 25 years.              │
│                                                              │
│  Low-cost index funds keep more of your money working.       │
└──────────────────────────────────────────────────────────────┘

When expense ratio > 0.5% (active fund territory):
┌──────────────────────────────────────────────────────────────┐
│  💡 Fee impact                                               │
│                                                              │
│  At 1.00% annual fees, your estimated fee cost on            │
│  Total US Stock Market is $148,291 over 25 years.            │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Switching to a 0.03% index fund could save you        │  │
│  │  an estimated $145,477 over the same period.           │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

- **Container:** `var(--surface)` card with a `3px` left border in `var(--accent)` (indigo). NOT orange/yellow (that's the warning color from tax drag — this is a different emotional register: insight, not warning).
- **"💡 Fee impact" header:** `var(--text-sm)`, `font-weight: 700`, `text-transform: uppercase`. Light bulb emoji communicates "insight."
- **Fee dollar amount:** `font-weight: 700`, `color: var(--text)`. Bold but same size as body text — not alarming.
- **Savings callout (when ER > 0.5%):** Nested box with `var(--green-bg)` background and `var(--green-border)`. This is the positive reframe — "here's what you could save." The green communicates opportunity, not fear.
- **Animation:** The fee dollar amount counter-animates when the ER slider changes (same animation as banner counter).
- **Only visible when "Customize Assumptions" is expanded.** Slides in smoothly with the accordion.

**✅ DECISION:** Insight-framed (not warning). Left-border accent in indigo. Green callout for savings opportunity when ER > 0.5%. Counter animation on fee amount.

---

## Debate 10: Advanced Panel — Customize Assumptions

**Jordan (Dev):** This panel contains: 6 rate sliders, 6 ER sliders, and 1 contribution increase slider. That's 13 sliders. If they're all visible at once, the panel is enormous.

**Jamie (UXR):** Group them by fund. Each fund gets a mini-card with its rate + ER side by side. The contribution increase is a separate group at the bottom.

**Dana (FA):** Show the net return (rate minus ER) next to each fund's sliders. This is the key teaching moment — users move the ER slider and watch the net return change in real time.

**Sam (UXD) — FINAL CALL:**

```
┌─ Customize Assumptions ─────────────────────────────────────────┐
│                                                                 │
│  CONTRIBUTION GROWTH                                            │
│  Annual Increase  [0%  ══●═══════]  3.0%                        │
│  ⓘ How much you increase contributions each year (salary growth)│
│                                                                 │
│  ── Fund Assumptions ────────────────────────────────────────── │
│                                                                 │
│  ● Total US Stock Market                    Net Return: 10.17%  │
│  Annual Return   [══════●═════]  10.2%                          │
│  Expense Ratio   [●══════════]  0.03%                           │
│                                                                 │
│  ● S&P 500                                  Net Return: 9.97%   │
│  Annual Return   [══════●═════]  10.0%                          │
│  Expense Ratio   [●══════════]  0.03%                           │
│                                                                 │
│  ● Bonds                                    Net Return: 4.47%   │
│  Annual Return   [══●═════════]   4.5%                          │
│  Expense Ratio   [●══════════]  0.03%                           │
│                                                                 │
│  ● HYSA                                     Rate: 4.5%          │
│  Annual Return   [══●═════════]   4.5%                          │
│  Expense Ratio   N/A                                            │
│                                                                 │
│  [Reset to Defaults]                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

- **Fund groups:** Only show sliders for SELECTED funds. If a user has 3 funds toggled, they see 3 groups. This keeps the panel manageable.
- **Net Return badge:** Positioned top-right of each fund group. Updates in real time as rate/ER sliders move. This is Dana's teaching moment — users see the impact of fees on returns as they drag.
- **Contribution Growth:** Top of panel, separated by a section label. This is a universal setting (not per-fund).
- **ⓘ hint:** Subtle inline help below the contribution increase slider.
- **Reset to Defaults:** Bottom of panel. Resets all rates and ERs to the presets from Section 4.1.
- **Accordion animation:** Smooth height transition, `350ms ease`. Same pattern as existing Advanced Settings accordion in the mortgage section.
- **Only visible funds:** If S&P 500 is not toggled on, its sliders don't appear. This prevents a 13-slider wall and focuses attention.

**✅ DECISION:** Dynamic — only shows sliders for selected funds. Net Return badge per fund. Contribution increase at top. Reset button at bottom.

---

## Debate 11: Data Table — Expandable Year-by-Year

**Jordan (Dev):** Same pattern as the existing 30-year table. Sticky header, alternating rows, horizontal scroll on mobile.

**Dana (FA):** Column headers must say "Projected" — not just "Total US Stock Market." The word must appear in the table context because users screenshot tables.

**Jamie (UXR):** Add the contributions column as the first data column (after Year). It grounds every row in reality. Users can always see "I put in $X, and it became $Y."

**Sam (UXD) — FINAL CALL:**

| Year | Your Contributions | Total US Stock (Projected) | Bonds (Projected) | HYSA (Projected) |
|------|--------------------|----------------------------|--------------------|--------------------|
| 1 | $16,000 | $17,412 | $16,526 | $16,738 |
| 5 | $40,000 | $54,328 | $44,712 | $45,884 |
| 10 | $70,000 | $128,940 | $90,128 | $93,840 |
| 25 | $160,000 | $847,214 | $296,164 | $298,206 |

- **"Your Contributions" column:** Left-aligned, `font-weight: 600`. Uses a subtle gray background to visually separate it from the projection columns.
- **Projection columns:** Dynamic — one per selected fund. Column header includes "(Projected)".
- **Year 25 (final row):** Highlighted with `var(--green-bg)` background.
- **Hover row:** `var(--surface-hover)` background in dark mode, light gray in light mode.
- **Toggle button:** "Show Year-by-Year Breakdown ▾" / "Hide Breakdown ▴". Uses existing accordion button pattern.
- **Mobile:** Horizontally scrollable. "Year" and "Contributions" columns are sticky-left for context as user scrolls right.

**✅ DECISION:** "Projected" in column headers. Contributions as first data column with distinct background. Final row highlighted. Mobile: sticky Year + Contributions columns.

---

## Debate 12: Mobile Layout

**Jamie (UXR):** Mobile is where we lose most users. The layout needs to deliver value above the fold — banner + chart visible without scrolling past inputs.

**Sam (UXD) — FINAL CALL:**

```
Mobile Stack (< 768px):
┌────────────────────────────┐
│  ═══ Section Header ═════  │
│  Investment Growth Calc    │
├────────────────────────────┤
│  ┌─ Summary Banner ─────┐ │
│  │  Projected value:     │ │
│  │  $847,214             │ │
│  │  after 25 years       │ │
│  │  $647K gains · $200K  │ │
│  └───────────────────────┘ │
│  ⓘ Projections use...     │
├────────────────────────────┤
│  ┌─ Stat Cards (stacked) ┐ │
│  │  4.2× growth multiple │ │
│  │  $847,214 projected   │ │
│  ├────────────────────────┤ │
│  │  $200,000 contributed │ │
│  └───────────────────────┘ │
├────────────────────────────┤
│  ┌─ Chart (full width) ──┐ │
│  │                        │ │
│  │  (min-height: 300px)   │ │
│  │                        │ │
│  └────────────────────────┘ │
├────────────────────────────┤
│  ▸ Adjust Inputs           │
│  ┌─ (collapsed accordion) ┐│
│  │  Initial Investment    ││
│  │  [═══●══]  $10,000     ││
│  │  Monthly Contribution  ││
│  │  [═●════]  $500        ││
│  │  Time Horizon          ││
│  │  [════●═]  25 yrs      ││
│  │                        ││
│  │  Fund Types            ││
│  │  [● Stock] [● Bonds]  ││
│  │  [● HYSA]             ││
│  │                        ││
│  │  ▸ Customize Assump.   ││
│  └────────────────────────┘│
├────────────────────────────┤
│  ▸ Year-by-Year Breakdown  │
└────────────────────────────┘
```

Key mobile decisions:
- **Inputs are BELOW the chart** in a collapsed accordion labeled "Adjust Inputs." This is the reverse of typical calculator layouts — but Sam's rationale is that the default values produce a useful chart immediately. Users see value before they need to configure anything.
- **Banner → Disclaimer → Stat Cards → Chart → Inputs → Table.** This ordering optimizes for "value above the fold."
- **Fund pills on mobile:** Wrap to 2 rows. Slightly smaller padding.
- **Chart touch:** Tooltip appears on tap and holds until user taps elsewhere or scrolls.
- **ℹ️ volatility popover:** Triggered by tap. Anchored below the icon, not centered on screen.

**✅ DECISION:** Results-first mobile layout. Inputs in collapsed accordion below chart. Touch-friendly tooltips.

---

## Debate 13: Micro-Interactions and Animation

**Taylor (Marketing):** Animated counter on the banner number. Chart lines draw in on load. These are the little things that make people go "oh this is nice."

**Jordan (Dev):** Chart.js has built-in draw-in animation. Counter animation is a custom `requestAnimationFrame` loop — I already have this in the mortgage section, just reuse it. Keep all animations under 500ms so they don't feel sluggish.

**Sam (UXD) — FINAL CALL:**

| Element | Animation | Duration | Easing | Trigger |
|---------|-----------|----------|--------|---------|
| Banner hero number | Counter (scrolling digits) | 400ms | ease-out | On any input change |
| Banner gains/contributions | Fade + slide up | 200ms | ease-out | On input change |
| Chart lines | Draw-in from left | 800ms | ease-in-out | On first render |
| Chart lines | Smooth morph | 300ms | ease-out | On input change |
| Fund pill toggle | Scale bounce | 150ms | spring | On click |
| Stat card numbers | Counter | 300ms | ease-out | On input change |
| Fee impact amount | Counter | 300ms | ease-out | On ER slider change |
| Advanced panel | Height expand | 350ms | ease | On toggle |
| Data table | Height expand | 350ms | ease | On toggle |
| Contributions dashed line | Draw-in (same as fund lines) | 800ms | ease-in-out | On first render |
| Gains fill (leading fund) | Fade in | 400ms | ease-out | After lines finish drawing |

- **Reduced motion:** All animations respect `prefers-reduced-motion: reduce`. When active, all animations are instant (0ms duration).
- **No animation on slider drag:** Values update instantly (debounced at 16ms / 60fps). No counter animation during continuous drag — only on release. This prevents nausea-inducing rapid counter animation.

**✅ DECISION:** Full animation spec above. Respect reduced motion. No counter animation during active slider drag.

---

## Debate 14: Empty / Zero / Edge States

**Jamie (UXR):** What happens when all fund types are deselected? When initial investment is $0? When time horizon is 1 year?

**Sam (UXD) — FINAL CALL:**

| State | Behavior |
|-------|----------|
| **All funds deselected** | Chart shows only the contributions dashed line. Banner says "Select a fund type to see projected growth." Stat cards show contributions only; returns card shows "—". |
| **Initial investment = $0, Monthly = $0** | Banner: "Enter an investment amount to see projections." Chart: empty with a centered message. |
| **Initial = $0, Monthly > $0** | Works normally. "Starting from scratch" is a valid scenario. |
| **Time horizon = 1 year** | Chart shows monthly granularity (12 data points instead of yearly). Table shows month-by-month. Banner shows 1-year projection. |
| **All rates set to 0%** | Lines overlap with contributions line. Banner: "At 0% return, your portfolio matches your contributions." Stat cards: growth multiple = 1.0×. |
| **ER higher than return rate** | Net return goes negative. Line goes below contributions line. Banner says "projected" with the lower number. Fee impact card highlights this explicitly. |

**✅ DECISION:** Graceful degradation for all edge states. Helpful inline messages, never broken/empty UI.

---

## Summary: Design Token Usage

For quick reference, all new CSS needed:

```css
/* New tokens for investment section */
:root {
    --invest-stock: #4f46e5;
    --invest-sp500: #2563eb;
    --invest-intl: #0d9488;
    --invest-bonds: #d97706;
    --invest-target: #e11d48;
    --invest-hysa: #64748b;
    --invest-contrib: rgba(100, 116, 139, 0.5);
    --invest-gains-fill: 0.06;  /* opacity for gains area fill */
}

[data-theme="dark"] {
    --invest-stock: #818cf8;
    --invest-sp500: #60a5fa;
    --invest-intl: #2dd4bf;
    --invest-bonds: #fbbf24;
    --invest-target: #fb7185;
    --invest-hysa: #94a3b8;
    --invest-contrib: rgba(148, 163, 184, 0.5);
}
```

All other styling reuses existing tokens (`--surface`, `--border`, `--text`, `--text-secondary`, `--radius`, `--shadow`, `--shadow-md`, typography scale, etc.).

---

## Appendix: Component Reuse Map

| Component | Reuse from Existing? | Notes |
|-----------|---------------------|-------|
| Card container | ✅ `.card` class | No changes |
| Slider + number input | ✅ `.input-group`, `.input-row` | Same pattern, horizontal layout |
| Accordion toggle | ✅ `.advanced-toggle` / `.advanced-body` | Same JS and CSS |
| Chart.js config | ✅ Existing chart setup | Add dashed line dataset, gains fill |
| Data table | ✅ `.data-table` | Dynamic columns, "Projected" headers |
| Counter animation | ✅ Existing `animateValue()` | Same function |
| Tabs (if used) | ✅ `.tabs`, `.tab-btn` | For section nav in header |
| Dark mode | ✅ `[data-theme="dark"]` | New tokens only for fund colors |

**New components needed:**
- Section divider (diamond ornament + typography)
- Fund type pill selector
- Fee impact card (with savings callout variant)
- ℹ️ popover (reusable for future tooltips)
- Gains area fill (Chart.js plugin or filler config)
- Horizontal input bar (3-column grid, responsive)

---

*This UI spec was generated via structured expert consensus review on March 14, 2026.*
*Team Lead: Sam (UXD) — final authority on all visual and interaction decisions.*
*Financial accuracy reviewed by: Dana (Financial Advisor SME).*
*Approved by: UXD ✅ | FA ✅ | PM ✅ | Dev ✅ | UXR ✅ | Marketing ✅*
