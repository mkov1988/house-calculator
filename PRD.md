# Product Requirements Document: The Mortgage vs. Invest Calculator

**Version:** 1.0  
**Date:** February 28, 2026  
**Author:** Product & Design Team  
**Status:** Draft — Ready for Development Handoff

---

## 1. Executive Summary & Goals

### Core Value Proposition

The **Mortgage vs. Invest Calculator** is an interactive web application that answers a critical personal finance question:

> *"Should I put a massive down payment on my house to minimize my mortgage, or should I put down the standard 20% and invest the remaining cash in a total stock market index fund?"*

Most homebuyers make this decision based on intuition or incomplete math. This tool makes the math rigorous and visual by modeling a full 30-year amortization alongside monthly-compounding investment growth—including realistic **tax drag** on dividend income. Users input their own numbers and instantly see **who wins, by how much, and when**.

### Primary Goals

| # | Goal | Success Metric |
|---|------|----------------|
| 1 | Help users make data-driven home financing decisions | > 80% of sessions interact with at least one input slider |
| 2 | Deliver a premium, trust-inspiring UX | Average time-on-page > 3 minutes |
| 3 | Educate users on tax drag and opportunity cost | > 50% of users toggle the data table or hover on chart tooltips |
| 4 | Drive shareability and organic reach | > 5% of users share results via URL or screenshot |

---

## 2. Target User Personas

### Persona 1: The First-Time Homebuyer — "Jordan"
- **Age:** 28–35
- **Context:** Saved a large cash reserve and is buying their first home. Unsure whether to dump it all into the down payment.
- **Motivation:** Wants to make the *mathematically optimal* choice but doesn't have the spreadsheet skills to model it.
- **Pain Point:** Conflicting advice from family ("be debt-free!") vs. personal finance forums ("invest the difference!").

### Persona 2: The FIRE Enthusiast — "Priya"
- **Age:** 30–45
- **Context:** Active in the Financial Independence / Retire Early community. Deeply familiar with index fund investing (VTSAX, VTI).
- **Motivation:** Wants to quantify the exact wealth advantage of leveraging a low-rate mortgage and investing the delta.
- **Pain Point:** Has done rough napkin math but wants an interactive, visual tool that accounts for tax drag on dividends.

### Persona 3: The Real Estate-Curious Investor — "Marcus"
- **Age:** 35–55
- **Context:** Considering a primary residence purchase and wants to compare it against putting more money to work in the market.
- **Motivation:** Needs to see the liquidity trade-off — home equity is illiquid, a brokerage portfolio is not.
- **Pain Point:** Existing calculators don't show the *liquidity* comparison side-by-side with net wealth.

---

## 3. User Interface (UI) & User Experience (UX) Requirements

### 3.1 Design Principles

| Principle | Implementation |
|-----------|----------------|
| **Clarity over cleverness** | Every number should be labeled with its unit. Jargon gets a tooltip. |
| **Real-time feedback** | All outputs update instantly as inputs change — no "Calculate" button. |
| **Progressive disclosure** | Summary cards and charts are always visible; the full data table is expandable. |
| **Premium feel** | Dark mode, smooth gradients, micro-animations on value changes, modern sans-serif typography (e.g., Inter or Outfit). |

