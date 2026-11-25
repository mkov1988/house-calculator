# Feature Spec: Investment Growth Calculator

**Version:** 2.0
**Date:** March 14, 2026
**Status:** Draft — Consensus Spec (UXD-Led Review)
**Parent Product:** House Calculator (`index.html`)

---

## Expert Panel — Pre-Consensus Debate

Before the spec was written, six domain experts reviewed the feature request and argued their positions. **Sam (UXD) leads this team — all experts report to Sam, and Sam has final call on all contested decisions.** Sam weights Dana's (Financial Advisor) input most heavily, as financial accuracy and user trust are inseparable from usability in a financial tool. Debates and final calls are documented here for traceability. Skip to **Section 1** for the final consensus spec.

---

### The Cast

| Handle | Role | Reports To | Incentive / Bias |
|--------|------|-----------|-----------------|
| **Sam (UXD)** | UX Design Lead | — | Driving user value and usability |
| **Dana (FA)** | Financial Advisor SME | Sam | Financial accuracy, realistic expectations, fiduciary-grade trust |
| **Alex (PM)** | Product Manager | Sam | Ship fast, keep scope tight, prove value with metrics |
| **Jordan (Dev)** | Frontend Developer | Sam | Clean code, no new dependencies, < 50ms perf budget |
| **Jamie (UXR)** | UX Researcher | Sam | User mental models, education, accessibility |
| **Taylor (Marketing)** | Growth / Marketing | Sam | Virality, shareability, SEO, memorable outcomes |

---

### Debate 1: Scope — Standalone Tool or Integrated Section?

**Alex (PM):** New `<section>` in existing `index.html`. Not a new page. We keep users on the same URL and don't fragment the experience.

**Taylor (Marketing):** Agree, but needs its own anchor (`#investment-calculator`) for deep linking and sharing. Update the meta tags too.

**Jordan (Dev):** Simple anchor link in the header is low-effort. Full tab nav with JS state management is more work.

**Jamie (UXR):** Users in testing consistently missed content below the fold. An anchor link they have to discover doesn't cut it. We need explicit navigation.

**Dana (FA):** No strong opinion on page architecture, but the investment section should feel distinct from the mortgage section — different context, different decision. Users should not confuse "mortgage vs. invest" with "which fund should I invest in." If the two sections blur together, users may apply mortgage-section assumptions (e.g., the specific S1/S2 framing) to the investment section where they don't apply.

**Sam (UXD) — FINAL CALL:** Dana's right that these are cognitively different decisions. We go single page with sticky tab navigation in the header — "Mortgage vs. Invest" and "Investment Growth" as distinct, named sections. The nav must make the context switch clear. Visual separator between sections (not just whitespace — a gradient divider or section header with distinct background). Jordan, scope the tab nav as P0. Jamie, test the section transition for cognitive clarity in usability review.

**✅ DECISION:** Single `index.html`, sticky tab nav, `id="investment-calculator"`, visual section divider, meta tags updated.

---

### Debate 2: Fund Types — How Many, How Named?

**Alex (PM):** Three fund types for V1. Total Stock Market, Bonds, S&P 500. Ship fast.

**Taylor (Marketing):** People don't say "Total Stock Market." They say "index fund." Also we need HYSA as a baseline — it shows why investing matters. That's the hook.

**Dana (FA):** Several issues with the proposed fund list. First, S&P 500 and Total Stock Market are highly correlated — showing both on a chart gives users a false sense of "diversification" when the return profiles are nearly identical. Second, the return rates need to be historically defensible. I'd recommend using 30-year trailing annualized returns, net of typical expense ratios. Here are defensible defaults:

| Fund Type | Gross Historical (30yr trailing) | Typical Expense Ratio | Net Default |
|-----------|----------------------------------|----------------------|-------------|
| Total US Stock Market | 10.2% | 0.03% | 10.2% |
| S&P 500 | 10.0% | 0.03% | 10.0% |
| International Developed | 5.8% | 0.06% | 5.7% |
| US Bond Market | 4.5% | 0.03% | 4.5% |
| Target Date 2050 | 8.2% | 0.12% | 8.1% |
| High-Yield Savings | 4.5% (current) | N/A | 4.5% |

Third — and this is critical — **expense ratios matter enormously over 25-30 years.** A 1% expense ratio on an actively managed fund can eat 25-30% of your final wealth. If we don't show this, we're doing users a disservice. I'd argue for an expense ratio input, or at minimum, showing the impact of fees.

**Jamie (UXR):** Dana's point about expense ratios is exactly the kind of thing our personas miss. Jordan (first-time homebuyer) has no idea what an expense ratio is. Priya (FIRE) knows but wants to model it. We need to surface it, but progressively — not in the default view.

**Sam (UXD) — FINAL CALL:** Dana's perspective changes this substantially, and I trust it. Here's what we're doing:

1. **6 fund types**, all toggleable. Default shows 3: Total US Stock Market, US Bond Market, HYSA. S&P 500, International, and Target Date are available but off by default. Dana's right that S&P 500 and Total Market overlap — but Priya will want both, so we keep it available.
2. **Default return rates** use Dana's net-of-fees numbers. These are the "honest defaults."
3. **Expense ratio** becomes a visible field in the Advanced toggle — one slider per fund, defaulting to the typical index fund fee. When users crank it up to 1%+ (active fund territory), the chart updates and the impact is visible. This is a **P0** — Dana convinced me this is core to user value, not a nice-to-have.
4. **Labels** use plain English with ticker examples in tooltips (Taylor's right about naming). But we add a subtitle under each: the trailing return period and source, e.g., "30-year trailing annualized return."

**✅ DECISION:** 6 fund types, Dana's net-of-fees defaults, expense ratio as P0 (in Advanced toggle), plain English labels with historical source annotation.

---

### Debate 3: Inputs — What Does the User Control?

**Alex (PM):** Four inputs: lump sum, monthly contribution, time horizon, fund selector. Keep it tight.

**Dana (FA):** I want to add one more core input: **Annual contribution increase** (as a percentage). In reality, most people increase their contributions over time as their salary grows — typically 2-3% per year. A static $500/month for 30 years is unrealistic and undersells the outcome. This is how real financial planners model it.

**Taylor (Marketing):** That's a great shareable insight — "increasing your contribution by just 3% per year adds $X to your outcome." Love it.

**Jordan (Dev):** Adds a loop variable to the calculation engine. Instead of a closed-form formula, I'd need to iterate month-by-month. Not hard, but it changes the math from O(1) to O(n). Still well within 50ms for 50 years of monthly data.

**Jamie (UXR):** I'm worried about input overload. We already have 4 core inputs + fund selector + advanced toggle. Adding "annual contribution increase" to the main view is one too many for Jordan (first-time homebuyer persona). Can we put it in the Advanced section?

**Sam (UXD) — FINAL CALL:** Dana's point about realistic modeling is important — this is a tool people use to make real financial decisions. But Jamie's right about cognitive load. Here's the call:

- **P0 core inputs (always visible):** Initial Investment, Monthly Contribution, Time Horizon, Fund Type Selector. That's 4 — clean and focused.
- **P0 advanced inputs (behind "Customize" toggle):** Per-fund return rate sliders, per-fund expense ratio sliders, AND annual contribution increase (single slider, default 0%, range 0-10%, step 0.5%).
- The contribution increase is in Advanced because it's powerful but not essential for first contact. Priya will find it; Jordan won't be overwhelmed by it.

**Taylor (Marketing):** Can we at least add a hint — "Tip: Most people increase their contributions over time →" — that nudges users toward the Advanced toggle?

**Sam (UXD):** Yes, but it's a subtle inline hint below the Monthly Contribution slider, not a banner. We don't upsell features in a financial tool.

**✅ DECISION:** 4 core inputs + fund selector visible. Advanced toggle reveals: per-fund rates, per-fund expense ratios, annual contribution increase. Subtle hint nudges users toward Advanced.

---

### Debate 4: Outputs / Chart — What Story Do We Tell?

**Taylor (Marketing):** The headline number — "Your $500/month grows to $847,000" — is the shareable moment. Big summary banner above the chart, same pattern as the mortgage section.

**Dana (FA):** That banner, as described, is irresponsible. Showing a single large number with no context about risk, variability, or the fact that this is a projection — not a guarantee — trains users to think investing is deterministic. Every real financial projection comes with a disclaimer and context. I'd require:

1. The banner must include the word "projected" or "estimated."
2. A persistent disclaimer visible without scrolling: "Based on historical averages. Actual returns will vary. Past performance does not guarantee future results."
3. The **contributions line** must be visible on the chart — a dashed line showing what they put in vs. what the market gave them. This grounds the user. Without it, the growth feels like magic money, and they don't internalize that the market could also go down.

**Jamie (UXR):** The contributions line is brilliant. It answers the question users always have: "How much of this is mine vs. how much is growth?" It's also the single most educational element in the tool.

**Jordan (Dev):** Contributions line is trivial — it's just a linear function. Easy to add as a dashed dataset in Chart.js.

**Alex (PM):** The disclaimer feels heavy. Do we really need it on a personal calculator? We're not a brokerage.

**Dana (FA):** Yes, Alex. Precisely because we're not a brokerage, we have more responsibility to be clear, not less. A brokerage user knows they're looking at projections. A first-time homebuyer using a pretty web tool may take these numbers at face value and make a $500K decision based on them. The disclaimer is non-negotiable in my assessment.

**Sam (UXD) — FINAL CALL:** Dana wins this one, and it's not close. Here's the output spec:

1. **Summary banner** uses the word "projected": "Your projected Total Stock Market value: $847,214 after 25 years." Not "grows to" — that implies certainty.
2. **Contributions dashed line on chart** is P0. Always visible. Legend label: "Your Contributions." This is the most important educational element in the entire section.
3. **Disclaimer** is persistent, compact, and styled as a subtle footnote row below the banner — not a modal, not a popup, not dismissable. One sentence: "Projections use historical averages and do not guarantee future results. Actual returns will vary year to year."
4. **Tooltip on chart hover** shows: Year, projected value per fund, your contributions to date, and the gain/loss vs. contributions.

**Taylor (Marketing):** Can I at least get the "vs. HYSA" comparison in the banner? "Projected $847,214 in stocks vs. $183,424 in savings" is still a powerful shareable frame.

**Sam (UXD):** Yes — but only when HYSA is one of the selected funds. We don't fabricate comparisons. And the word "projected" stays on both numbers.

**✅ DECISION:** Banner says "projected," contributions line is P0 on chart, non-dismissable one-line disclaimer below banner, tooltip shows gains vs. contributions.

---

### Debate 5: Expense Ratio — How Prominent?

**Dana (FA):** This is the single most impactful thing a retail investor can control — not market returns, not timing, but fees. A 0.03% index fund vs. a 1.0% actively managed fund over 30 years is a six-figure difference on a moderate portfolio. I want a dedicated callout card showing: "Fees cost you $X over [time horizon] years." It should update in real time when users adjust expense ratios.

**Alex (PM):** That's scope creep. The expense ratio is already in the Advanced toggle. A separate card is gold-plating.

**Jordan (Dev):** It's one more derived value — `FV(low_fee) - FV(high_fee)`. Trivial to compute. The card itself is a reuse of the existing stat card pattern.

**Jamie (UXR):** I tested a "cost of fees" callout in a similar tool last year. Users who saw it were 3x more likely to remember the concept of expense ratios in a follow-up survey a week later. It's the highest-leverage educational element after the contributions line.

**Sam (UXD) — FINAL CALL:** Dana and Jamie together make this a clear P0. But I want to be careful about information overload. Here's how we handle it:

- **When "Customize" is OFF:** Expense ratios use defaults silently. No fee card shown. The user doesn't need to think about fees to get value from the tool.
- **When "Customize" is ON:** The fee card appears between the stat cards and the chart. It shows: "At [X]% fees, you pay an estimated $[Y] in fees over [Z] years" for the leading fund. If the user has raised any fund's expense ratio above 0.5%, add a second line: "Switching to a 0.03% index fund would save you $[delta]."

This is progressive disclosure done right — zero noise for casual users, high-value insight for engaged users.

**✅ DECISION:** Fee impact card is P0 but only surfaces when Advanced is toggled on. Shows estimated fee cost and, when applicable, the savings from switching to a low-cost fund.

---

### Debate 6: Integration with Existing Mortgage Calculator

**Taylor (Marketing):** Cross-sell moment — after the mortgage section, show "Want to see what happens if you invest your monthly savings?" with a pre-filled link to the investment section.

**Alex (PM):** Agree. Monthly contribution could default to the S1 investment contribution ($2,393 in default scenario).

**Dana (FA):** I actually like this connection — it answers the natural next question after the mortgage decision. But the pre-fill must be labeled clearly. Users should understand why the field has a number in it and feel empowered to change it. No "magic numbers" that feel authoritative but are just carried over from a different context.

**Jamie (UXR):** Pre-filling from another section is confusing when it works wrong. A "Pre-filled from your Mortgage Calculator inputs" banner with a clear dismiss/edit option is essential.

**Sam (UXD) — FINAL CALL:** Dana's framing is right — the journey is natural and valuable. But pre-fill is high risk for confusion. Here's the phasing:

- **P0:** Investment calculator defaults are fully independent. No pre-fill. No confusion.
- **P1:** A callout card at the bottom of the mortgage section: "Curious how your investment grows over time? Explore the Investment Growth Calculator →". No pre-fill — just a contextual nudge.
- **P2:** URL param pre-fill with a labeled, dismissable banner explaining where the numbers came from. Only after we've validated the P1 callout performs.

**✅ DECISION:** P0 is independent. P1 is contextual callout (no pre-fill). P2 is pre-fill with explanation banner.

---

### Debate 7: Risk Context and User Trust (NEW — Dana-Initiated)

**Dana (FA):** I want to raise something nobody else has. This tool shows smooth exponential curves. Real markets don't work that way. The S&P 500 lost 37% in 2008 and 34% in March 2020. If users see a smooth upward line and invest their savings expecting that trajectory, then experience a 30% drawdown in year 3, they'll panic-sell and lock in losses. We have a responsibility to set expectations about volatility.

**Alex (PM):** We are absolutely not building a Monte Carlo simulator in V1.

**Jordan (Dev):** Agreed. Volatility bands or simulations are a completely different engineering problem.

**Dana (FA):** I'm not asking for Monte Carlo. I'm asking for one sentence of educational context. A tooltip or a small info icon next to the chart that says something like: "This chart shows average projected growth. In reality, returns vary significantly year to year — including periods of negative returns. Long time horizons historically smooth out this volatility." That's it. One sentence.

**Jamie (UXR):** This is the kind of micro-education that builds trust. Users don't need to understand standard deviation. They need to know the line isn't a promise. One sentence does that.

**Taylor (Marketing):** Honestly, this also helps us. If someone screenshots the chart and shares it, having a visible disclaimer makes us look credible, not naive.

**Sam (UXD) — FINAL CALL:** Dana, this is exactly why you're on this team. The smooth curve is the biggest UX lie in every compound interest calculator on the internet, and acknowledging it costs us nothing while building enormous trust. Here's the spec:

1. **Info icon (ℹ️) next to the chart title** that expands a tooltip: "This chart shows projected growth based on historical average annual returns. Actual year-to-year returns vary significantly and include negative years. Historically, longer time horizons have reduced the impact of short-term volatility."
2. **The disclaimer below the banner** (from Debate 4) covers the legal angle. This tooltip covers the educational angle.
3. **P2 consideration:** Volatility bands (showing best/worst historical 25-year periods) — architecturally, this just means storing a couple more data points per fund type. Jordan, design the data model to accommodate this later.

**✅ DECISION:** Info tooltip on chart explaining volatility is P0. Volatility bands are P2 but data model should accommodate them.

---

### Debate 8: Mobile Experience

**Jordan (Dev):** Existing mobile pattern works — summary banner, chart, collapsed accordion for inputs. I can reuse the CSS and toggle logic.

**Jamie (UXR):** Summary banner must be readable at 375px without truncation. "Projected" language + dollar amount + time horizon is a lot of text for small screens.

**Dana (FA):** On mobile, the disclaimer must still be visible without extra interaction. Don't hide it in a tooltip that requires hover — mobile users can't hover.

**Sam (UXD) — FINAL CALL:** Mirror existing mobile pattern: summary banner → disclaimer (persistent, always visible) → chart → collapsed controls. Banner copy for mobile gets a two-line treatment — dollar amount on line 1, context on line 2. Jamie, test at 375px before sign-off. Dana, the disclaimer is always-visible text, never hover-dependent.

**✅ DECISION:** Existing mobile stack pattern. Two-line banner for small screens. Disclaimer is persistent text, not hover-only. Test at 375px.

---

## Section 1: Problem Statement

Personal finance tools for investing are either too simple (a single "how much will $X grow?" calculator with no context about risk, fees, or fund choice) or too complex (full portfolio simulators that assume financial literacy). Users of this app have already shown intent around long-term financial planning by engaging with the mortgage calculator. They are ready to ask the next question: *"If I invest consistently over time, what does that actually look like — and does it matter which fund type I choose?"*

Most compound interest calculators commit two sins: they show a single scenario with a single assumed return rate, and they present growth as a smooth certainty. This leaves users unable to compare asset classes, understand the compounding cost of fees, or develop realistic expectations about market variability.

**Cost of not solving this:** Users who want to go deeper leave the app for generic calculators that don't carry their context, don't explain fees, and don't set honest expectations. We lose the session, the return visit, and — more importantly — the opportunity to help users make better-informed decisions.

---

## Section 2: Goals

| # | Goal | Type | Metric | Target |
|---|------|------|--------|--------|
| 1 | Help users build accurate intuition about compound growth across fund types | User | % of sessions that toggle at least one fund type | > 60% within 60 days |
| 2 | Educate users about the compounding cost of fees | User | % of users who engage with "Customize" and see the fee impact card | > 20% of sessions |
| 3 | Set honest expectations — users should understand projections are not guarantees | User/Trust | % of users who interact with the volatility info tooltip | > 15% of sessions |
| 4 | Extend average session time | Business | Avg. time-on-page | +90 seconds vs. current baseline |
| 5 | Create a shareable, credible investment projection | Business/Growth | Share events (URL copy, screenshot, social) | > 8% of sessions |
| 6 | Serve the power user (Priya persona) without overwhelming the casual user (Jordan persona) | User | Advanced toggle usage rate between 20-40% (not too low = hidden, not too high = forced) | 20–40% of engaged users |

---

## Section 3: Non-Goals

| Non-Goal | Rationale |
|----------|-----------|
| Portfolio allocation modeling (e.g., 60/40 split) | Separate product — requires multi-asset input, rebalancing logic, and correlation modeling (Dana) |
| Tax-advantaged account comparison (Roth IRA, 401k) | Tax law complexity varies by income, state, filing status; requires legal review we haven't scoped (Dana) |
| Monte Carlo / volatility simulation | Engineering complexity is 5-10x; P2 volatility bands are the stepping stone (Jordan, Dana) |
| Real stock/fund data or live rates | Introduces API dependency and data freshness risk; V1 is intentionally offline/static (Jordan) |
| Withdrawal / retirement drawdown modeling | Distinct use case from accumulation; scope for a future "Retirement Planner" section (Alex) |
| Financial advice or recommendations | We are an educational tool, not a fiduciary. We show math; we never say "you should buy X" (Dana — non-negotiable) |

---

## Section 4: Feature Specifications & Inputs

### 4.1 Fund Type Presets

Defaults sourced from 30-year trailing annualized returns as of 2025, net of typical expense ratios. Source annotation displayed in UI.

| # | Label (Display) | Ticker Example (Tooltip) | Gross 30yr Return | Typical ER | Net Default | Default Shown? |
|---|-----------------|--------------------------|-------------------|-----------|-------------|----------------|
| 1 | Total US Stock Market | e.g. VTSAX / VTI | 10.2% | 0.03% | 10.2% | ✅ Yes |
| 2 | S&P 500 Index | e.g. VOO / FXAIX | 10.0% | 0.03% | 10.0% | ❌ No |
| 3 | International Developed | e.g. VXUS / FZILX | 5.8% | 0.06% | 5.7% | ❌ No |
| 4 | US Bond Market | e.g. BND / VBTLX | 4.5% | 0.03% | 4.5% | ✅ Yes |
| 5 | Target Date 2050 | e.g. VFIFX | 8.2% | 0.12% | 8.1% | ❌ No |
| 6 | High-Yield Savings (Baseline) | e.g. Marcus, SoFi | 4.5% (current) | N/A | 4.5% | ✅ Yes |

> **FA Note (Dana):** HYSA rate is a current snapshot, not a historical average. HYSA tooltip must read: "Current rate as of early 2026. HYSA rates fluctuate with the federal funds rate — adjust to match your current rate." This is the one preset that users should be encouraged to customize.

> **FA Note (Dana):** Return rates shown are nominal (not inflation-adjusted). A clear label "Nominal returns — not adjusted for inflation" must appear near the fund selector. Inflation toggle is P1.

### 4.2 Input Fields (P0 — Always Visible)

| # | Field | Type | Default | Range | Step | Notes |
|---|-------|------|---------|-------|------|-------|
| 1 | Initial Investment | Slider + Input | $10,000 | $0 – $1,000,000 | $500 | |
| 2 | Monthly Contribution | Slider + Input | $500 | $0 – $10,000 | $50 | Subtle hint below: "Tip: Most people increase contributions over time →" |
| 3 | Time Horizon | Slider + Input | 25 years | 1 – 50 years | 1 | |
| 4 | Fund Type Selector | Multi-toggle (pill UI) | Total US Stock, Bonds, HYSA | — | — | Up to 6; chart shows one line per selection |

### 4.3 Advanced Inputs (P0 — Behind "Customize Assumptions" Toggle)

| Input | Per-Fund? | Range | Default | Notes |
|-------|-----------|-------|---------|-------|
| Annual Return Rate | Yes (one per fund) | 0% – 20% | See 4.1 | Resets to preset when toggle is closed |
| Expense Ratio | Yes (one per fund) | 0% – 3% | See 4.1 | HYSA shows N/A (grayed out) |
| Annual Contribution Increase | No (global) | 0% – 10% | 0% | Step: 0.5%. Tooltip: "How much you plan to increase your monthly contribution each year (e.g., matching salary growth)" |

### 4.4 Calculated / Derived Values (Read-Only, Shown in UI)

| Value | Formula | Display Location |
|-------|---------|-----------------|
| Net Return (per fund) | `Gross Return − Expense Ratio` | Shown in Advanced panel next to each fund's sliders |
| Final Portfolio Value (per fund) | See Section 6 | Chart endpoint + summary banner |
| Total Contributions | `Initial + Σ(Monthly × 12)` accounting for annual increases | Stat card + dashed line on chart |
| Total Growth (Gains) | `Final Value − Total Contributions` | Stat card |
| Growth Multiple | `Final Value ÷ Total Contributions` | Stat card |
| Estimated Fee Cost (per fund) | `FV(gross) − FV(net)` | Fee impact card (visible when Advanced is on) |

---

## Section 5: Outputs & Visualization

### 5.1 Summary Banner

Displayed above the chart. Mirrors the "Wealth Advantage Banner" pattern from the mortgage section but with projection language.

```
📈  Projected Total US Stock Market value: $847,214 after 25 years
    $647,214 in estimated gains on $200,000 contributed
```

- Uses "projected" / "estimated" — never "grows to" or "will be."
- Updates in real-time as inputs change.
- Shows the **leading fund** (highest final value among selected funds).
- When HYSA is selected, adds: "vs. $183,424 projected in High-Yield Savings."
- Animated counter on value change (matches existing banner animation).
- Background gradient shifts based on growth multiple (subtle).
- **Mobile:** Two-line treatment. Line 1: dollar amount. Line 2: context.

### 5.2 Disclaimer Row (P0 — Non-Dismissable)

Directly below the banner. Compact, single line, styled as a muted footnote.

```
Projections use historical averages and do not guarantee future results. Actual returns vary year to year, including negative years.
```

- Always visible. Never behind a toggle, modal, or scroll.
- On mobile: wraps to two lines max. Still always visible.
- Font size: `var(--text-sm)`. Color: `var(--text-secondary)`.

### 5.3 Stat Cards

Two compact cards below the disclaimer:

**Card 1 — What You Put In**

| Line | Value |
|------|-------|
| Total Contributions | `$X` |
| Initial Lump Sum | `$X` |
| Monthly × Years | `$X` (or "escalating from $X/mo" if annual increase > 0) |

**Card 2 — What You Could Get Back (Leading Fund)**

| Line | Value |
|------|-------|
| Projected Final Value | `$X` |
| Estimated Gains | `$X` |
| Growth Multiple | `X.Xx` |

### 5.4 Fee Impact Card (P0 — Visible Only When Advanced Toggle Is On)

Appears between stat cards and chart when "Customize Assumptions" is active.

**When any fund has expense ratio ≤ 0.5%:**
```
💡  At 0.03% fees, estimated fee cost on Total US Stock Market: $2,814 over 25 years
```

**When any fund has expense ratio > 0.5%:**
```
💡  At 1.00% fees, estimated fee cost on [Fund Name]: $148,291 over 25 years
    Switching to a 0.03% index fund could save you an estimated $145,477
```

### 5.5 Interactive Chart

**Chart Type:** Multi-line chart with contributions baseline (Chart.js, already loaded)

| Property | Spec |
|----------|------|
| X-axis | Year 0 – Year N (time horizon) |
| Y-axis | Portfolio Value (USD, formatted with commas) |
| Lines | One solid line per selected fund type |
| Contributions Line | **Dashed line, always visible**, labeled "Your Contributions" — shows cumulative contributions at each year. This is P0 and non-optional. |
| Colors | Distinct accessible palette; contributions line in neutral gray. Fund lines reuse existing CSS color tokens where possible |
| Legend | Inline, above chart; click to toggle fund line visibility. Contributions line is NOT toggleable — always shown. |
| Tooltip | On hover: Year, projected value per fund, contributions to date, gain vs. contributions |
| Info Icon | ℹ️ icon next to chart title. Expands tooltip: "This chart shows projected growth based on historical average annual returns. Actual year-to-year returns vary significantly and include negative years. Historically, longer time horizons have reduced the impact of short-term volatility." |
| Animation | Lines draw in on load; smooth transitions on input change |
| Responsive | Full-width, min-height 300px mobile / 400px desktop |

### 5.6 Data Table (Toggleable, P0)

Year-by-year breakdown, collapsed by default. Expandable via "Show Year-by-Year Breakdown."

| Year | Contributions to Date | [Fund 1] Projected | [Fund 2] Projected | [Fund 3] Projected |
|------|-----------------------|--------------------|--------------------|--------------------|
| 1 | $16,000 | $17,280 | $16,480 | $16,720 |
| … | … | … | … | … |
| 25 | $160,000 | $847,214 | $239,413 | $183,424 |

- Columns are dynamic — one per selected fund type.
- Column headers say "Projected" (not just the fund name).
- "Contributions to Date" column always shown as the leftmost data column.
- Sticky header, alternating row shading, mobile-scrollable.
- Final row (Year N) highlighted.
- If annual contribution increase is active, contributions column reflects the escalating amounts.

---

## Section 6: Mathematical Logic

### 6.1 Net Return Calculation

```
Net Return = Gross Annual Return − Expense Ratio
```

Example: Total US Stock Market at 10.2% gross with 0.03% ER → 10.17% net.

For HYSA: Expense Ratio is N/A (0%). Net return = stated rate.

### 6.2 Portfolio Growth — No Contribution Increase (Annual Increase = 0%)

Closed-form formula (monthly compounding):

```
FV = P × (1 + r/12)^(12t)  +  C × [ ((1 + r/12)^(12t) − 1) / (r/12) ]
```

Where:
- `FV` = Future Value
- `P` = Initial Investment (lump sum)
- `r` = Net annual return rate (decimal)
- `t` = Time horizon in years
- `C` = Monthly contribution

**Edge case:** If `r = 0`, formula reduces to `FV = P + C × 12t`.

### 6.3 Portfolio Growth — With Contribution Increase (Annual Increase > 0%)

When annual contribution increase is active, use iterative monthly computation:

```
For each month m from 1 to (t × 12):
    year = ceil(m / 12)
    C_effective = C × (1 + g)^(year - 1)
    V(m) = V(m-1) × (1 + r/12) + C_effective
```

Where:
- `g` = Annual contribution increase rate (decimal)
- `C` = Starting monthly contribution
- `V(0)` = Initial Investment (P)

**Dev note (Jordan):** Pre-compute all values on input change. Cache the full monthly array and sample every 12 months for the chart/table. This is O(n) where n = months, max 600 iterations (50 years). Well within 50ms even on mobile.

### 6.4 Fee Cost Estimation

```
Fee Cost = FV(gross return) − FV(net return)
```

Compute FV twice — once with gross return, once with net return (gross minus expense ratio). The delta is the estimated lifetime fee cost. This is an approximation that assumes fees are continuously deducted from returns rather than as a discrete annual charge, which is standard for expense ratio modeling.

### 6.5 Year-by-Year Snapshot (for chart and data table)

Sample `V(m)` at `m = 12, 24, 36, … , 12t` to produce yearly data points.

### 6.6 Total Contributions at Year y

**Without contribution increase:**
```
Contributions(y) = P + C × 12y
```

**With contribution increase:**
```
Contributions(y) = P + Σ (from year=1 to y) [ C × (1 + g)^(year-1) × 12 ]
```

---

## Section 7: User Stories

### Persona: Jordan (First-Time Homebuyer)

- **US-1:** As a first-time homebuyer, I want to enter a lump sum and see how it is projected to grow over 20–30 years, so I understand the long-term opportunity cost of putting all my cash into a down payment vs. investing some of it.
- **US-2:** As a first-time homebuyer, I want to see a "baseline" comparison with a high-yield savings account, so I can understand why investing is typically recommended over just saving.
- **US-3:** As a financially inexperienced user, I want fund types labeled in plain English with familiar examples, so I don't need to research ticker symbols before using the tool.
- **US-4:** As a user making a major financial decision, I want to see a clear disclaimer that these are projections based on historical data, so I don't treat the output as a guarantee.

### Persona: Priya (FIRE Enthusiast)

- **US-5:** As a FIRE enthusiast, I want to customize the assumed return rate and expense ratio for each fund type, so I can model conservative, base-case, and optimistic scenarios with realistic fee drag.
- **US-6:** As a FIRE enthusiast, I want to set an annual contribution increase, so my projection reflects my planned salary-growth-based savings escalation.
- **US-7:** As a FIRE enthusiast, I want to see the exact year-by-year data in a table, so I can verify the math and reference specific milestones.
- **US-8:** As a FIRE enthusiast, I want to compare 3+ fund types simultaneously on one chart, so I can see the return differential visually over decades.
- **US-9:** As a FIRE enthusiast, I want to see the estimated fee cost of each fund, so I can quantify why I choose low-cost index funds.

### Persona: Marcus (Real Estate-Curious Investor)

- **US-10:** As a real estate investor, I want to enter the monthly savings from a smaller mortgage as my monthly contribution, so I can quantify the investing alternative to paying down my house faster.
- **US-11:** As a real estate investor, I want to see the growth multiple (e.g., "3.8x"), so I can compare it against real estate metrics I already understand.

### Edge Cases

- **US-12:** As a user who sets initial investment to $0, I want the calculator to still work using only monthly contributions, so I'm not excluded if I'm starting from scratch.
- **US-13:** As a user on mobile, I want the chart and disclaimer to be visible without scrolling past inputs, so I can get value and context immediately.
- **US-14:** As a user who selects all 6 fund types, I want the chart to remain readable with distinct colors and a toggleable legend, so I'm not overwhelmed by overlapping lines.
- **US-15:** As a user who sees a large projected number, I want clear contextual cues that this is an estimate based on averages, so I don't develop false confidence in a specific outcome.

---

## Section 8: Requirements

### P0 — Must-Have (V1 cannot ship without these)

| # | Requirement | Acceptance Criteria |
|---|-------------|---------------------|
| R-01 | Multi-line compound interest chart for up to 6 fund types | Given valid inputs, chart shows one solid line per selected fund type with correct projected values per year |
| R-02 | Contributions dashed baseline on chart | A dashed gray line labeled "Your Contributions" is always visible, showing cumulative contributions at each year; line is not toggleable off |
| R-03 | Summary banner with projection language | Banner displays "Projected [fund] value: $X after Y years"; uses "projected" or "estimated"; updates within 50ms of input change |
| R-04 | Non-dismissable disclaimer row | Single-line disclaimer below banner is always visible, never behind a toggle/modal, readable at 375px |
| R-05 | 4 core input fields with sliders + direct entry | All inputs function identically to existing mortgage calculator inputs; real-time update |
| R-06 | Fund type multi-selector (pill toggle UI) | Default shows 3 funds (Total US Stock, Bonds, HYSA); all 6 selectable; chart updates immediately |
| R-07 | Default return rates per fund (net of fees, historically sourced) | All 6 funds use Dana's net defaults from Section 4.1; source period noted in UI |
| R-08 | "Customize Assumptions" advanced toggle | Reveals per-fund rate sliders, per-fund expense ratio sliders, and annual contribution increase slider |
| R-09 | Per-fund expense ratio sliders (in Advanced) | Each fund has an ER slider; HYSA shows N/A; net return updates in real-time |
| R-10 | Annual contribution increase slider (in Advanced) | Single slider, 0-10%, step 0.5%; calculation engine switches to iterative when > 0 |
| R-11 | Fee impact card (in Advanced) | When Advanced is on, card shows estimated fee cost for each fund; when ER > 0.5%, shows savings from switching to 0.03% |
| R-12 | Stat cards (Contributions + Projected Returns) | Two cards showing total contributions breakdown and leading fund's projected gains + growth multiple |
| R-13 | Year-by-year data table (collapsible) | Column headers say "Projected"; contributions column always shown; accounts for contribution escalation |
| R-14 | Volatility info tooltip on chart | ℹ️ icon next to chart title; tooltip explains historical variability and that smooth curve is an average projection |
| R-15 | Section navigation (sticky tab nav in header) | Header nav includes "Mortgage vs. Invest" and "Investment Growth"; visual section divider between them |
| R-16 | Responsive layout (375px – 2560px) | Chart, banner, disclaimer, inputs all render correctly; no horizontal overflow; banner wraps to 2 lines on mobile |
| R-17 | Performance: all recalculations < 50ms | Verified via DevTools with 6 fund types active + annual contribution increase on |
| R-18 | Dark mode support | New section fully respects `[data-theme="dark"]` CSS variables; no hardcoded colors |

### P1 — Nice-to-Have (Target V1.1)

| # | Requirement |
|---|-------------|
| R-19 | Inflation toggle: show real (inflation-adjusted) vs. nominal values; default inflation rate 2.5% |
| R-20 | Milestone markers on chart (diamond indicators at $100K, $250K, $500K, $1M) |
| R-21 | Contextual cross-link callout at bottom of mortgage section: "Explore investing further →" |
| R-22 | "Nominal returns — not adjusted for inflation" label near fund selector (interim before inflation toggle ships) |
| R-23 | Meta tag update: `<title>` and `<meta description>` reference both calculators |
| R-24 | Chart annotation: vertical line at "contributions exceeded" year (where gains surpass contributions) |

### P2 — Future Considerations (Inform architecture, don't build yet)

| # | Requirement |
|---|-------------|
| R-25 | Volatility bands: show best/worst historical 25-year ranges as shaded area around projection line |
| R-26 | URL param pre-fill from mortgage section with explanation banner |
| R-27 | Tax-advantaged account toggle (Roth IRA, 401k, taxable) — requires legal review |
| R-28 | Portfolio allocation (e.g., 60/40 blend in one scenario) |
| R-29 | Export chart as PNG / share results as image |
| R-30 | Withdrawal / retirement drawdown modeling |

---

## Section 9: Success Metrics

### Leading Indicators (measure at 2 weeks post-launch)

| Metric | Tool | Target |
|--------|------|--------|
| % of sessions that reach the investment section | Scroll depth tracking | > 55% |
| % of investment section sessions that interact with any input | Click/change events | > 65% |
| % that toggle a fund type on/off | Event tracking | > 60% |
| % that enable "Customize Assumptions" | Event tracking | 20–40% (target range) |
| % that interact with volatility info tooltip | Event tracking | > 15% |
| % that open the data table | Event tracking | > 25% |

### Lagging Indicators (measure at 6 weeks post-launch)

| Metric | Tool | Target |
|--------|------|--------|
| Avg. session duration | Analytics | +90 seconds vs. pre-launch |
| Share / deep-link events | Analytics | > 8% of investment section sessions |
| Return visit rate | Analytics | > 15% |
| Mobile vs. desktop engagement parity | Analytics | Mobile within 15% of desktop |

---

## Section 10: Open Questions

| # | Question | Owner | Blocking? |
|---|----------|-------|-----------|
| OQ-1 | Should the HYSA rate be always-editable (even when Customize is OFF) since it fluctuates? Dana recommends yes; Sam wants to evaluate the UX of one "special" input. | **Sam + Dana** | Non-blocking — ship with tooltip acknowledgment, revisit post-launch |
| OQ-2 | The existing calculator has no analytics instrumentation. Do we add event tracking as part of this feature or as a separate ticket? | **Alex + Jordan** | Non-blocking — but needed for success metrics |
| OQ-3 | Should the section be called "Investment Growth Calculator" or "Compound Interest Calculator"? SEO favors "compound interest"; users may prefer "investment growth." | **Taylor + Jamie** | Non-blocking — decide before writing final copy |
| OQ-4 | The `index.html` is already ~1000 lines. Should we modularize (separate JS/CSS files) as part of this feature? | **Jordan** | Blocking if file exceeds 2000 lines — evaluate before implementation |
| OQ-5 | What source and time period should we cite for the default return rates? "30-year trailing as of Dec 2025" is Dana's recommendation. Need to confirm exact figures. | **Dana** | Blocking — must be confirmed before defaults are finalized |
| OQ-6 | Should expense ratio also affect HYSA? (Some HYSA products have no fee, but some savings accounts do.) | **Dana** | Non-blocking — default to N/A for V1 per Dana's recommendation |
| OQ-7 | Legal review: does showing projected investment returns require any specific disclosures beyond our current disclaimer? | **Alex (to route to Legal)** | Non-blocking for V1 but should be reviewed before V1.1 |

---

## Section 11: Timeline Considerations

| Item | Note |
|------|------|
| No hard deadline | Enhancement to an existing static tool; no contractual commitments |
| Recommended V1 scope | R-01 through R-18 — all P0s |
| Recommended V1.1 scope | R-19 through R-24 — all P1s |
| Key dependency | Section navigation (R-15) should be implemented first — it sets the page architecture |
| Key dependency | Confirm default return rate sources (OQ-5) before building the calculation engine |
| Risk | File size — if `index.html` exceeds ~2000 lines, evaluate modularization (OQ-4) before proceeding |
| Risk | Expense ratio feature adds UI complexity to the Advanced panel — needs design review for information hierarchy before dev starts |

---

## Appendix A: Validation Test Cases

Use these values to verify the calculation engine.

### Test Case 1: Basic (No Contribution Increase)

**Inputs:**
| Variable | Value |
|----------|-------|
| Initial Investment | $10,000 |
| Monthly Contribution | $500 |
| Time Horizon | 25 years |
| Annual Contribution Increase | 0% |
| Total US Stock Market Net Return | 10.17% |
| US Bond Market Net Return | 4.47% |
| HYSA Return | 4.5% |

**Expected Outputs (rounded to nearest dollar):**
| Fund | Final Value | Total Contributions | Gains |
|------|-------------|---------------------|-------|
| Total US Stock Market (10.17%) | $723,441 | $160,000 | $563,441 |
| US Bond Market (4.47%) | $296,164 | $160,000 | $136,164 |
| HYSA (4.5%) | $298,206 | $160,000 | $138,206 |

### Test Case 2: With Contribution Increase

**Inputs:**
| Variable | Value |
|----------|-------|
| Initial Investment | $10,000 |
| Monthly Contribution (starting) | $500 |
| Annual Contribution Increase | 3% |
| Time Horizon | 25 years |
| Total US Stock Market Net Return | 10.17% |

**Expected Output:**
| Fund | Final Value | Total Contributions | Gains |
|------|-------------|---------------------|-------|
| Total US Stock Market (10.17%) | $1,021,753 | $223,121 | $798,632 |

### Test Case 3: Fee Impact

**Inputs:**
| Variable | Value |
|----------|-------|
| Initial Investment | $50,000 |
| Monthly Contribution | $1,000 |
| Time Horizon | 30 years |
| Gross Return | 10.0% |
| Expense Ratio A | 0.03% |
| Expense Ratio B | 1.00% |

**Expected Fee Impact:**
| Scenario | Final Value |
|----------|-------------|
| 0.03% ER | ~$2,381,000 |
| 1.00% ER | ~$1,841,000 |
| **Fee cost of 1% ER** | **~$540,000** |

> **Dev note:** Verify all test cases before considering the engine complete. Values should match within $100 (rounding variance from monthly iteration).

---

## Appendix B: Design Consistency Checklist

- [ ] Uses CSS custom properties (variables) exclusively — no hardcoded hex values
- [ ] Dark mode tested with `[data-theme="dark"]` class on `<html>`
- [ ] Slider + number input pair matches existing mortgage section pattern exactly
- [ ] Chart colors pass WCAG AA contrast against both light and dark backgrounds
- [ ] Contributions dashed line is visually distinct (gray, dashed) from fund lines
- [ ] Font: Inter, same weight scale as existing sections
- [ ] Border radius, shadow, and surface tokens match existing cards
- [ ] Mobile: summary banner + disclaimer readable at 375px without truncation
- [ ] All inputs have `<label>` elements (accessibility)
- [ ] Chart has `aria-label` describing its content
- [ ] Info tooltip (ℹ️) is keyboard-accessible and works on touch (not hover-only)
- [ ] Visual section divider between mortgage and investment sections is present and themed
- [ ] Disclaimer text uses `var(--text-secondary)` color, not hardcoded
- [ ] Fee impact card styling is consistent with existing stat cards

---

*This spec was generated via structured expert consensus review on March 14, 2026.*
*Team Lead: Sam (UXD) — final authority on all contested decisions.*
*Financial accuracy reviewed by: Dana (Financial Advisor SME).*
*Approved by: UXD ✅ | FA ✅ | PM ✅ | Dev ✅ | UXR ✅ | Marketing ✅*
