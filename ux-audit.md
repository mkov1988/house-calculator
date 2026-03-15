# UX/UI Heuristic Audit — House Calculator
### Mortgage vs. Invest + Investment Growth Calculator
**Methodology:** Nielsen Norman Group 10 Usability Heuristics
**Evaluator:** Senior UX/UI Audit (NN/g methodology, 3-pass evaluation)
**Date:** March 15, 2026
**Scope:** Full product — both calculator sections, navigation, responsive states, edge cases

---

## 1. Executive Summary

**Overall Usability Score: 7.1 / 10**

The product delivers a sophisticated, visually polished dual-calculator experience with strong real-time feedback, a clear visual hierarchy, and thoughtful contextual help. The calculation logic is reliable, the design tokens are consistently applied, and financial disclaimers are appropriately surfaced. For a single-page financial tool targeting a financially literate but non-expert audience, this is a competent first build.

However, three systemic issues meaningfully reduce usability. First, the navigation label "Compound Calculator" (header tab) conflicts with the section's own heading "Investment Growth Calculator" — a direct H4 (Consistency) violation that will confuse users and undermine trust in a product where precision of language is the core value proposition. Second, neither calculator provides a visible, discoverable reset mechanism at the section level (H3, User Control and Freedom) — a critical omission in a tool designed for what-if exploration. Third, "Net Return (after dividend taxes)" is displayed as a persistent label in the mortgage section without context or explanation, stranding users unfamiliar with dividend tax drag at a pivotal moment in their decision-making (H2, Real World Match).

**Top 5 Critical Issues:**

| # | Issue | Heuristic | Severity |
|---|-------|-----------|----------|
| 1 | Tab label "Compound Calculator" ≠ section title "Investment Growth Calculator" | H4 Consistency | 3 |
| 2 | No section-level reset for Mortgage Calculator inputs | H3 User Control | 3 |
| 3 | "Net Return (after dividend taxes)" — unexplained jargon at primary decision point | H2 Real World | 3 |
| 4 | Inconsistent interactive-help affordance: "?" tooltips (mortgage) vs "ℹ️" emoji button (investment) | H4 Consistency / H6 Recognition | 2 |
| 5 | Investment section has no "How It Works" equivalent; mortgage section does | H10 Help | 2 |