### 3.2 Layout — Desktop (≥ 1024px)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           HEADER / APP TITLE                                │
├──────────────────────────┬──────────────────────────────────────────────────┤
│                          │                                                  │
│   CONTROL PANEL          │  ┌────────────────────────────────────────────┐  │
│                          │  │  [$$] WEALTH ADVANTAGE BANNER              │  │
│  ┌────────────────────┐  │  │  "Scenario 2 generates $688,816 more       │  │
│  │ Home Price         │  │  │   by Year 30"                              │  │
│  │ [═══════●══] $750k │  │  └────────────────────────────────────────────┘  │
│  │                    │  │                                                  │
│  │ S1 Down Payment    │  │  ┌───────────────────┐  ┌───────────────────┐    │
│  │ [══●═══════] $550k │  │  │ S1 Monthly        │  │ S2 Monthly        │    │
│  │                    │  │  │ Cash Flow         │  │ Cash Flow         │    │
│  │ S2 Down Payment    │  │  │                   │  │                   │    │
│  │ [═●════════] $150k │  │  │ P&I:  $1,197      │  │ P&I:  $3,590      │    │
│  │                    │  │  │ Tax:    $688      │  │ Tax:    $688      │    │
│  │ Mortgage Rate      │  │  │ Ins:    $438      │  │ Ins:    $438      │    │
│  │ [══════●══] 5.98%  │  │  │ Mnt:    $625      │  │ Mnt:    $625      │    │
│  │                    │  │  │ ─────────────     │  │ ─────────────     │    │
│  │ Loan Term          │  │  │ Total: $2,947     │  │ Total: $5,340     │    │
│  │ [30 years       ▾] │  │  └───────────────────┘  └───────────────────┘    │
│  │                    │  │                                                  │
│  │ Prop Tax Rate      │  │  ┌────────────────────────────────────────────┐  │
│  │ [════●════] 1.1%   │  │  │                                            │  │
│  │                    │  │  │  [^] NET WEALTH COMPARISON (Line Chart)    │  │
│  │ Annual Insurance   │  │  │                                            │  │
│  │ [══●═════] $5,250  │  │  │  S2  ────────────────╱                     │  │
│  │                    │  │  │  S1  ──────────────╱                       │  │
│  │ Maintenance %      │  │  │                  ╱                         │  │
│  │ [═●══════] 1.0%    │  │  │                ╱                           │  │
│  │                    │  │  └────────────────────────────────────────────┘  │
│  │ ── Market ──       │  │                                                  │
│  │ Gross Return       │  │  ┌────────────────────────────────────────────┐  │
│  │ [══════●══] 8.0%   │  │  │                                            │  │
│  │                    │  │  │  [~] LIQUIDITY COMPARISON (Line Chart)     │  │
│  │ Dividend Yield     │  │  │                                            │  │
│  │ [═●══════] 1.4%    │  │  │  S2  ────────────────╱                     │  │
│  │                    │  │  │  S1  ──────────────╱                       │  │
│  │ Div Tax Rate       │  │  │                                            │  │
│  │ [══●═════] 15.0%   │  │  └────────────────────────────────────────────┘  │
│  │                    │  │                                                  │
│  │ ── Calculated ──   │  │  ┌────────────────────────────────────────────┐  │
│  │ Tax Drag:  0.21%   │  │  │  [>] 30-Year Data Table  (expandable)      │  │
│  │ Net Return: 7.79%  │  │  └────────────────────────────────────────────┘  │
│  └────────────────────┘  │                                                  │
│                          │                                                  │
├──────────────────────────┴──────────────────────────────────────────────────┤
│                           FOOTER                                            │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.3 Layout — Mobile (< 768px)

On mobile, the layout stacks vertically:

1. **Header**
2. **Wealth Advantage Banner** (always visible, sticky)
3. **Monthly Cash Flow Cards** (side-by-side, scrollable)
4. **Charts** (full-width, tab-selectable: Net Wealth | Liquidity)
5. **Control Panel** (collapsible accordion, opens on tap)
6. **Data Table** (horizontally scrollable)

### 3.4 Interaction Patterns

| Interaction | Behavior |
|-------------|----------|
| **Slider drag** | Output values, charts, and the wealth banner update in real-time (debounced at ~50ms). |
| **Number input** | Users can click the displayed value next to any slider to type an exact number. |
| **Chart hover** | Tooltip shows Year, S1 value, S2 value, and the delta. |
| **Chart click** | Clicking a year on the chart highlights the corresponding row in the data table. |
| **Data table toggle** | A "Show 30-Year Breakdown" button expands/collapses the full amortization table with a smooth animation. |

---

## 4. Feature Specifications & Data Inputs

### 4.1 Input Fields

All inputs must support both **slider interaction** and **direct numeric entry**. Values update all outputs in real-time.

| # | Input Field | Type | Default | Range | Step | Unit |
|---|-------------|------|---------|-------|------|------|
| 1 | Home Price | Slider + Input | $750,000 | $100K – $3M | $10,000 | USD |
| 2 | Scenario 1: Big Down Payment | Slider + Input | $550,000 | $0 – Home Price | $10,000 | USD |
| 3 | Scenario 2: Standard Down Payment | Slider + Input | $150,000 | $0 – Home Price | $5,000 | USD |
| 4 | Mortgage Rate | Slider + Input | 5.98% | 0% – 12% | 0.01% | % |
| 5 | Loan Term | Dropdown | 30 years | 10, 15, 20, 25, 30 | — | Years |
| 6 | Property Tax Rate | Slider + Input | 1.1% | 0% – 4% | 0.05% | % |
| 7 | Annual Insurance | Slider + Input | $5,250 | $0 – $25,000 | $250 | USD |
| 8 | Maintenance % | Slider + Input | 1.0% | 0% – 5% | 0.1% | % |
| 9 | Gross VTSAX Return | Slider + Input | 8.0% | 0% – 15% | 0.1% | % |
| 10 | VTSAX Dividend Yield | Slider + Input | 1.4% | 0% – 5% | 0.1% | % |
| 11 | Dividend Tax Rate | Slider + Input | 15.0% | 0% – 40% | 1% | % |

