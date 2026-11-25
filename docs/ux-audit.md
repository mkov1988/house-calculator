# UX/UI Heuristic Audit — House Calculator (Merged)
### Mortgage vs. Invest + Investment Growth Calculator
**Methodology:** Nielsen Norman Group 10 Usability Heuristics
**Sources:** Audit A (3-pass NN/g evaluation, March 15 2026), Audit B (targeted heuristic spot-check), Owner review
**Date:** March 15, 2026
**Scope:** Full product — both calculator sections, navigation, information architecture, responsive states, edge cases

---

## 1. Executive Summary

**Overall Usability Score: 6.4 / 10** *(revised down from 7.1 after IA review)*

The product delivers a polished dual-calculator experience with strong real-time feedback, consistent design tokens, and thoughtful contextual help. The calculation logic is reliable, financial disclaimers are appropriately surfaced, and edge states are handled gracefully.

However, a foundational information architecture problem overshadows the component-level polish: **the two calculators have no connective hierarchy.** Switching between them feels like switching between two unrelated apps. There is no shared framing, no product-level narrative, no visual or conceptual bridge between "should I maximize my down payment or invest the difference?" and "how does compound growth work over time?" This is a Severity 4 issue — it undermines the product's core value proposition as an integrated financial decision-support tool.

Beyond the IA gap, three additional systemic issues meaningfully reduce usability: naming inconsistency between the tab label and section heading, missing reset mechanisms, and unexplained financial jargon at a primary decision point.

**Top 7 Critical Issues (merged & re-prioritized):**

| # | Issue | Heuristic | Severity | Source |
|---|-------|-----------|----------|--------|
| 1 | **No information architecture connecting the two calculators** — they feel like separate apps with no shared context, framing, or narrative flow | H4 Consistency / H2 Real World / H6 Recognition | **4** | Owner |
| 2 | Tab label "Compound Calculator" ≠ section title "Investment Growth Calculator" | H4 Consistency | 3 | Audit A |
| 3 | No section-level reset for Mortgage Calculator inputs | H3 User Control | 3 | Audit A |
| 4 | "Net Return (after dividend taxes)" — unexplained jargon at primary decision point | H2 Real World | 3 | Audit A |
| 5 | Mortgage chart lines (blue vs purple) rely on color alone — fails deuteranomaly accessibility | H1 Visibility / WCAG 2.1 | 3 | Audit B |
| 6 | Scenario 2 slider labels ($150K / $400K) are not editable — violates Fitts's Law for precision | H4 Consistency / H7 Flexibility | 3 | Audit B |
| 7 | Number inputs lack comma formatting — cognitive friction counting zeros on large values | H5 Error Prevention | 2 | Audit B |