**High-level recommendation:** Address severity-3 issues before any marketing or wider distribution. The naming inconsistency (#1) is a one-line fix with outsized trust impact. The reset pattern (#2) can be added to both sections with minimal effort. The jargon (#3) requires a tooltip or inline explanation.

---

## 2. Methodology

**Scope:** The complete `index.html` single-page application, including the Mortgage vs. Invest calculator, Investment Growth Calculator, header navigation, responsive behavior from 375px to 1440px, dark mode, all edge states, and tooltip/help content.

**Passes:**
- **Pass 1 (Familiarization):** Walked through both calculators as a typical first-time user completing the primary task (estimating home-buying financial impact; projecting investment growth). Explored all inputs, toggled all accordions, triggered all edge states, and toggled dark mode.
- **Pass 2 (Systematic Heuristic Inspection):** Evaluated every screen element, label, microcopy string, feedback mechanism, error state, and responsive behavior against each of the 10 heuristics in sequence.
- **Pass 3 (Cross-heuristic consolidation):** Verified edge cases (all funds deselected, $0 investment, negative net return, 1-year horizon), reconciled overlapping findings, and assigned final severity ratings.

**Severity Scale (NN/g standard):**
- 0 = Not a usability problem
- 1 = Cosmetic only
- 2 = Minor problem (low priority)
- 3 = Major problem (high priority; affects many users)
- 4 = Usability catastrophe (must fix before launch)

**Source materials reviewed:** Live screenshots (both sections), full `index.html` source code including all labels, ARIA attributes, tooltip content, edge-case logic, and CLAUDE.md spec.

---

## 3. Detailed Findings

---

### H1 — Visibility of System Status
*The design should always keep users informed about what is going on, through appropriate feedback within a reasonable amount of time.*

**Positives:**
- Both calculators update in real time on every slider drag (< 50ms), giving immediate visual confirmation that input changes are registered.
- Counter-animation (`animateValue()`) on slider release creates a satisfying, legible "landing" on the final value without causing distraction during drag. Distinction between `input` (debounced, no animation) and `change` (animated) events is exactly right.
- The "WINNER" badge on Scenario 2 and the "+$910,105 Wealth Advantage" callout provide unambiguous outcome status.
- `prefers-reduced-motion` is respected — users with vestibular disorders receive instant updates with no animation.
- The section navigation shows an underline active state that updates to reflect which section tab is currently selected.

**Issues:**

- **[Severity 2] — Section switch provides no persistent location indicator beyond the nav tab.**
  Location: Header tab navigation.
  Violation: After the fade transition to Investment Growth, the only visual cue of location is the active underline on the "Compound Calculator" tab. The section heading is below the fold on many viewports. If the user scrolls down and returns focus to the top, there is no persistent breadcrumb or section label visible in the sticky app bar itself. Users scrolled mid-page have no reminder of which calculator they are in.
  Impact: Low on desktop (section title is visible); moderate on mobile (header occupies most of the above-fold real estate; user may lose context).
  Recommendation: Add the active section name as a secondary label inside the sticky `.app-bar` (e.g., faint subtitle beneath the brand name that reads "Mortgage vs. Invest" or "Investment Growth"). This is a < 1-hour CSS change.

- **[Severity 1] — Dark mode toggle button state not self-describing.**
  Location: Header, top-right.
  Violation: The button renders "🌙" in both light and dark mode. In dark mode it should show "☀️" (or some alternate state indicator) to communicate the current theme and the action available. Currently, the icon represents the destination state rather than the current state, which is confusing.
  Impact: Cosmetic; most users infer dark-mode behavior from convention, but it violates the principle of continuous status communication.
  Recommendation: Toggle the icon between "🌙" (currently light mode → click to go dark) and "☀️" (currently dark mode → click to go light). The `aria-label` should also update to "Switch to dark mode" / "Switch to light mode" accordingly.

---

### H2 — Match Between the System and the Real World
*The design should speak the users' language. Use words, phrases, and concepts familiar to the user, rather than internal jargon.*

**Positives:**
- "What You Put In" (stat card label) is warm, plain-language, and immediately comprehensible to non-experts.
- "Invest the Difference" as the Scenario 2 name maps perfectly to the real-world concept and is better than any technical label would be.
- The "How It Works" callout in the mortgage section explains the core mechanic in natural language with bolded amounts matching the inputs.
- Fund tickers ("e.g. VTSAX / VTI") give knowledgeable users immediate recognition anchors without alienating novices.
- Financial disclaimer language ("not financial advice", "do not guarantee future results") matches the language of the industry, matching user expectations for a financial tool.

**Issues:**

- **[Severity 3] — "Net Return (after dividend taxes): 9.55%" — unexplained jargon at a primary decision point.**
  Location: Mortgage Calculator left panel, below the Expected Market Return slider.
  Violation: The label "Net Return (after dividend taxes)" appears in small gray text immediately below the gross return input. The target audience includes first-time buyers and people early in their investment journey who know "10% S&P 500 average" but do not understand why that is silently reduced by "dividend taxes." The tooltip on the gross return slider explains historical averages but says nothing about the tax drag calculation. Users who notice the discrepancy (10% → 9.55%) may distrust the tool rather than understand it.
  Impact: Erodes trust. A user who sees their 10% expectation silently reduced to 9.55% without explanation may suspect a bug or a biased assumption baked in.
  Recommendation: Add a `?` tooltip or inline expand to this label explaining the tax drag in plain language: "We reduce your gross return by your dividend yield × your dividend tax rate to model the after-tax reality. You can adjust both in Advanced Settings." This matches the existing tooltip pattern exactly.

- **[Severity 2] — "Opportunity Cost: -$910,105" in the Scenario 1 callout uses economics jargon.**
  Location: Mortgage section, Scenario 1 result card, orange/red callout.
  Violation: "Opportunity Cost" is a precise economics term that many users — particularly first-time home buyers — will not have internalized. The negative value and red styling communicate negativity, but the label does not tell users what opportunity they missed.
  Impact: Users may read the number but not understand what it means relative to their decision.
  Recommendation: Replace or supplement with plain language: "Leaving $910,105 on the table vs. Scenario 2" or add a parenthetical: "Opportunity Cost (what you give up by maximizing your down payment)."

- **[Severity 2] — "Compound Calculator" as the navigation tab label creates a terminology mismatch.**
  Location: Header tab nav, second tab.
  Violation: The section title reads "Investment Growth Calculator." The tab in the navigation reads "Compound Calculator." These are two different names for the same section. "Compound" is shorthand (technically correct — it refers to compound interest), but it is a significantly less descriptive and less user-centered label than "Investment Growth." Users unfamiliar with the term "compound calculator" as a product category may not predict what they will find.
  Impact: Direct H4 violation also (see below), but the H2 impact is user misdirection — a user scanning the header for "Investment" or "Fund Growth" will not associate that with "Compound."
  Recommendation: Rename the tab to "Investment Growth" to match the section heading. One-line change.

- **[Severity 1] — "P&I" abbreviation used in some contexts without full expansion.**
  Location: Monthly Cash Flow Comparison table, "Mortgage P&I" row.
  Violation: "Mortgage P&I" expands to "Principal & Interest" via tooltip, but the label itself still requires tooltip activation to resolve. Users who do not know "P&I" and do not hover the tooltip will see an unexplained abbreviation.
  Impact: Minor; the tooltip resolves it, but relies on user-initiated discovery.
  Recommendation: Spell out "Mortgage (Principal & Interest)" in the label, or at minimum make the abbreviation visually distinct (underline or dotted border) to signal it is expandable.

---

### H3 — User Control and Freedom
*Users often choose system functions by mistake and will need a clearly marked "emergency exit." Support undo and redo.*

**Positives:**
- The Investment Growth Calculator's Advanced panel includes a visible "Reset to Defaults" button scoped to the fund assumptions.
- Fund pills can be toggled off as easily as they are toggled on — no confirmation required, and re-adding is instant.
- The data table and year-by-year breakdown accordion can be opened and closed freely.

**Issues:**

- **[Severity 3] — No section-level "Reset to Defaults" for Mortgage Calculator inputs.**
  Location: Mortgage Calculator left panel ("Your Inputs" card).
  Violation: The mortgage section has 12 input controls with no reset mechanism. A user who has explored a wide range of scenarios (e.g., changed home price to $2M, mortgage rate to 2%, and maintenance to 0%) has no single action to return to a meaningful baseline. They must manually re-adjust each slider or refresh the page (losing all context).
  Impact: Exploration is the primary use case. Without a reset, users who get lost in edge-case parameter combinations are stuck — they must remember the original values or reload. This adds cognitive friction to the core task.
  Recommendation: Add a "Reset to defaults" link or small ghost button at the top of the "Your Inputs" card. The existing JavaScript already has default values in every input's `value` attribute — a reset function is 10 lines of code.

- **[Severity 2] — "Scenario 2: Invest the Difference" slider has no labeled range boundaries.**
  Location: Scenario 2 box, split slider between 🏠 and 📈.
  Violation: The slider moves continuously between full down payment (Scenario 1-equivalent) and $0 down. Emoji labels (🏠 and 📈) communicate direction but not range values. Users cannot tell at a glance what the minimum allowable down payment would be in reality (e.g., 3.5% FHA minimum), making it possible to inadvertently set up an unrealistic scenario with no feedback.
  Impact: Users may build a scenario around a 0% down payment without realizing it is not achievable in most lending contexts, leading to plans based on false premises.
  Recommendation: Add a small note beneath the slider: "Note: Most conventional loans require 5–20% down" or surface a soft warning when the down payment drops below 3.5% of home price. This is error prevention (H5) as well.

- **[Severity 1] — The Investment Advanced panel "Reset to Defaults" only resets fund rate/ER assumptions, not contribution increase.**
  Location: Investment Calculator, Advanced panel, "Reset to Defaults" button.
  Violation: The reset button resets per-fund rates and expense ratios but the Annual Contribution Increase slider is a sibling element in the same panel. After reset, if the user had set a 3% annual increase, it remains at 3% — creating an inconsistency where the fund assumptions are reset but the contribution compounding is not.
  Impact: Minor; affects users who use the full advanced panel and expect a clean slate.
  Recommendation: Include the contribution increase slider in the reset scope.

---

### H4 — Consistency and Standards
*Users should not have to wonder whether different words, situations, or actions mean the same thing. Follow platform conventions.*

**Positives:**
- Slider + number input pairing is applied consistently across all 12+ input pairs in both sections. Users learn the pattern once and apply it everywhere.
- The `.card` visual component is used consistently for all grouped content in both sections.
- Currency formatting (`$X,XXX`) is consistent throughout.
- Active tab underline styling is consistent between the section nav and the chart tabs.
- The "▾" chevron for accordions is used on both Advanced panels and the data table toggle.

**Issues:**

- **[Severity 3] — "Compound Calculator" (tab) ≠ "Investment Growth Calculator" (section heading) — critical internal naming inconsistency.**
  Location: Header nav tab vs. investment section `<h2>`.
  Violation: This is the highest-impact consistency violation in the product. The section is named twice in the same viewport and the names differ. Nielsen's H4 explicitly states "users should not have to wonder whether different words... mean the same thing." Here the same thing is literally called two different names within the same product.
  Impact: Trust erosion. Financial products especially rely on precise, consistent language. A user who sees two names for the same section may question whether the tool itself is reliable.
  Recommendation: Unify on "Investment Growth" for both the tab and the section heading. "Compound" can be retained as a descriptive subtitle if needed (e.g., "Investment Growth — compound interest projections") but should not be the primary label.

- **[Severity 2] — Inconsistent progressive-disclosure patterns across sections.**
  Location: Mortgage section uses tabs ("Charts" / "Year-by-Year Data") for the same category of content (chart vs. table) that the Investment section exposes via a collapsed toggle ("Show Year-by-Year Breakdown").
  Violation: Two different UI patterns for structurally equivalent content. In the mortgage section, charts and data coexist as tabs (equal hierarchy). In the investment section, the chart is always visible and the table is hidden. Users who complete the mortgage section first will look for a tab to access the investment table and will not immediately scan for a collapsed toggle below the chart.
  Impact: Moderate; users familiar with one section may not find the equivalent control in the other.
  Recommendation: Align patterns. Either move the investment table into a tab (Charts / Year-by-Year Data) to match the mortgage section, or move the mortgage section to a toggle-style reveal. The tab pattern is recommended as it suggests content parity.

- **[Severity 2] — Interactive help affordance is inconsistent between sections.**
  Location: Mortgage section uses styled "?" tooltip icons. Investment section uses an "ℹ️" emoji button on the chart header.
  Violation: Two different visual languages for "click here for more information." The "?" tooltip uses a consistent `<span class="tooltip-wrap">` pattern across all 17 mortgage inputs. The investment info button is a standalone `<button>` with an emoji label styled differently. Users cannot apply a single learned affordance.
  Impact: Minor confusion, but signals production polish gap.
  Recommendation: Create a single `.tooltip-icon` or `.info-btn` component and apply it everywhere contextual help is offered. The investment chart's ℹ️ and the mortgage's "?" should render identically.

- **[Severity 1] — Banner left-border color updates to the leading fund color, but the stat card hero value also updates its color to the leading fund. These two colored elements use different CSS mechanisms.**
  Location: Investment section — `.invest-banner` left border vs. `.invest-stat-multiple` text color.
  Violation: Minor internal implementation inconsistency (one is set via `style.borderLeftColor`, the other via `style.color`). Both visually tie to the same data concept (leading fund) but are not thematically grouped in the visual layout. A user scanning the page may not intuit that both elements reference the same fund.
  Impact: Very low; cosmetic and implementation-level.
  Recommendation: Consider adding a small leading-fund label or badge near both elements, or group them visually to make the shared data source explicit.

---

### H5 — Error Prevention
*Even better than good error messages is a careful design which prevents a problem from occurring in the first place.*

**Positives:**
- Default values are well-chosen and representative (Home Price $750K, Rate 8%, Market Return 10%, Initial Investment $10K, Monthly $500, Horizon 25 years) — they represent realistic use cases, not extremes.
- Min/max constraints on all sliders and number inputs prevent extreme out-of-bounds values.
- HYSA correctly shows "N/A" for expense ratio input — users cannot accidentally enter an ER for a fund type that doesn't have one.
- Tooltip on "Expected Market Return" explicitly anchors to "S&P 500 has historically averaged ~10%/year," preventing wildly optimistic entries.
- Edge-state logic is robust: no-funds selected and $0 investment both produce graceful empty states rather than NaN or rendering crashes.

**Issues:**

- **[Severity 2] — Mortgage rate and investment return inputs allow values that produce misleading results without any soft warning.**
  Location: Mortgage Calculator Advanced Settings — "Mortgage Interest Rate" (0–12%) and "Expected Market Return" (0–20%).
  Violation: A user can set mortgage rate to 0% (unrealistic) or market return to 20% (extreme outlier) and receive results that look authoritative. There is no soft constraint or warning to flag that these are edge-case assumptions.
  Impact: Users anchored on optimistic or unrealistic inputs may make real-world decisions based on outputs that assume conditions that almost never occur.
  Recommendation: Add a soft warning (non-blocking, amber-colored inline note) when values are outside realistic ranges. Example: market return > 15% → "Returns above 15% are rare historically. Adjust with caution." Mortgage rate at 0% → "Mortgage rates below 3% are uncommon in most markets." This is a warning, not a block.

- **[Severity 2] — Scenario 2 slider allows $0 down payment with no real-world context warning.**
  Location: Scenario 2 split slider.
  Violation: Most US mortgages require a minimum 3.5–5% down payment. A user who drags the slider to $0 down constructs a scenario that is not achievable under any standard lending program, with no indication from the tool.
  Impact: Users could plan their finances around a scenario they cannot execute.
  Recommendation: Show a soft inline note when down payment falls below 3.5% of home price: "Note: Most loans require at least 3.5% down ($26,250 at this home price)." This is a three-line JS check, not a full validation system.

- **[Severity 2] — Investment section: negative net return (ER > gross return) is silently allowed.**
  Location: Investment Calculator Advanced panel, per-fund ER slider.
  Violation: A user can set an expense ratio higher than the gross return, producing a negative net return. The line on the chart will drop below the contributions line, which is technically correct — but there is no visual warning to flag this as an unusual configuration. A user who accidentally sets ER to 3% on a bond fund (0.045 gross) receives a devastating-looking projection with no explanation.
  Impact: Users may be alarmed or confused by a chart that shows their investment shrinking below their contributions without understanding why.
  Recommendation: The Net Return badge in the advanced panel already shows the calculated net return — add a red highlight (danger color) and brief inline note when it goes negative: "Warning: fees exceed returns. Net return is negative."

- **[Severity 1] — Number inputs accept free-text entry that can momentarily produce NaN or empty states during typing.**
  Location: All number inputs (mortgage and investment).
  Violation: While the JS handles the edge case gracefully (no crashes observed), typing in a number field naturally passes through intermediate states (e.g., user types "1" in a field that should read "10000"). During this transient state, calculations may flash intermediate results.
  Impact: Very low; users with fast interaction recover immediately. But it can produce a momentary confusing number.
  Recommendation: Debounce number input changes by at least 300ms or apply them only on blur to avoid transient calculation states.

---

### H6 — Recognition Rather Than Recall
*Minimize the user's memory load by making objects, actions, and options visible.*

**Positives:**
- Fund pills display a color dot alongside the fund name, so the chart legend is learnable before the user even looks at the chart. Color-to-fund mapping is established at the interaction layer, not only in the chart.
- Fund ticker examples ("e.g. VTSAX / VTI") act as recognition anchors for users who know funds by ticker symbol.
- The stat card "WHAT YOU PUT IN" summary ($10,000 initial + $500/mo × 25 years) restates the inputs so the user does not have to scroll back to the input panel to remember what they entered.
- Net Return badge in the advanced panel shows the calculated net return live alongside the rate and ER sliders, eliminating the need for mental math.
- The "How It Works" callout in the mortgage section restates the core logic that drives the two-scenario comparison, making it unnecessary for users to hold the model in working memory.

**Issues:**

- **[Severity 2] — The "⚙" gear emoji on the Investment Advanced toggle is not a standard interactive affordance; it does not convey clickability.**
  Location: Investment Calculator, "⚙ Customize Assumptions" accordion toggle.
  Violation: The gear emoji is decorative but commonly associated with settings or modals, not with an expand/collapse pattern. The "▾" chevron communicates the accordion behavior — the gear is redundant and may confuse users about whether clicking it opens a settings dialog vs. expands inline content.
  Impact: Low; most users will follow the chevron cue. But the gear introduces an extra visual signal that does not map cleanly to the interaction.
  Recommendation: Replace the emoji gear with a simple text label "Customize" styled in `var(--accent)` to create a clear call-to-action that pairs naturally with the chevron.

- **[Severity 2] — The "Show Year-by-Year Breakdown" toggle is positioned after the chart, making it hard to discover without scrolling.**
  Location: Investment Calculator, below the chart card.
  Violation: The table reveal trigger is below the chart, which is below the stat cards, which is below the input bar. On a 1080p screen the toggle is below the fold. Users looking for detailed projection data must scroll to find it, and may not know it exists.
  Impact: Users seeking validation of the projected numbers (a natural behavior in a financial tool) will not see the data table option until they scroll considerably.
  Recommendation: Add a small text link or icon button ("View year-by-year data ↓") inside the chart card header, adjacent to the existing ℹ️ button. This surfaces discoverability without moving the table.

- **[Severity 2] — The Investment section has no persistent reminder of what inputs are driving the current projection.**
  Location: Investment banner and stat cards.
  Violation: The banner shows "$808,707 after 25 years in Total US Stock Market" — but does not include the assumed inputs (Initial $10K, Monthly $500). The mortgage section's "How It Works" callout does this well by dynamically including the per-scenario contribution amounts. The stat card's "What You Put In" partially addresses this, but it is below the banner and not visually linked to it.
  Impact: A user who returns to the Investment section after using the Mortgage section may not immediately know whether the banner reflects their inputs or the defaults.
  Recommendation: Add a one-line input summary to the banner detail row: "Based on $10,000 initial + $500/mo over 25 years." This is a small text element that would prevent the banner from appearing decontextualized.

---

### H7 — Flexibility and Efficiency of Use
*Accelerators may speed up the interaction for the expert user such that the system can cater to both inexperienced and experienced users.*

**Positives:**
- The dual-input pattern (slider + number field) is the single best flexibility feature in the product. Novices drag; experts type exact values. This is a textbook accelerator implementation.
- The "Tip: Many people increase contributions yearly →" hint is clickable and opens the advanced panel, scrolling focus to the contribution increase slider — a well-designed micro-accelerator for users who notice it.
- The fund pill interface allows rapid multi-fund comparison without navigating away or opening a modal.
- Sliders use appropriate step values (e.g., $500 for initial investment, $50 for monthly contribution) that balance precision with usability.

**Issues:**

- **[Severity 2] — No mechanism to share or export a specific calculator state (URL parameters, clipboard copy, PDF export).**
  Location: Both calculators.
  Violation: Expert users and comparison shoppers will want to share a scenario with a partner, financial advisor, or return to it later. Currently, page refresh resets all inputs to defaults (there is no state persistence).
  Impact: Limits the tool's utility for the most engaged users, who are precisely the audience that has the highest intent to act on the information. A financial advisor audience (e.g., using this to model options for a client) would be particularly affected.
  Recommendation: Even a basic "Copy link to this scenario" feature (URL params for primary inputs) would provide outsized value relative to implementation complexity. This is marked as P2 in the spec — it is worth reconsidering as P1 given the target audience.

- **[Severity 2] — No keyboard navigation between tabs or sections.**
  Location: Section nav tabs, chart tabs.
  Violation: Tab buttons are `<button>` elements, which are keyboard-accessible, but there is no `keydown` handler for arrow-key navigation between tabs — the standard keyboard interaction model for tablist patterns (ARIA `role="tablist"`).
  Impact: Keyboard-only users and screen reader users must Tab through each nav button individually rather than using arrow keys to switch sections.
  Recommendation: Implement the ARIA `tablist`/`tab`/`tabpanel` pattern with arrow-key support. The visual styling already matches the tablist pattern — the ARIA roles are missing.

- **[Severity 1] — No keyboard shortcut or access key for commonly repeated actions (e.g., toggle dark mode, reset to defaults).**
  Location: Global.
  Violation: Power users who use this tool repeatedly (running multiple scenarios) must mouse to the theme toggle, reset button, etc. No keyboard shortcuts exist.
  Impact: Very low for a public-facing financial calculator. Relevant primarily for internal team use or power-user audiences.
  Recommendation: Low priority. Could add `accesskey` attributes or a `?` keyboard shortcut dialog in a future pass.

---

### H8 — Aesthetic and Minimalist Design
*Dialogues should not contain information which is irrelevant or rarely needed. Every extra unit of information competes with relevant information.*

**Positives:**
- The card-based layout cleanly separates inputs from outputs and charts from data tables.
- The design token system ensures visual consistency without over-engineering.
- The section divider (ornamental diamond + horizontal rule) provides clear visual separation between the two calculators without heavy UI chrome.
- The "PROJECTED PORTFOLIO VALUE" banner in the Investment section is effectively minimal — one eyebrow label, one large number, one supporting label. This is a model of focused information hierarchy.
- The disclaimer text is appropriately small and unobtrusive without being hidden.

**Issues:**

- **[Severity 2] — Investment stat cards partially duplicate information already visible in the banner.**
  Location: Investment section — "PROJECTED RETURNS" stat card vs. the banner.
  Violation: The banner shows: projected value, fund name, estimated gains, total contributions. The stat cards show: total contributed (banner also shows this), projected value (banner), estimated gains (banner). The only net-new information in the stat cards is the growth multiple (4.5×) and the detailed breakdown lines. The rest is a visual echo of the banner.
  Impact: The page becomes denser without adding information. Users scan the stat cards expecting new information and find the same numbers they just read.
  Recommendation: Streamline the stat cards to show only what is not in the banner. The "What You Put In" card is useful (it shows the input breakdown: $10K initial + $500/mo × 25 years, which the banner does not). The "Projected Returns" card should lead exclusively with the growth multiple (the one number not in the banner) and show only the supplementary detail below, not the full-value repeat.

- **[Severity 2] — The "WINNER" badge on the Scenario 2 result card introduces promotional visual language into a calculation tool.**
  Location: Mortgage section, Scenario 2 result card.
  Violation: "WINNER" is a marketing/gamification label. In a financial decision-support tool, it implies a recommendation that the product should not be making. The tool correctly shows Scenario 2 outperforming Scenario 1 under default assumptions — but "winner" implies universal superiority, which is not true (e.g., users who value housing security over wealth maximization, users in high-appreciation real estate markets, users with inconsistent income).
  Impact: May reinforce user bias toward Scenario 2 without examining whether it is appropriate for their situation. Undermines the "educational purposes only" framing.
  Recommendation: Replace "WINNER" with "+$910,105 ahead" or a check-mark icon. The wealth advantage callout below already quantifies the difference — the badge adds emotional framing without analytical value.

- **[Severity 1] — Disclaimer text appears in two places in the investment section — inline under the fund pills ("Returns shown are nominal") and in the disclaimer paragraph and again in the chart popover.**
  Location: Investment section.
  Violation: "Returns shown are nominal (not adjusted for inflation)" appears as a small italic note under the pills and again (expanded) in the chart info popover. The core message is repeated.
  Impact: Redundancy adds visual noise. The inline note under the fund pills and the popover serve slightly different contexts (one is always visible, one is on demand) but they are not meaningfully differentiated.
  Recommendation: Keep the inline note under the pills. Remove the inflation sentence from the popover (it is already visible). The popover content is strong without it.

- **[Severity 1] — The Liquidity Over Time chart's "Why This Matters" callout box uses orange styling in a results panel dominated by blue/purple scenario colors.**
  Location: Mortgage section, Liquidity chart.
  Violation: The `.how-it-works` callout is orange-tinted. In the chart immediately above, the two scenario lines are blue and purple. The orange callout creates a color discontinuity that draws the eye disproportionately relative to its informational importance.
  Impact: Very minor visual distraction.
  Recommendation: Consider using a neutral (`--accent-light` or `--surface` with `--border`) background for the callout, or adopt the indigo `--accent-light` to unify it with the accent color used elsewhere.

---

### H9 — Help Users Recognize, Diagnose, and Recover from Errors
*Error messages should be expressed in plain language, precisely indicate the problem, and constructively suggest a solution.*

**Positives:**
- The edge state for "no funds selected" is handled gracefully: the banner shows "—" with the message "Select a fund type above to see projected growth." This is plain language, locates the solution, and does not require the user to understand what went wrong.
- The "$0 investment" edge state shows "Enter an investment amount to see projections" — again, directive and clear.
- The negative net return scenario produces a chart where the line visibly drops below contributions, which is technically correct and communicates the result (though it needs a warning — see H5).
- No JavaScript errors or NaN values surface to users in any tested edge state.

**Issues:**

- **[Severity 2] — Number inputs do not provide visible validation feedback when a user enters a value that violates min/max constraints by typing.**
  Location: All number inputs.
  Violation: If a user types "-5000" into the Initial Investment field, the browser's native number validation may prevent submission in a form context, but in this single-page app there is no form submission — the value is read on `input` event. Values that fall outside min/max are handled by the slider clamping, but the number input does not visually indicate it rejected or clamped the value.
  Impact: Users who type extreme values may not know whether their input was accepted as-is or silently clamped.
  Recommendation: Add a brief red border or inline "Min: $0 / Max: $1,000,000" note when the input value is outside range. Alternatively, on blur, snap the displayed value back to the clamped value with a visual pulse to signal correction.

- **[Severity 1] — The investment section's "negative net return" scenario (ER > gross return) lacks a recovery-oriented message.**
  Location: Investment Calculator, Advanced panel when expense ratio exceeds gross return.
  Violation: As noted in H5, the tool allows this configuration silently. From an H9 perspective, even if this configuration is intentional (academic/exploratory), users who encounter it by accident need help diagnosing the cause and recovering (e.g., reducing the ER or increasing the gross return).
  Impact: Low in practice (ER > gross return requires deliberate misconfiguration), but the recovery path should be indicated.
  Recommendation: The Net Return badge in the advanced panel already turns negative — add a red danger style to the badge text and a one-line note: "ER exceeds return. Lower the expense ratio or raise the return."

---

### H10 — Help and Documentation
*Help and documentation should be easy to search, focused on the user's task, list concrete steps, and not be too large.*

**Positives:**
- The tooltip pattern on 17+ mortgage inputs is exemplary: each tooltip is task-focused (explains the field's meaning in context of the user's decision), plain-language, and concise (1–2 sentences max). No jargon left unexplained.
- The "How It Works" callout in the mortgage results panel provides inline contextual documentation at exactly the point of decision — this is the NN/g ideal of help that is "focused on the user's task."
- The investment chart info popover explains the projection methodology and limitations clearly, including the critical "negative years" caveat.
- The global footer disclaimer ("educational purposes only... consult a financial advisor") meets the minimum legal/contextual disclosure standard without dominating the UI.

**Issues:**

- **[Severity 2] — The Investment Growth Calculator section has no "How It Works" equivalent.**
  Location: Investment section.
  Violation: The mortgage section's "How It Works" callout explains the core model mechanic in plain language with dynamic values. The investment section has no analogous explanation. Users new to compound interest or expense ratios have no contextual anchor beyond the tooltips and the disclaimer.
  Impact: The investment section is conceptually more complex than the mortgage section (6 fund types, expense ratios, contribution escalation, compound growth). Users need more help here, not less.
  Recommendation: Add a "How This Works" callout (using the same `.how-it-works` CSS component) below the input bar. Content example: "This calculator compounds your initial investment and monthly contributions at each fund's historical average return, minus its annual expense ratio. Contributions are assumed to be made monthly. All values are nominal — not adjusted for inflation."

- **[Severity 2] — The Advanced panel's annual contribution increase slider has only a one-line hint ("ⓘ How much you increase contributions each year (salary growth)") — insufficient for users who don't know what contribution escalation means for their projections.**
  Location: Investment Calculator, Advanced panel, Annual Contribution Increase field.
  Violation: "Salary growth" is given as the explanation, but the user needs to understand the model implication: that this does not just increase their raw contributions — it compounds the contribution amount annually. The difference between 0% and 3% escalation can be $300,000 over 25 years, and the user has no way to understand that impact from the hint alone.
  Impact: Users may set a 3% increase casually without understanding it changes their projected outcome by 25–30%.
  Recommendation: Expand the hint or add a tooltip: "Setting this to 3% means your $500/mo contribution becomes $515 in Year 2, $530 in Year 3, etc., matching typical salary growth. This significantly increases your projected final value."

- **[Severity 1] — No help or guidance for interpreting the Wealth Composition bar chart.**
  Location: Mortgage section, "Wealth Composition (Year 30)" chart.
  Violation: The bar chart shows Home Equity vs Portfolio for each scenario at year 30. There is no subtitle or callout explaining what the ratio of Home Equity to Portfolio means in terms of financial decision-making (e.g., home equity is illiquid; portfolio is liquid). The Liquidity chart covers this concept, but the Wealth Composition chart is presented without analogous context.
  Impact: Minor; users who have already read the Liquidity chart callout will carry that understanding forward. But first-time users reading the charts in order might find the Wealth Composition chart visually interesting but unclear in implication.
  Recommendation: Add a one-line subtitle: "Home equity is only accessible by selling or refinancing; portfolio value is liquid."

---

## 4. Prioritized Recommendations

Sorted by Severity × Impact / Effort.

| Rank | Issue | Heuristic | Severity | Effort | Expected Impact |
|------|-------|-----------|----------|--------|----------------|
| 1 | Rename "Compound Calculator" tab → "Investment Growth" | H4 | 3 | Low (1 line) | High — eliminates the most visible trust-eroding inconsistency |
| 2 | Add "Reset to Defaults" button to Mortgage Calculator inputs | H3 | 3 | Low (10 lines JS) | High — restores exploration freedom for primary use case |
| 3 | Add tooltip/explanation for "Net Return (after dividend taxes)" | H2, H10 | 3 | Low (1 tooltip) | High — converts jargon into a teaching moment at the decision point |
| 4 | Add "How It Works" callout to Investment section | H10 | 2 | Low (static HTML) | Medium — reduces cognitive overhead for compound interest novices |
| 5 | Unify interactive help affordance ("?" vs "ℹ️") | H4, H6 | 2 | Low–Medium | Medium — professional polish, reduces interaction learning cost |
| 6 | Add soft warning when sliders enter unrealistic ranges | H5 | 2 | Medium | Medium — prevents decisions based on impossible assumptions |
| 7 | Replace "WINNER" badge with quantified advantage label | H8 | 2 | Low (CSS/copy) | Medium — removes promotional framing from an educational tool |
| 8 | Surface the data table toggle link inside chart card header | H6 | 2 | Low | Medium — surfaces a hidden feature without scroll |
| 9 | Expand contribution increase slider hint with projection context | H10 | 2 | Low (copy edit) | Medium — prevents naive 3% entry without understanding impact |
| 10 | Replace "Opportunity Cost" with plain-language label | H2 | 2 | Low (copy edit) | Low–Medium — reduces comprehension barrier at key result moment |

---

## 5. Overall Score & Next Steps

### Score by Heuristic

| Heuristic | Score (0–10) | Key Factor |
|-----------|-------------|------------|
| H1 Visibility of Status | 8 | Real-time updates excellent; dark mode icon ambiguous |
| H2 Real World Match | 7 | "Invest the Difference" great; dividend tax jargon and tab naming drag it down |
| H3 User Control | 6 | Fund pills reversible; no mortgage reset is a meaningful gap |
| H4 Consistency | 6 | Slider pattern excellent; tab naming and help affordance inconsistency notable |
| H5 Error Prevention | 7 | Good defaults and edge states; no soft warnings for unrealistic inputs |
| H6 Recognition | 8 | Fund dots, tickers, stat card summaries all strong |
| H7 Flexibility/Efficiency | 6 | Dual-input accelerator excellent; no sharing, no keyboard nav for tabs |
| H8 Aesthetic | 8 | Clean, focused; stat card duplication and "WINNER" badge minor issues |
| H9 Error Recovery | 8 | Edge states handled gracefully; no input validation feedback |
| H10 Help | 7 | Mortgage tooltips exemplary; investment section missing How It Works |

**Composite Score: 7.1 / 10**

### Next Steps

1. **Immediate (this sprint):** Fix severity-3 issues — tab label rename, mortgage reset button, Net Return tooltip. Combined effort: under 2 hours. Combined trust impact: high.

2. **Next sprint:** Add investment "How It Works" callout, align interactive help affordance pattern, expand contribution increase hint. Effort: 4–6 hours. Impact: meaningfully reduces comprehension barrier for less financially literate users.

3. **User testing (recommended before v1.0 launch):** Run a 5-participant moderated usability test with the primary persona (first-time buyer in 28–40 age range, moderately financially literate). Specific hypotheses to test:
   - Do users understand the "Net Return (after dividend taxes)" label without assistance?
   - Do users discover the "Customize Assumptions" panel organically, or do they miss it?
   - Do users find the "Year-by-Year Breakdown" toggle without prompting?
   - Do users attempt to reset calculator inputs and, if so, how?

4. **A/B test (post-launch):** "WINNER" badge vs. "+$910,105 ahead" label on Scenario 2. Measure whether the badge increases or decreases trust scores in a post-task survey.

5. **Accessibility pass:** Implement ARIA `tablist`/`tab`/`tabpanel` roles with arrow-key navigation on both tab groups. Add `aria-describedby` linking inputs to their tooltip content. Verify focus state visibility on all interactive elements in both light and dark modes.

---

## 6. Appendix

### A. Severity-3 Issues — Stakeholder Quick Reference

| ID | Location | Issue | Heuristic |
|----|----------|-------|-----------|
| A1 | Header tab nav | "Compound Calculator" ≠ "Investment Growth Calculator" | H4 |
| A2 | Mortgage "Your Inputs" card | No section-level reset to defaults | H3 |
| A3 | Mortgage left panel, below gross return slider | "Net Return (after dividend taxes)" unexplained | H2 |

### B. Positive Highlights Worth Preserving

- **Dual slider + number input pattern** — Best-in-class accelerator implementation. Do not remove number inputs to simplify the UI.
- **`animateValue()` on `change` only (not `input`)** — Sophisticated distinction that prevents nausea-inducing counter cycling during drag. Keep this pattern for all future animated values.
- **17+ contextual tooltips in the mortgage section** — This is the gold standard for contextual help in a financial calculator. The investment section should reach parity.
- **Edge-state graceful degradation** — All six investment edge cases produce helpful, directive empty-state messages. This prevents user confusion without requiring error dialogs.
- **`prefers-reduced-motion` support** — Proactive accessibility that goes beyond most comparable tools. Preserve and extend to any future animations.
- **Language compliance** — "Projected," "estimated gains," "(Projected)" in column headers — the financial hedging language is applied consistently and correctly throughout. Maintain this discipline.
- **Fund pill color system** — Color dots on pills that match chart lines create a cohesive visual legend system learned at the interaction layer. Extend this pattern to any future fund or scenario additions.

---

*Report prepared March 15, 2026 — NN/g heuristic evaluation methodology, 3-pass review.*