### 4.2 Calculated Fields (Read-Only, Displayed in Control Panel)

| Field | Formula | Default Value |
|-------|---------|---------------|
| **Tax Drag** | `Dividend Yield × Dividend Tax Rate` | 0.21% |
| **Net VTSAX Return** | `Gross VTSAX Return − Tax Drag` | 7.79% |

### 4.3 Derived Scenario Values (Internal)

| Value | Formula |
|-------|---------|
| S1 Loan Amount | `Home Price − S1 Down Payment` |
| S2 Loan Amount | `Home Price − S2 Down Payment` |
| Total Available Cash | `max(S1 Down Payment, S2 Down Payment) + Starting Investment Reserve` |
| S1 Starting VTSAX | `Total Cash − S1 Down Payment` |
| S2 Starting VTSAX | `Total Cash − S2 Down Payment` |
| S1 Monthly P&I | Standard amortization of S1 Loan Amount |
| S2 Monthly P&I | Standard amortization of S2 Loan Amount |
| Common Monthly Costs | `(Home Price × Prop Tax Rate / 12) + (Annual Insurance / 12) + (Home Price × Maintenance % / 12)` |
| S1 Total Monthly Housing | `S1 P&I + Common Monthly Costs` |
| S2 Total Monthly Housing | `S2 P&I + Common Monthly Costs` |
| VTSAX Monthly Contribution (S1) | `S2 Total Monthly Housing − S1 Total Monthly Housing` |

> [!IMPORTANT]
> **Key Insight:** Scenario 1 (big down payment) has lower monthly costs. The difference is redirected as a monthly contribution to S1's investment portfolio. Scenario 2 starts with a much larger lump sum but makes **no** additional monthly contributions. This is the core trade-off the tool visualizes.

---

## 5. Output & Visualization Requirements

### 5.1 The "Wealth Advantage" Banner

**Location:** Top of the output panel, always visible.

**Content:**
```
💰 Scenario 2 generates $688,816 more in net wealth by Year 30
```

- Dynamically updates with all inputs.
- If Scenario 1 wins, the banner flips accordingly.
- Use a large, bold font with an animated counter effect when values change.
- Background: a subtle gradient that shifts hue based on which scenario is winning (e.g., green for S2, blue for S1).

### 5.2 Monthly Cash Flow Breakdown

Two side-by-side cards comparing the monthly housing cost structure:

**Card: Scenario 1 — Big Down Payment**
| Line Item | Amount |
|-----------|--------|
| Principal & Interest | $1,197.20 |
| Property Tax | $687.50 |
| Insurance | $437.50 |
| Maintenance | $625.00 |
| **Total** | **$2,947.20** |

**Card: Scenario 2 — Standard (20%) Down Payment**
| Line Item | Amount |
|-----------|--------|
| Principal & Interest | $3,590.31 |
| Property Tax | $687.50 |
| Insurance | $437.50 |
| Maintenance | $625.00 |
| **Total** | **$5,340.31** |

**Below the cards, display:**
- S1 Starting VTSAX Balance: $107,000
- S2 Starting VTSAX Balance: $507,000
- S1 Monthly VTSAX Contribution: $2,393.06 (the housing cost savings redirected to investing)
- S2 Monthly VTSAX Contribution: $0.00

### 5.3 Interactive Charts

#### Chart 1: Net Wealth Comparison (Line Chart)

- **X-axis:** Year (0–30)
- **Y-axis:** Total Net Wealth (USD, formatted with commas)
- **Lines:**
  - **S1 Net Wealth** = `(Home Price − S1 Remaining Loan Balance) + S1 VTSAX Portfolio Value`
  - **S2 Net Wealth** = `(Home Price − S2 Remaining Loan Balance) + S2 VTSAX Portfolio Value`