**High-level recommendation:** The IA problem (#1) is the single highest-leverage fix. It reframes the entire product from "two calculators on one page" to "one integrated financial exploration tool." Address it before any marketing or wider distribution. The remaining severity-3 issues are 1–2 hour fixes each.

---

## 2. Methodology

**Scope:** The complete `index.html` single-page application, including the Mortgage vs. Invest calculator, Investment Growth Calculator, header navigation, responsive behavior from 375px to 1440px, dark mode, all edge states, and tooltip/help content.

**Audit A — Full 3-pass NN/g evaluation:**
- Pass 1 (Familiarization): Walked through both calculators as a typical first-time user.
- Pass 2 (Systematic): Evaluated every element against each of the 10 heuristics.
- Pass 3 (Cross-heuristic consolidation): Verified edge cases, reconciled overlapping findings, assigned severity.

**Audit B — Targeted spot-check:**
- Focused on visual accessibility, input interaction patterns, and data density issues.

**Owner review:**
- Identified the information architecture gap that both audits missed.

**Severity Scale (NN/g standard):**
- 0 = Not a usability problem
- 1 = Cosmetic only
- 2 = Minor problem (low priority)
- 3 = Major problem (high priority; affects many users)
- 4 = Usability catastrophe (must fix before launch)

---

## 3. Detailed Findings

---

### FINDING 0 — INFORMATION ARCHITECTURE (Severity 4)
*The structural foundation that both audits missed.*

**The Problem:**

The two calculators exist as disconnected islands. When a user switches from the mortgage calculator to the investment calculator, the experience is jarring — a fade transition replaces the entire page content with no shared context, no visual continuity, and no narrative thread. There is:

- **No product-level framing.** The header says "Mortgage vs. Invest" as if that's the entire product. The investment calculator has its own header ("Investment Growth Calculator") that could belong to any standalone app. Nothing ties them together as parts of a unified financial exploration experience.

- **No shared visual hierarchy.** The mortgage section uses a left-panel/right-panel grid layout. The investment section uses a full-width vertical stack. They look like they were designed by different teams for different products.

- **No conceptual bridge.** The mortgage calculator answers "should I put more down on my house or invest the difference?" The investment calculator answers "how does compound growth work?" These are deeply related questions — the answer to the first depends on understanding the second — but the product makes zero connection between them. A user who learns from the mortgage calculator that "investing the difference wins by $910K" has no way to explore *what* that invested money would grow into across different fund types without manually switching to an unrelated-feeling tool.

- **No shared state.** The two calculators share no inputs. The mortgage calculator assumes a 10% gross return; the investment calculator lets you pick from 6 fund types with specific returns. A user who adjusts the gross return in the mortgage section expects that assumption to carry over. It doesn't.

- **No navigation context.** When you're in the investment calculator, the only indication of location is the active tab. The header brand still says "Mortgage vs. Invest" — which no longer describes what you're looking at.

**Why This Matters:**

This isn't a cosmetic issue. The product's value proposition is helping users make *integrated* financial decisions about home buying and investing. Two disconnected calculators deliver less value than one calculator that connects the dots. The whole is supposed to be greater than the sum of its parts — right now it's less.

**Recommendations:**

1. **Product-level framing:** Rename the header from "Mortgage vs. Invest" to a product name that encompasses both tools (e.g., "House Calculator" or "Home & Wealth Planner"). Add a subtitle or tagline that frames the product's purpose: "Make data-driven decisions about your home and your money."

2. **Connective bridge:** Add a cross-link callout at the bottom of the mortgage results: "Want to explore how the invested difference grows? See it in the Investment Growth Calculator →" — and vice versa, add a link from the investment section back: "See how this fits into a home-buying decision →".

3. **Shared visual language:** Consider giving both sections a shared structural pattern — even if the layouts differ (which is appropriate given the different input:output ratios), they should share header typography, card styling, and spacing rhythms that make them feel like siblings, not strangers.

4. **Consider shared state (P1):** When the mortgage calculator determines an investment amount and gross return, pre-populate the investment calculator with those values. This creates a "drill-down" flow: mortgage decision → investment exploration.

5. **Persistent section indicator:** Add the active section name as a subtitle in the sticky header, so the user always knows where they are regardless of scroll position.

---

### H1 — Visibility of System Status

**Positives:**
- Both calculators update in real time on every slider drag (< 50ms).
- Counter-animation on slider release creates a satisfying "landing" without causing distraction during drag. Distinction between `input` (debounced) and `change` (animated) events is exactly right.
- "WINNER" badge and "+$910,105 Wealth Advantage" callout provide unambiguous outcome status.
- `prefers-reduced-motion` respected throughout.
- Section nav shows active underline state.

**Issues:**

- **[Severity 3] — Mortgage chart lines rely on color alone to distinguish data sets.** *(Audit B)*
  Location: Net Wealth and Liquidity charts — blue vs. purple lines.
  Violation: Adjacent hues fail WCAG 2.1 for deuteranomaly (red-green color blindness). Users with color vision deficiency cannot distinguish Scenario 1 from Scenario 2.
  Recommendation: Add distinct markers (circles vs. squares) or use a dashed stroke for one line. This also benefits grayscale printing.

- **[Severity 2] — Section switch provides no persistent location indicator beyond the nav tab.** *(Audit A)*
  Location: Header tab navigation.
  After fade transition, the only cue of location is the active underline on the tab. Users scrolled mid-page have no reminder of which calculator they're in.
  Recommendation: Add the active section name as a secondary label in the sticky app bar.

- **[Severity 1] — Dark mode toggle icon state is ambiguous.** *(Audit A)*
  Location: Header, top-right.
  The button renders "🌙" in light mode (correct — represents destination) but the convention is inconsistent with self-describing state. The `aria-label` should also update.
  Recommendation: Ensure icon toggles between 🌙 and ☀️ and `aria-label` updates accordingly. *(Note: code review shows this is already implemented in `setTheme()` — verify it works correctly.)*

---

### H2 — Match Between System and Real World

**Positives:**
- "What You Put In" is warm, plain-language, immediately comprehensible.
- "Invest the Difference" maps perfectly to the real-world concept.
- "How It Works" callout explains the core mechanic in natural language.
- Fund tickers ("e.g. VTSAX / VTI") give knowledgeable users recognition anchors.

**Issues:**

- **[Severity 3] — "Net Return (after dividend taxes): 9.55%" — unexplained jargon.** *(Audit A)*
  Location: Mortgage left panel, below gross return slider.
  Users who see 10% reduced to 9.55% without explanation may suspect a bug.
  Recommendation: Add a `?` tooltip: "We reduce your gross return by your dividend yield × your dividend tax rate to model the after-tax reality. You can adjust both in Advanced Settings."

- **[Severity 2] — "Opportunity Cost: -$910,105" uses economics jargon.** *(Audit A)*
  Location: Mortgage Scenario 1 result card.
  Recommendation: Replace with "Leaving $910,105 on the table vs. Scenario 2" or add parenthetical.

- **[Severity 2] — "Compound Calculator" tab label mismatch.** *(Audit A)*
  Location: Header nav tab vs. investment section `<h2>`.
  Recommendation: Unify naming. Use the same label in both places.

- **[Severity 1] — "P&I" abbreviation requires tooltip to resolve.** *(Audit A)*
  Recommendation: Spell out "Principal & Interest" in the label or add dotted-underline to signal expandability.

---

### H3 — User Control and Freedom

**Positives:**
- Investment Advanced panel has "Reset to Defaults" button.
- Fund pills toggle on/off instantly with no confirmation.
- Accordions open and close freely.

**Issues:**

- **[Severity 3] — No section-level reset for Mortgage Calculator.** *(Audit A)*
  Location: "Your Inputs" card — 12 input controls with no reset.
  Recommendation: Add "Reset to defaults" button. Default values are already in the HTML `value` attributes — 10 lines of JS.

- **[Severity 3] — Scenario 2 slider labels are not editable inputs.** *(Audit B)*
  Location: "$150,000" and "$400,000" text labels above the Scenario 2 slider.
  Violation: Dialing in an exact dollar amount via drag violates Fitts's Law. Power users cannot bypass the slider for precision.
  Recommendation: Convert the text labels into editable number inputs so users can type exact down payment amounts.

- **[Severity 2] — Scenario 2 slider has no labeled range boundaries.** *(Audit A)*
  Violation: No indication that most loans require 3.5–20% down. Users can set $0 down without realizing it's unrealistic.
  Recommendation: Add a note beneath the slider about minimum down payment requirements.

- **[Severity 1] — Investment "Reset to Defaults" doesn't reset contribution increase.** *(Audit A)*
  Recommendation: Include contribution increase slider in reset scope.

- **[Severity 1] — Fund pill interaction model is ambiguous (single-select vs. multi-select).** *(Audit B)*
  Recommendation: Add "(select multiple)" hint text or add checkmark icons to active pills.

---

### H4 — Consistency and Standards

**Positives:**
- Slider + number input pairing consistent across all inputs.
- `.card` component used consistently.
- Currency formatting consistent throughout.
- Chevron accordion pattern consistent.

**Issues:**

- **[Severity 4] — No information architecture hierarchy between sections.** *(Owner — see Finding 0)*

- **[Severity 3] — "Compound Calculator" ≠ "Investment Growth Calculator."** *(Audit A)*
  The highest-visibility naming inconsistency. Financial products rely on precise, consistent language.
  Recommendation: Unify naming.

- **[Severity 2] — Inconsistent progressive-disclosure patterns.** *(Audit A)*
  Mortgage uses tabs (Charts / Year-by-Year Data). Investment uses a collapsed toggle below the chart.
  Recommendation: Align patterns across sections.

- **[Severity 2] — Inconsistent help affordance ("?" vs. "ℹ️").** *(Audit A)*
  Recommendation: Create a single help-icon component used everywhere.

---

### H5 — Error Prevention

**Positives:**
- Default values are well-chosen and representative.
- Min/max constraints on all sliders.
- HYSA correctly shows "N/A" for expense ratio.
- All edge states produce graceful empty states, not NaN.

**Issues:**

- **[Severity 2] — Number inputs lack comma formatting.** *(Audit B)*
  Location: All large-number inputs (e.g., "750000", "10000").
  Violation: Users must count zeros mentally, increasing order-of-magnitude errors (Miller's Law).
  Recommendation: Auto-format displayed values with comma separators as the user types.

- **[Severity 2] — No soft warnings for unrealistic input ranges.** *(Audit A)*
  Recommendation: Add amber inline notes when values are outside realistic ranges (e.g., mortgage rate 0%, market return > 15%).

- **[Severity 2] — Negative net return (ER > gross return) is silently allowed.** *(Audit A)*
  Recommendation: Add red highlight to Net Return badge and inline note when it goes negative.

- **[Severity 1] — Number inputs flash intermediate values during typing.** *(Audit A)*
  Recommendation: Debounce number input changes by 300ms or apply on blur.

---

### H6 — Recognition Rather Than Recall

**Positives:**
- Fund pills with color dots create a learnable legend before the user sees the chart.
- Ticker examples act as recognition anchors.
- Stat card restates inputs so users don't have to scroll back.
- Net Return badge shows live calculations, eliminating mental math.

**Issues:**

- **[Severity 2] — Wealth Composition chart may render blank without clear empty state.** *(Audit B)*
  Recommendation: If values are zero, show baseline axis with "No data available yet" microcopy or skeleton loader.

- **[Severity 2] — "⚙" gear emoji doesn't convey clickability as an accordion toggle.** *(Audit A)*
  Recommendation: Replace with a text label "Customize" in accent color.

- **[Severity 2] — Data table toggle is below the fold, hard to discover.** *(Audit A)*
  Recommendation: Add a "View year-by-year data ↓" link inside the chart card header.

- **[Severity 2] — Investment banner lacks input summary.** *(Audit A)*
  Recommendation: Add "Based on $10,000 initial + $500/mo over 25 years" to the banner detail row.

---

### H7 — Flexibility and Efficiency of Use

**Positives:**
- Dual-input pattern (slider + number field) is textbook accelerator implementation.
- "Tip: Many people increase contributions yearly →" is a well-designed micro-accelerator.
- Fund pill interface allows rapid multi-fund comparison.

**Issues:**

- **[Severity 2] — No mechanism to share or export calculator state.** *(Audit A)*
  Recommendation: URL parameters for primary inputs ("Copy link to this scenario"). Marked P2 in spec — worth reconsidering as P1.

- **[Severity 2] — No keyboard navigation between tabs.** *(Audit A)*
  Recommendation: Implement ARIA `tablist`/`tab`/`tabpanel` pattern with arrow-key support.

---

### H8 — Aesthetic and Minimalist Design

**Positives:**
- Card-based layout cleanly separates content.
- Section divider provides clear visual separation.
- Investment banner is effectively minimal — exemplary information hierarchy.

**Issues:**

- **[Severity 2] — Investment stat cards partially duplicate banner information.** *(Audit A)*
  Recommendation: Streamline stat cards to show only what the banner doesn't (growth multiple, input breakdown).

- **[Severity 2] — "WINNER" badge introduces promotional language into an educational tool.** *(Audit A)*
  Recommendation: Replace with "+$910,105 ahead" or a neutral check-mark icon.

- **[Severity 2] — Cash flow table rows lack visual grouping.** *(Audit B)*
  Location: Monthly Cash Flow Comparison.
  Violation: Dense number wall without visual anchors. Eye struggles to track across rows.
  Recommendation: Add zebra striping or light background fill to subtotal/total rows for scannability.

- **[Severity 1] — "Returns shown are nominal" appears in two places redundantly.** *(Audit A)*
  Recommendation: Keep inline note under pills, remove from popover.

---

### H9 — Help Users Recognize, Diagnose, and Recover from Errors

**Positives:**
- "No funds selected" edge state: banner shows "—" with directive message.
- "$0 investment" edge state: clear prompt to enter an amount.
- No JavaScript errors in any tested edge state.

**Issues:**

- **[Severity 2] — Number inputs don't provide visible validation feedback.** *(Audit A)*
  Recommendation: Add red border or "Min/Max" note when value is out of range.

- **[Severity 1] — Negative net return scenario lacks a recovery-oriented message.** *(Audit A)*
  Recommendation: Add red danger style to badge and note: "ER exceeds return. Lower the expense ratio or raise the return."

---

### H10 — Help and Documentation

**Positives:**
- 17+ contextual tooltips in mortgage section — exemplary.
- "How It Works" callout explains core mechanic with dynamic values.
- Chart info popover explains methodology and limitations.

**Issues:**

- **[Severity 2] — Investment section has no "How It Works" equivalent.** *(Audit A)*
  Recommendation: Add a "How This Works" callout below the input bar explaining compound growth, expense ratios, and nominal vs. real returns.

- **[Severity 2] — Contribution increase slider hint is insufficient.** *(Audit A)*
  Recommendation: Expand to explain that 3% escalation can add $300K+ over 25 years.

- **[Severity 1] — Wealth Composition chart has no contextual help.** *(Audit A)*
  Recommendation: Add subtitle: "Home equity is only accessible by selling or refinancing; portfolio value is liquid."

---

## 4. Prioritized Recommendations

Sorted by Severity × Impact / Effort.

| Rank | Issue | Heuristic | Sev | Effort | Source |
|------|-------|-----------|-----|--------|--------|
| **1** | **Build information architecture connecting both calculators** | H4/H2/H6 | **4** | Medium–High | Owner |
| 2 | Rename tab to match section heading | H4 | 3 | Low (1 line) | Audit A |
| 3 | Add "Reset to Defaults" to Mortgage Calculator | H3 | 3 | Low (10 lines) | Audit A |
| 4 | Add tooltip for "Net Return (after dividend taxes)" | H2/H10 | 3 | Low (1 tooltip) | Audit A |
| 5 | Add distinct markers/dashes to mortgage chart lines for colorblind accessibility | H1/WCAG | 3 | Low | Audit B |
| 6 | Make Scenario 2 slider labels editable inputs | H3/H7 | 3 | Low–Medium | Audit B |
| 7 | Add comma formatting to all number inputs | H5 | 2 | Medium | Audit B |
| 8 | Add "How It Works" callout to Investment section | H10 | 2 | Low | Audit A |
| 9 | Unify help affordance ("?" and "ℹ️") | H4/H6 | 2 | Low–Medium | Audit A |
| 10 | Replace "WINNER" badge with quantified advantage | H8 | 2 | Low | Audit A |
| 11 | Add soft warnings for unrealistic input ranges | H5 | 2 | Medium | Audit A |
| 12 | Add zebra striping to cash flow table | H8 | 2 | Low | Audit B |
| 13 | Add input summary to investment banner | H6 | 2 | Low | Audit A |
| 14 | Expand contribution increase hint | H10 | 2 | Low | Audit A |
| 15 | Replace "Opportunity Cost" with plain language | H2 | 2 | Low | Audit A |

---

## 5. Overall Score & Next Steps

### Score by Heuristic

| Heuristic | Score (0–10) | Key Factor |
|-----------|-------------|------------|
| H1 Visibility of Status | 7 | Real-time updates excellent; chart color accessibility gap |
| H2 Real World Match | 6 | Strong copy; jargon and naming issues |
| H3 User Control | 5 | Fund pills reversible; no mortgage reset, imprecise slider |
| H4 Consistency | 4 | **IA gap is the dominant issue**; naming and pattern inconsistencies compound it |
| H5 Error Prevention | 6 | Good defaults; no number formatting, no soft warnings |
| H6 Recognition | 7 | Fund dots and stat summaries strong; discoverability gaps |
| H7 Flexibility/Efficiency | 6 | Dual-input excellent; no sharing, no editable slider labels |
| H8 Aesthetic | 7 | Clean and focused; stat duplication and promotional language |
| H9 Error Recovery | 8 | Edge states handled gracefully |
| H10 Help | 7 | Mortgage tooltips exemplary; investment section missing equivalent |

**Composite Score: 6.4 / 10**

### Next Steps

1. **Immediate (this sprint):** Fix the IA hierarchy — add product-level framing, cross-links between sections, persistent section indicator in header. Fix tab naming. Add mortgage reset. Add Net Return tooltip. Combined effort: ~4–6 hours. Combined impact: transforms the product from "two calculators" to "one integrated tool."

2. **Next sprint:** Chart accessibility (dashed lines), editable Scenario 2 labels, number formatting, "How It Works" for investment section, help affordance unification. Effort: 6–8 hours.

3. **User testing (before v1.0):** 5-participant moderated test. Key hypotheses:
   - Do users understand the relationship between the two calculators without prompting?
   - Do users discover "Customize Assumptions" organically?
   - Do users attempt to reset inputs, and how?
   - Do users find the year-by-year table without prompting?

---

## 6. Appendix — Positive Highlights Worth Preserving

- **Dual slider + number input pattern** — Best-in-class accelerator. Do not remove.
- **`animateValue()` on `change` only (not `input`)** — Prevents nausea-inducing counter cycling during drag. Keep this pattern.
- **17+ contextual tooltips (mortgage)** — Gold standard. Investment section should reach parity.
- **Edge-state graceful degradation** — All six investment edge cases produce helpful messages.
- **`prefers-reduced-motion` support** — Proactive accessibility. Preserve and extend.
- **Projection language compliance** — "Projected," "estimated gains," "(Projected)" in headers. Maintain this discipline.
- **Fund pill color system** — Color dots matching chart lines. Extend to future additions.

---

*Merged audit prepared March 15, 2026. Sources: Audit A (NN/g 3-pass), Audit B (targeted spot-check), Owner IA review.*