- **Tooltip on hover:** Shows Year, S1 Net Wealth, S2 Net Wealth, and Delta.
- **Shaded area** between the two lines to visually emphasize the advantage gap.

#### Chart 2: Liquidity Comparison (Line Chart)

- **X-axis:** Year (0–30)
- **Y-axis:** Liquid Assets (USD)
- **Lines:**
  - **S1 VTSAX Portfolio Value** (liquid)
  - **S2 VTSAX Portfolio Value** (liquid)
- **Purpose:** Shows that even though net wealth differences may appear modest, the *liquidity* difference is dramatic. Home equity is illiquid; portfolio value is accessible.

#### Chart Requirements (Both)

| Requirement | Detail |
|-------------|--------|
| Library | Chart.js, Recharts, or D3.js — developer's choice |
| Animation | Lines draw in on initial load; smooth transitions on input changes |
| Responsive | Charts resize gracefully on all viewports |
| Colors | Two distinct, accessible colors with sufficient contrast (WCAG AA) |
| Legend | Inline legend above each chart |
| Grid | Subtle gridlines, y-axis formatted as currency |

### 5.4 Data Table (Toggleable)

A 30-year breakdown table, collapsed by default, expandable via a "Show 30-Year Breakdown" button.

| Year | S1 Loan Balance | S1 VTSAX | S1 Net Wealth | S2 Loan Balance | S2 VTSAX | S2 Net Wealth | S2 Advantage |
|------|-----------------|----------|---------------|-----------------|----------|---------------|--------------|
| 1 | $197,535 | $145,404 | $697,869 | $592,604 | $547,938 | $705,334 | $7,463 |
| 2 | ... | ... | ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... | ... | ... | ... |
| 30 | $0 | $4,517,220 | $5,267,220 | $0 | $5,206,036 | $5,956,036 | $688,816 |

**Table features:**
- Alternating row shading for readability
- Sticky header row
- Highlight the final row (Year 30) with a distinct background color
- All currency values formatted with commas and `$` prefix
- Horizontally scrollable on mobile

---

## 6. Technical & Mathematical Logic

### 6.1 Monthly Mortgage Payment (P&I)

Use the **standard amortization formula:**

```
M = P × [ r(1+r)^n ] / [ (1+r)^n − 1 ]
```

Where:
- `M` = Monthly payment (Principal & Interest only)
- `P` = Loan principal (Home Price − Down Payment)
- `r` = Monthly interest rate (Annual Rate ÷ 12)
- `n` = Total number of payments (Loan Term × 12)

### 6.2 Remaining Loan Balance

After `k` monthly payments:

```
B(k) = P × [ (1+r)^n − (1+r)^k ] / [ (1+r)^n − 1 ]
```

### 6.3 Tax Drag Calculation

The tool must **reduce the gross market return** by the tax owed on dividends each year:

```
Tax Drag = VTSAX Dividend Yield × Dividend Tax Rate
Net VTSAX Return = Gross VTSAX Return − Tax Drag
```

**Example with defaults:**
```
Tax Drag = 1.4% × 15% = 0.21%
Net Return = 8.0% − 0.21% = 7.79%
```

> [!NOTE]
> This is a simplification. In reality, taxes are paid annually on dividends received, not deducted from the growth rate. However, this approximation is standard for long-horizon planning tools and produces results within an acceptable margin of error.

### 6.4 Investment Portfolio Growth (Monthly Compounding)

For each month `m`, the portfolio value is:

```
V(m) = V(m-1) × (1 + r_monthly) + C
```

Where:
- `r_monthly` = Net VTSAX Return ÷ 12
- `C` = Monthly contribution ($2,393.06 for S1, $0 for S2 with defaults)
- `V(0)` = Starting VTSAX Balance (S1: $107,000 | S2: $507,000 with defaults)

### 6.5 Net Wealth Calculation

At any point in time:

```
Net Wealth = Home Equity + VTSAX Portfolio Value
           = (Home Price − Remaining Loan Balance) + VTSAX Value
```

> [!IMPORTANT]
> Home Price is treated as **fixed** throughout the 30-year model in V1 (no appreciation). This is a deliberate simplification — home price appreciation is scoped as a V2 enhancement.

### 6.6 Wealth Advantage

```
S2 Advantage = S2 Net Wealth − S1 Net Wealth
```

A positive value means the standard-down-payment + invest strategy wins.

---

## 7. User Stories

| # | As a... | I want to... | So that I can... |
|---|---------|-------------|-----------------|
| US-1 | Homebuyer | Adjust the home price and see costs update instantly | Understand how price affects my decision |
| US-2 | FIRE enthusiast | See the exact tax drag on my dividend income | Model my real after-tax investment returns |
| US-3 | User | Compare monthly cash flows side by side | See the true cost difference of each scenario |
| US-4 | User | View a chart of net wealth over 30 years | Visually understand when and how much S2 overtakes S1 |
| US-5 | User | See my liquidity (VTSAX only) charted over time | Understand how accessible my money is in each scenario |
| US-6 | Data-driven user | Expand a full 30-year data table | Verify the exact numbers year-by-year |
| US-7 | Mobile user | Use the calculator on my phone | Make decisions on-the-go with a responsive layout |
| US-8 | User | See a prominent banner showing the wealth difference | Immediately grasp the bottom-line result |

---

## 8. Non-Functional Requirements

| Category | Requirement |
|----------|-------------|
| **Performance** | All recalculations must complete in < 50ms. No perceptible lag on slider drag. |
| **Accessibility** | WCAG 2.1 AA compliant. Chart colors must pass contrast checks. All inputs labeled. |
| **Browser Support** | Chrome, Firefox, Safari, Edge (latest 2 versions) |
| **Responsive** | Fully functional from 375px (mobile) to 2560px (ultrawide) |
| **SEO** | Proper meta tags, `<h1>` hierarchy, semantic HTML, descriptive `<title>` |
| **No Backend** | All calculations are client-side JavaScript. No server, no database. |
| **Hosting** | Static files only — deployable to GitHub Pages, Netlify, or Vercel |

---

## 9. Future Enhancements (V2)

### Enhancement 1: Home Price Appreciation Slider
- Add an input for **Annual Home Appreciation Rate** (e.g., 3%).
- Recalculate Net Wealth using an appreciating home value instead of a fixed price.
- This dramatically changes the model — higher appreciation narrows S2's advantage.

### Enhancement 2: Capital Gains Tax on Portfolio Liquidation
- Add a toggle: **"Account for capital gains tax on portfolio sale?"**
- When enabled, apply long-term capital gains tax (e.g., 15-20%) to the portfolio gains at Year 30.
- Displays a "Net After-Tax Wealth" comparison alongside the pre-tax view.

### Enhancement 3: Adjustable Monthly Investment Contribution
- Allow users to set a **custom monthly contribution** for either or both scenarios, independent of the housing cost delta.
- Useful for modeling scenarios where the user has additional income to invest beyond the housing cost savings.

---

## Appendix A: Reference Data (Default Scenario)

The following values were extracted from the reference spreadsheet and should be used for validation during development.

**Inputs:**
| Variable | Value |
|----------|-------|
| Home Price | $750,000 |
| S1 Down Payment | $550,000 |
| S2 Down Payment | $150,000 |
| Mortgage Rate | 5.98% |
| Loan Term | 30 years |
| Property Tax Rate | 1.1% |
| Annual Insurance | $5,250 |
| Maintenance % | 1.0% |
| Gross VTSAX Return | 8.0% |
| Dividend Yield | 1.4% |
| Dividend Tax Rate | 15.0% |

**Expected Outputs (for validation):**

| Output | Value |
|--------|-------|
| Tax Drag | 0.21% |
| Net VTSAX Return | 7.79% |
| S1 Monthly P&I | ~$1,197 |
| S2 Monthly P&I | ~$3,590 |
| S1 Total Monthly Housing | ~$2,947 |
| S2 Total Monthly Housing | ~$5,340 |
| S1 Starting VTSAX | $107,000 |
| S2 Starting VTSAX | $507,000 |
| S1 Monthly Contribution | ~$2,393 |
| Year 30 S2 Advantage | **$688,816** |

**Key Data Table Checkpoints:**

| Year | S1 Net Wealth | S2 Net Wealth | S2 Advantage |
|------|---------------|---------------|--------------|
| 1 | $697,869 | $705,334 | $7,463 |
| 5 | $896,572 | $939,325 | $42,753 |
| 15 | $1,763,820 | $1,949,029 | $185,209 |
| 30 | $5,267,220 | $5,956,036 | $688,816 |
