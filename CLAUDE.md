# CLAUDE.md — Investment Growth Calculator Handoff

> This file is the primary context document for Claude in VS Code. Read it fully before touching any code. All decisions documented here were made in a prior design session and are **not up for re-debate** unless the user explicitly asks to revisit them. Your job is to implement the consensus spec faithfully.

---

## Project Overview

**What this is:** A single-file static web app (`index.html`) that helps users make data-driven home financing decisions. It currently contains one calculator: "Mortgage vs. Invest" — a 30-year comparison of two down payment scenarios.

**What you are building:** A second section appended to the same `index.html`: an **Investment Growth Calculator**. It shows compound interest growth over time across multiple fund types, with expense ratio modeling, a contributions baseline, and honest projection language throughout.

**Constraints that are non-negotiable:**
- Single file only — all HTML, CSS, and JS stay in `index.html`. No separate files unless the file exceeds ~2000 lines and Jordan (Dev persona) explicitly scoped modularization.
- No new external dependencies. Chart.js (`@4.4.7`) is already loaded. Inter font is already loaded. Use them.
- All calculations are client-side JavaScript. No backend, no API, no data fetching.
- All CSS must use existing design tokens (`var(--bg)`, `var(--surface)`, etc.). No hardcoded hex values except for the 6 new fund color tokens defined below.
- Fully functional from 375px to 2560px viewport width.
- All recalculations must complete in < 50ms.
- Full dark mode support via existing `[data-theme="dark"]` CSS class on `<html>`.

---

## File Map

| File | Purpose |
|------|---------|
| `index.html` | The entire app. You will add the new section here. |
| `investment-calculator-spec.md` | Full feature spec — requirements, math, user stories, acceptance criteria |
| `investment-calculator-ui-spec.md` | Full UI spec — layout, colors, components, animations, edge states, ASCII mockups |
| `PRD.md` | Original product context and personas |
| `Design Brief.png` | Visual reference for the existing app's aesthetic |

**Read the spec files before writing code.** The math in `investment-calculator-spec.md` Section 6 is the source of truth. The layouts in `investment-calculator-ui-spec.md` are the source of truth for HTML structure.

---

## Existing Codebase — What's Already There

### Design Tokens (CSS variables — use these, don't hardcode)

```css
/* Colors */
--bg: #f4f5f7               /* Page background */
--surface: #ffffff           /* Card backgrounds */
--border: #e2e5ea            /* Borders */
--text: #1a1d23              /* Primary text */
--text-secondary: #5f6672    /* Secondary/muted text */
--accent: #4f46e5            /* Primary accent (indigo) */
--accent-light: #eef2ff      /* Light accent background */
--green: #10b981
--green-bg: #ecfdf5
--green-border: #a7f3d0
--orange: #f59e0b
--orange-bg: #fffbeb
--s1-color: #3b82f6          /* Mortgage Scenario 1 (blue) */
--s2-color: #7c3aed          /* Mortgage Scenario 2 (purple) */

/* Spacing / Shape */
--radius: 0.8rem
--radius-sm: 0.5333rem
--shadow: 0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04)
--shadow-md: 0 0.2667rem 0.8rem rgba(0,0,0,0.07), 0 2px 0.2667rem rgba(0,0,0,0.04)

/* Typography scale (clamp-based, responsive) */
--text-sm: clamp(0.875rem, 1vw, 0.9rem)
--text-base: clamp(1rem, 1.2vw, 1.05rem)
--text-md: clamp(1.125rem, 1.5vw, 1.25rem)
--text-lg: clamp(1.25rem, 2vw, 1.5rem)
--text-xl: clamp(1.5rem, 2.5vw, 2rem)
--text-2xl: clamp(2rem, 3.5vw, 2.75rem)
```

### Dark Mode Overrides
All of the above are overridden in `[data-theme="dark"]`. The dark mode toggle is already wired up in the existing JS. Your new section will inherit dark mode automatically if you use only CSS variables.

### CSS Classes Available to Reuse

| Class | What it does |
|-------|-------------|
| `.card` | White surface card with border, radius, shadow |
| `.input-group` | Vertical input block with label |
| `.input-row` | Flex row for slider + number input |
| `input[type="range"]` | Fully styled slider (accent colored thumb) |
| `input[type="number"]` | Styled number input, no spinners |
| `.advanced-toggle` | Collapsible section header with chevron |
| `.advanced-body` / `.advanced-body.open` | Accordion body (height: 0 → max-height) |
| `.chart-section` | Chart wrapper with title + subtitle |
| `.chart-wrapper` | `position: relative`, `height: 21.33rem` |
| `.tab-btn` / `.tab-btn.active` | Tab button (underline active state) |
| `.data-table` / `.data-table-wrap` | Scrollable table with sticky header |
| `.how-it-works` | Explanatory callout box (orange-tinted) |

### Existing JavaScript Patterns

**`animateValue(el, start, end, duration, formatter)`** — Counter animation already exists. Reuse it for the banner hero number and stat card values.

**`debounce(fn, ms)`** — May exist. If not, implement a standard 16ms debounce for slider input handlers.

**Chart.js instances** — Existing charts use `new Chart(ctx, config)`. Maintain references so you can call `.update()` on input change rather than destroying and recreating.

**Dark mode JS** — Already toggles `[data-theme="dark"]` on `<html>`. Chart colors need to be re-read from CSS variables and applied to Chart.js datasets when theme toggles. Look at how existing charts handle this.

---

## New CSS Tokens to Add

Add these to `:root` and `[data-theme="dark"]` in the `<style>` block:

```css
/* In :root */
--invest-stock:   #4f46e5;   /* Total US Stock Market */
--invest-sp500:   #2563eb;   /* S&P 500 */
--invest-intl:    #0d9488;   /* International */
--invest-bonds:   #d97706;   /* Bonds */
--invest-target:  #e11d48;   /* Target Date 2050 */
--invest-hysa:    #64748b;   /* HYSA — intentionally muted/lower energy */
--invest-contrib: rgba(100, 116, 139, 0.5); /* Contributions dashed line */

/* In [data-theme="dark"] */
--invest-stock:   #818cf8;
--invest-sp500:   #60a5fa;
--invest-intl:    #2dd4bf;
--invest-bonds:   #fbbf24;
--invest-target:  #fb7185;
--invest-hysa:    #94a3b8;
--invest-contrib: rgba(148, 163, 184, 0.5);
```

These colors were chosen to:
1. Pass WCAG AA contrast on `var(--surface)` in both light and dark modes.
2. Pass deuteranopia and protanopia simulation for the 3 default funds (indigo, amber, slate).
3. Not overlap with `--s1-color` (#3b82f6) or `--s2-color` (#7c3aed) — avoids confusion with mortgage scenarios.
4. HYSA is intentionally muted — it should read as the "baseline" at a glance.

---

## Implementation Order (Follow This Exactly)

Build in this order. Each step has a clear done state before moving to the next.

### Step 1 — Section Navigation (R-15) ⚡ Do this first

The existing `<header>` / app bar needs a tab nav added to it. This is the architectural foundation everything else sits inside.

**What to build:**
- Add two tab buttons to the header: `"Mortgage vs. Invest"` and `"Investment Growth"`
- Clicking either tab scrolls smoothly to `#mortgage-calculator` or `#investment-calculator`
- The active tab updates based on scroll position (IntersectionObserver or scroll event)
- Add `id="mortgage-calculator"` to the existing first section if it doesn't already exist
- The nav is sticky (already handled by the existing `.app-bar` sticky behavior)

**Done when:** Both tabs visible in header, clicking either scrolls correctly, active state updates on scroll.

---

### Step 2 — Section Divider + Shell (R-15 cont.)

Before the closing `</body>`, add the investment section HTML shell:

```html
<!-- ═══════════════════════════════════════════════
     INVESTMENT GROWTH CALCULATOR SECTION
     ═══════════════════════════════════════════════ -->
<div class="section-divider" aria-hidden="true">
  <span class="divider-ornament">◆</span>
</div>

<section id="investment-calculator" class="invest-section">
  <div class="invest-container">
    <header class="invest-header">
      <h2 class="invest-title">Investment Growth Calculator</h2>
      <p class="invest-subtitle">How does your money grow over time across different fund types?</p>
    </header>
    <!-- Components mount here in Steps 3–9 -->
  </div>
</section>
```

**Section layout CSS:**
```css
.invest-section {
  width: 95vw;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 1.6rem 6rem;
}

.invest-container {
  display: flex;
  flex-direction: column;
  gap: 1.6rem;
}

.section-divider {
  width: 95vw;
  max-width: 1600px;
  margin: 6rem auto 0;
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.section-divider::before,
.section-divider::after {
  content: '';
  flex: 1;
  height: 1px;
  background: var(--border);
}

.divider-ornament {
  color: var(--border);
  font-size: 0.75rem;
}

.invest-header {
  text-align: center;
  padding: 3rem 0 1rem;
}

.invest-title {
  font-size: var(--text-2xl);
  font-weight: 800;
  margin-bottom: 0.5rem;
}

.invest-subtitle {
  font-size: var(--text-base);
  color: var(--text-secondary);
}
```

**Done when:** Visible section break and header render correctly on page.

---

### Step 3 — Fund Config Object (Data Layer)

Before building any UI, define the fund config. This drives everything else.

```javascript
const FUND_CONFIG = {
  stock: {
    id: 'stock',
    label: 'Total US Stock Market',
    ticker: 'e.g. VTSAX / VTI',
    grossReturn: 0.102,
    expenseRatio: 0.0003,
    color: '--invest-stock',
    defaultOn: true,
  },
  sp500: {
    id: 'sp500',
    label: 'S&P 500 Index',
    ticker: 'e.g. VOO / FXAIX',
    grossReturn: 0.100,
    expenseRatio: 0.0003,
    color: '--invest-sp500',
    defaultOn: false,
  },
  intl: {
    id: 'intl',
    label: 'International Developed',
    ticker: 'e.g. VXUS / FZILX',
    grossReturn: 0.058,
    expenseRatio: 0.0006,
    color: '--invest-intl',
    defaultOn: false,
  },
  bonds: {
    id: 'bonds',
    label: 'US Bond Market',
    ticker: 'e.g. BND / VBTLX',
    grossReturn: 0.045,
    expenseRatio: 0.0003,
    color: '--invest-bonds',
    defaultOn: true,
  },
  target: {
    id: 'target',
    label: 'Target Date 2050',
    ticker: 'e.g. VFIFX',
    grossReturn: 0.082,
    expenseRatio: 0.0012,
    color: '--invest-target',
    defaultOn: false,
  },
  hysa: {
    id: 'hysa',
    label: 'High-Yield Savings',
    ticker: 'e.g. Marcus, SoFi',
    grossReturn: 0.045,
    expenseRatio: null,          // HYSA has no expense ratio
    color: '--invest-hysa',
    defaultOn: true,
  },
};
```

**Done when:** Object is defined in `<script>` block, accessible to all subsequent functions.

---

### Step 4 — Calculation Engine (R-01, R-05, R-09, R-10)

Implement the math before building any UI that depends on it. Validate against the test cases below before proceeding.

#### 4a. Net Return
```javascript
function getNetReturn(fund, customRates) {
  const rate = customRates?.[fund.id]?.rate ?? fund.grossReturn;
  const er   = customRates?.[fund.id]?.er   ?? (fund.expenseRatio ?? 0);
  return rate - er;
}
```

#### 4b. Yearly Projection — No Contribution Increase (closed-form)
```javascript
// Returns array of length (years + 1): index 0 = Year 0 value = initialInvestment
function projectYearly(initialInvestment, monthlyContrib, years, annualNetReturn) {
  const r = annualNetReturn / 12;
  const results = [initialInvestment];

  for (let y = 1; y <= years; y++) {
    const n = y * 12;
    let fv;
    if (r === 0) {
      fv = initialInvestment + monthlyContrib * n;
    } else {
      const growth = Math.pow(1 + r, n);
      fv = initialInvestment * growth + monthlyContrib * ((growth - 1) / r);
    }
    results.push(fv);
  }
  return results;
}
```

#### 4c. Yearly Projection — With Contribution Increase (iterative)
```javascript
// annualContribIncrease: decimal (e.g. 0.03 for 3%)
function projectYearlyEscalating(initialInvestment, monthlyContrib, years, annualNetReturn, annualContribIncrease) {
  const r = annualNetReturn / 12;
  let portfolioValue = initialInvestment;
  const results = [initialInvestment];

  for (let y = 1; y <= years; y++) {
    const effectiveMonthly = monthlyContrib * Math.pow(1 + annualContribIncrease, y - 1);
    for (let m = 0; m < 12; m++) {
      portfolioValue = portfolioValue * (1 + r) + effectiveMonthly;
    }
    results.push(portfolioValue);
  }
  return results;
}
```

#### 4d. Contributions Array
```javascript
// Returns array of cumulative contributions at each year
function contributionsYearly(initialInvestment, monthlyContrib, years, annualContribIncrease = 0) {
  const results = [initialInvestment];
  let cumulative = initialInvestment;

  for (let y = 1; y <= years; y++) {
    const effectiveMonthly = monthlyContrib * Math.pow(1 + annualContribIncrease, y - 1);
    cumulative += effectiveMonthly * 12;
    results.push(cumulative);
  }
  return results;
}
```

#### 4e. Main Compute Function
```javascript
// Call this on every input change. Returns all data needed to render UI.
function computeInvestment(inputs, customRates) {
  const { initialInvestment, monthlyContrib, years, annualContribIncrease, selectedFunds } = inputs;

  const useEscalating = annualContribIncrease > 0;
  const contribs = contributionsYearly(initialInvestment, monthlyContrib, years, annualContribIncrease);

  const projections = {};
  for (const fundId of selectedFunds) {
    const fund = FUND_CONFIG[fundId];
    const netReturn = getNetReturn(fund, customRates);
    projections[fundId] = useEscalating
      ? projectYearlyEscalating(initialInvestment, monthlyContrib, years, netReturn, annualContribIncrease)
      : projectYearly(initialInvestment, monthlyContrib, years, netReturn);
  }

  // Find leading fund (highest final value)
  let leadingFundId = selectedFunds[0];
  for (const fundId of selectedFunds) {
    if (projections[fundId]?.[years] > (projections[leadingFundId]?.[years] ?? 0)) {
      leadingFundId = fundId;
    }
  }

  const leadingFinalValue = projections[leadingFundId]?.[years] ?? 0;
  const totalContributions = contribs[years];
  const totalGains = leadingFinalValue - totalContributions;
  const growthMultiple = totalContributions > 0 ? leadingFinalValue / totalContributions : 0;

  return { projections, contribs, leadingFundId, leadingFinalValue, totalContributions, totalGains, growthMultiple };
}
```

#### ✅ Validation Test Cases — Run These Before Building UI

**Test 1: Basic (no escalation)**
```
initialInvestment = 10000, monthlyContrib = 500, years = 25
Total US Stock (netReturn = 0.1017): FV ≈ $723,441
US Bond Market (netReturn = 0.0447): FV ≈ $296,164
HYSA (netReturn = 0.0450):           FV ≈ $298,206
totalContributions = $160,000
```
Values should match within $100.

**Test 2: With contribution escalation**
```
initialInvestment = 10000, monthlyContrib = 500, years = 25, annualContribIncrease = 0.03
Total US Stock (netReturn = 0.1017): FV ≈ $1,021,753
totalContributions ≈ $223,121
```

**Test 3: Fee impact**
```
initialInvestment = 50000, monthlyContrib = 1000, years = 30
At 0.03% ER (net = 9.97%): FV ≈ $2,381,000
At 1.00% ER (net = 9.00%): FV ≈ $1,841,000
Fee cost ≈ $540,000
```

**Done when:** `computeInvestment()` returns validated values matching the above within $100.

---

### Step 5 — Input Bar (R-03, R-04, R-06, R-07, R-08, R-10)

#### HTML Structure
```html
<div class="invest-input-bar card">
  <!-- Row 1: 3 sliders -->
  <div class="invest-inputs-grid">
    <div class="input-group">
      <label for="invest-initial">Initial Investment</label>
      <div class="input-row">
        <span class="unit">$</span>
        <input type="number" id="invest-initial-num" value="10000" min="0" max="1000000" step="500">
      </div>
      <input type="range" id="invest-initial" min="0" max="1000000" step="500" value="10000">
    </div>

    <div class="input-group">
      <label for="invest-monthly">Monthly Contribution</label>
      <div class="input-row">
        <span class="unit">$</span>
        <input type="number" id="invest-monthly-num" value="500" min="0" max="10000" step="50">
      </div>
      <input type="range" id="invest-monthly" min="0" max="10000" step="50" value="500">
      <p class="invest-hint">Tip: Many people increase contributions yearly →</p>
    </div>

    <div class="input-group">
      <label for="invest-years">Time Horizon</label>
      <div class="input-row">
        <input type="number" id="invest-years-num" value="25" min="1" max="50" step="1">
        <span class="unit">yrs</span>
      </div>
      <input type="range" id="invest-years" min="1" max="50" step="1" value="25">
    </div>
  </div>

  <!-- Row 2: Fund pills -->
  <div class="invest-funds-row">
    <span class="invest-funds-label">Fund Types</span>
    <div class="invest-pills" id="invest-pills">
      <!-- Pills generated by JS from FUND_CONFIG -->
    </div>
    <p class="invest-nominal-note">Returns shown are nominal (not adjusted for inflation)</p>
  </div>

  <!-- Row 3: Customize toggle -->
  <div class="advanced-toggle" id="invest-advanced-toggle">
    <span>⚙ Customize Assumptions — adjust returns, fees, and contribution growth</span>
    <span class="chevron">▾</span>
  </div>
  <div class="advanced-body" id="invest-advanced-body">
    <!-- Generated by JS based on selected funds -->
  </div>
</div>
```

**Key CSS to add:**
```css
.invest-inputs-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 2rem;
  margin-bottom: 1.6rem;
}

.invest-hint {
  font-size: var(--text-sm);
  color: var(--accent);
  margin-top: 0.25rem;
  cursor: pointer;  /* clicking it opens the advanced panel */
}

.invest-funds-row {
  margin-bottom: 1rem;
}

.invest-funds-label {
  font-size: var(--text-sm);
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  display: block;
  margin-bottom: 0.5rem;
}

.invest-pills {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.invest-pill {
  display: flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.4rem 0.85rem;
  border-radius: 999px;
  border: 1.5px solid var(--border);
  font-size: var(--text-sm);
  font-weight: 600;
  cursor: pointer;
  background: transparent;
  color: var(--text);
  transition: all 0.15s;
  font-family: inherit;
}

.invest-pill .pill-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  flex-shrink: 0;
}

/* Active state — set via JS using inline style for the fund color */
.invest-pill.active {
  color: white;
  border-color: transparent;
}

.invest-nominal-note {
  font-size: var(--text-sm);
  color: var(--text-secondary);
  font-style: italic;
}

/* Responsive */
@media (max-width: 1023px) {
  .invest-inputs-grid {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 767px) {
  .invest-inputs-grid {
    grid-template-columns: 1fr;
  }
}
```

**JS — Generate pills from config:**
```javascript
function renderFundPills(selectedFunds) {
  const container = document.getElementById('invest-pills');
  container.innerHTML = '';
  for (const [id, fund] of Object.entries(FUND_CONFIG)) {
    const isActive = selectedFunds.includes(id);
    const color = getComputedStyle(document.documentElement).getPropertyValue(fund.color).trim();

    const btn = document.createElement('button');
    btn.className = 'invest-pill' + (isActive ? ' active' : '');
    btn.dataset.fundId = id;
    if (isActive) {
      btn.style.backgroundColor = color;
      btn.style.borderColor = color;
    }

    const dot = document.createElement('span');
    dot.className = 'pill-dot';
    dot.style.backgroundColor = color;
    if (isActive) dot.style.backgroundColor = 'rgba(255,255,255,0.8)';

    btn.appendChild(dot);
    btn.appendChild(document.createTextNode(fund.label));
    btn.addEventListener('click', () => toggleFund(id));
    container.appendChild(btn);
  }
}
```

**JS — Customize accordion:**
The advanced panel should dynamically generate sliders only for SELECTED funds. Regenerate it whenever selected funds change.

```javascript
function renderAdvancedPanel(selectedFunds, customRates, annualContribIncrease) {
  const body = document.getElementById('invest-advanced-body');
  // Build HTML for: contribution increase slider (top), then per-fund rate+ER sliders
  // Each fund group shows: fund label, net return badge (updates live), rate slider, ER slider (or N/A for HYSA)
  // Bottom: "Reset to Defaults" button
}
```

**Done when:** Sliders update state, pills toggle funds on/off, advanced panel shows per-selected-fund sliders.

---

### Step 6 — Banner, Disclaimer, and Stat Cards (R-02, R-03, R-04, R-11, R-12)

Mount these above the chart:

```html
<!-- Summary Banner -->
<div class="invest-banner card" id="invest-banner">
  <div class="invest-banner-eyebrow">Projected Portfolio Value</div>
  <div class="invest-banner-hero">
    <span class="invest-banner-amount" id="invest-banner-amount">$847,214</span>
    <span class="invest-banner-after">after <span id="invest-banner-years">25</span> years</span>
  </div>
  <div class="invest-banner-fund" id="invest-banner-fund">in Total US Stock Market</div>
  <div class="invest-banner-detail">
    <span id="invest-banner-gains">$647,214 in estimated gains</span>
    <span class="invest-banner-dot">·</span>
    <span id="invest-banner-contribs">$200,000 contributed</span>
  </div>
  <div class="invest-banner-comparison" id="invest-banner-comparison"></div>
</div>

<!-- Disclaimer — always visible, never dismissable -->
<p class="invest-disclaimer" id="invest-disclaimer">
  <span class="invest-disclaimer-icon">ⓘ</span>
  Projections use historical averages and do not guarantee future results.
  Actual returns vary year to year, including negative years. Not financial advice.
</p>

<!-- Stat Cards -->
<div class="invest-stat-cards">
  <div class="card invest-stat-card">
    <div class="stat-card-label">What You Put In</div>
    <div class="stat-card-hero" id="invest-stat-contribs">$200,000</div>
    <div class="stat-card-sub">total contributed</div>
    <div class="stat-card-detail">
      <div id="invest-stat-initial-line"></div>
      <div id="invest-stat-monthly-line"></div>
    </div>
  </div>
  <div class="card invest-stat-card invest-stat-card--returns">
    <div class="stat-card-label" id="invest-stat-returns-label">Projected Returns — <span id="invest-stat-fund-name"></span></div>
    <div class="stat-card-hero invest-stat-multiple" id="invest-stat-multiple">4.2×</div>
    <div class="stat-card-sub">growth multiple</div>
    <div class="stat-card-detail">
      <div id="invest-stat-fv-line"></div>
      <div id="invest-stat-gains-line"></div>
    </div>
  </div>
</div>
```

**Key CSS:**
```css
.invest-banner {
  border-left: 4px solid var(--invest-stock);  /* update via JS to leading fund color */
  padding: 1.5rem 2rem;
}

.invest-banner-eyebrow {
  font-size: var(--text-sm);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  color: var(--text-secondary);
  margin-bottom: 0.25rem;
}

.invest-banner-hero {
  display: flex;
  align-items: baseline;
  gap: 0.75rem;
  margin-bottom: 0.25rem;
}

.invest-banner-amount {
  font-size: var(--text-2xl);
  font-weight: 800;
  letter-spacing: -1px;
}

.invest-banner-after {
  font-size: var(--text-lg);
  font-weight: 400;
  color: var(--text-secondary);
}

.invest-banner-fund {
  font-size: var(--text-base);
  font-weight: 500;
  margin-bottom: 0.5rem;
}

.invest-banner-detail {
  font-size: var(--text-sm);
  color: var(--text-secondary);
  display: flex;
  gap: 0.5rem;
}

.invest-disclaimer {
  font-size: var(--text-sm);
  color: var(--text-secondary);
  line-height: 1.4;
  display: flex;
  align-items: flex-start;
  gap: 0.4rem;
}

.invest-stat-cards {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.invest-stat-card {
  padding: 1.25rem 1.5rem;
}

.stat-card-label {
  font-size: var(--text-sm);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  color: var(--text-secondary);
  margin-bottom: 0.5rem;
}

.stat-card-hero {
  font-size: var(--text-xl);
  font-weight: 700;
  letter-spacing: -0.5px;
  margin-bottom: 0.15rem;
}

.invest-stat-multiple {
  font-size: var(--text-2xl);
  font-weight: 800;
  /* color set via JS to leading fund color */
}

@media (max-width: 767px) {
  .invest-stat-cards {
    grid-template-columns: 1fr;
  }
  /* Returns card appears first on mobile */
  .invest-stat-card--returns {
    order: -1;
  }
}
```

**Done when:** Banner shows correct leading fund value with "Projected" eyebrow label. Disclaimer always visible. Stat cards show growth multiple as hero number.

---

### Step 7 — Chart (R-01, R-02, R-13, R-14)

```html
<div class="card invest-chart-card">
  <div class="invest-chart-header">
    <div>
      <h3 class="invest-chart-title">Investment Growth Over Time</h3>
      <p class="invest-chart-sub">Projected values based on historical average annual returns</p>
    </div>
    <button class="invest-info-btn" id="invest-info-btn" aria-label="About these projections">ℹ️</button>
  </div>

  <!-- Info popover -->
  <div class="invest-popover" id="invest-popover" hidden>
    <p>This chart shows projected growth based on historical average annual returns. Actual year-to-year returns vary significantly — including negative years. Historically, longer time horizons have reduced the impact of short-term volatility.</p>
    <p style="margin-top:0.5rem">Returns shown are nominal (not adjusted for inflation).</p>
  </div>

  <div class="chart-wrapper">
    <canvas id="investChart"></canvas>
  </div>
</div>
```

**Chart.js Configuration:**
```javascript
function initInvestChart() {
  const ctx = document.getElementById('investChart').getContext('2d');
  investChart = new Chart(ctx, {
    type: 'line',
    data: { labels: [], datasets: [] },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: {
        duration: 800,
        easing: 'easeInOut',
        // On subsequent updates (input changes), faster:
        // call chart.update('active') for 300ms morphing
      },
      interaction: { mode: 'index', intersect: false },
      plugins: {
        legend: {
          position: 'top',
          labels: {
            usePointStyle: true,
            padding: 16,
            font: { family: 'Inter', size: 13 },
          },
        },
        tooltip: {
          callbacks: {
            title: (items) => `Year ${items[0].label}`,
            label: (item) => {
              if (item.dataset.isContributions) {
                return ` Contributions: ${formatCurrency(item.raw)}`;
              }
              return ` ${item.dataset.label}: ${formatCurrency(item.raw)}`;
            },
            afterBody: (items) => {
              // Show gains vs contributions for each fund line
              const contribValue = items.find(i => i.dataset.isContributions)?.raw;
              if (!contribValue) return [];
              return items
                .filter(i => !i.dataset.isContributions)
                .map(i => `  Gains (${i.dataset.label}): ${formatCurrency(i.raw - contribValue)}`);
            },
          },
        },
      },
      scales: {
        x: {
          title: { display: true, text: 'Year', color: 'var(--text-secondary)' },
          grid: { color: 'rgba(0,0,0,0.04)' },
          ticks: { color: 'var(--text-secondary)', font: { family: 'Inter' } },
        },
        y: {
          title: { display: true, text: 'Portfolio Value', color: 'var(--text-secondary)' },
          grid: { color: 'rgba(0,0,0,0.04)' },
          ticks: {
            color: 'var(--text-secondary)',
            font: { family: 'Inter' },
            callback: (v) => formatCurrencyShort(v),  // "$100K", "$1M"
          },
        },
      },
    },
  });
}

function updateInvestChart(result, inputs) {
  const { projections, contribs, leadingFundId } = result;
  const { years, selectedFunds } = inputs;
  const labels = Array.from({ length: years + 1 }, (_, i) => i);

  const datasets = [];

  // Contributions dataset — always first, always visible, NOT clickable in legend
  datasets.push({
    label: 'Your Contributions',
    data: contribs,
    borderColor: getCssVar('--invest-contrib'),
    backgroundColor: 'transparent',
    borderDash: [8, 4],
    borderWidth: 1.5,
    pointRadius: 0,
    order: 999,  // renders last (on top of fills but behind points)
    isContributions: true,  // custom flag for tooltip
  });

  // Fund datasets
  for (const fundId of selectedFunds) {
    const fund = FUND_CONFIG[fundId];
    const color = getCssVar(fund.color);
    const isLeading = fundId === leadingFundId;

    datasets.push({
      label: fund.label,
      data: projections[fundId],
      borderColor: color,
      backgroundColor: isLeading
        ? hexToRgba(color, 0.06)  // subtle gains fill only for leading fund
        : 'transparent',
      fill: isLeading ? { target: 0, above: hexToRgba(color, 0.06) } : false,  // fill above contributions line (index 0)
      borderWidth: 2.5,
      tension: 0.3,
      pointRadius: 0,
      pointHoverRadius: 5,
      order: isLeading ? 1 : 2,
    });
  }

  investChart.data.labels = labels;
  investChart.data.datasets = datasets;
  investChart.update('active');  // 300ms smooth morph on input changes
}
```

**Utility functions:**
```javascript
function getCssVar(name) {
  return getComputedStyle(document.documentElement).getPropertyValue(name).trim();
}

function hexToRgba(hex, alpha) {
  // Strip leading #
  const h = hex.replace('#', '');
  const r = parseInt(h.substring(0,2), 16);
  const g = parseInt(h.substring(2,4), 16);
  const b = parseInt(h.substring(4,6), 16);
  return `rgba(${r},${g},${b},${alpha})`;
}

function formatCurrency(value) {
  return '$' + Math.round(value).toLocaleString('en-US');
}

function formatCurrencyShort(value) {
  if (value >= 1_000_000) return '$' + (value / 1_000_000).toFixed(1) + 'M';
  if (value >= 1_000) return '$' + Math.round(value / 1_000) + 'K';
  return '$' + Math.round(value);
}
```

**Done when:** Chart renders with fund lines + dashed contributions line. Tooltip shows year, values, and gains. Gains fill visible on leading fund only.

---

### Step 8 — Data Table (R-07)

```html
<div class="invest-table-section">
  <button class="invest-table-toggle" id="invest-table-toggle" aria-expanded="false">
    <span>Show Year-by-Year Breakdown</span>
    <span class="chevron">▾</span>
  </button>
  <div class="invest-table-body" id="invest-table-body">
    <div class="data-table-wrap">
      <table class="data-table" id="invest-data-table">
        <!-- Generated by JS -->
      </table>
    </div>
  </div>
</div>
```

**JS — Render table:**
```javascript
function renderInvestTable(result, inputs) {
  const { projections, contribs } = result;
  const { years, selectedFunds } = inputs;
  const table = document.getElementById('invest-data-table');

  // Header
  const headerCells = ['Year', 'Your Contributions', ...selectedFunds.map(id => `${FUND_CONFIG[id].label} (Projected)`)];

  // Rows: year 0 through years, but only show 0, 1, then every year
  const rows = [];
  for (let y = 0; y <= years; y++) {
    rows.push([
      y,
      contribs[y],
      ...selectedFunds.map(id => projections[id][y])
    ]);
  }

  // Build table HTML
  // - Contributions column gets .contrib-col class (subtle gray background)
  // - Final row gets .final-row class (green highlight)
  // - All currency values formatted with formatCurrency()
}
```

**Done when:** Table expands/collapses, "Your Contributions" column has distinct background, "Projected" in column headers, final row highlighted.

---

### Step 9 — Fee Impact Card (R-11)

Only visible when the Customize panel is open. Insert it between stat cards and chart.

```javascript
function renderFeeCard(result, inputs, customRates) {
  const card = document.getElementById('invest-fee-card');
  const { leadingFundId } = result;
  const { years, selectedFunds } = inputs;

  if (!document.getElementById('invest-advanced-body').classList.contains('open')) {
    card.hidden = true;
    return;
  }

  const fund = FUND_CONFIG[leadingFundId];
  const er = customRates?.[leadingFundId]?.er ?? (fund.expenseRatio ?? 0);
  const grossRate = customRates?.[leadingFundId]?.rate ?? fund.grossReturn;

  if (fund.expenseRatio === null) { card.hidden = true; return; }  // HYSA

  // Calculate FV at gross rate vs net rate
  const { initialInvestment, monthlyContrib, annualContribIncrease } = inputs;
  const fvGross = projectYearly(initialInvestment, monthlyContrib, years, grossRate)[years];
  const fvNet   = projectYearly(initialInvestment, monthlyContrib, years, grossRate - er)[years];
  const feeCost = fvGross - fvNet;

  const isHighFee = er > 0.005;

  card.hidden = false;
  // Render: fee cost amount, savings callout if isHighFee
}
```

**Done when:** Fee card appears only when Advanced is open, counter-animates on ER slider change, shows green savings callout when ER > 0.5%.

---

### Step 10 — Wire Everything Together

Single `updateInvestment()` function called on every input change:

```javascript
function updateInvestment() {
  const inputs = readInvestInputs();
  const result = computeInvestment(inputs, customRates);

  updateInvestBanner(result, inputs);
  updateInvestStatCards(result, inputs);
  updateInvestChart(result, inputs);
  renderInvestTable(result, inputs);
  renderFeeCard(result, inputs, customRates);
}

// Debounce for slider drag
const debouncedUpdate = debounce(updateInvestment, 16);

// Attach to all sliders and number inputs in the invest section
// On release (change event): call updateInvestment() immediately + trigger counter animations
// On drag (input event): call debouncedUpdate() — no counter animation during drag
```

**Counter animation rules:**
- `animateValue()` fires on `change` events (slider release), NOT on `input` events (active drag)
- This prevents nausea-inducing rapid digit cycling during drag
- After drag releases, animate to final value

**Reduced motion:**
```javascript
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
// Pass this to animateValue — if true, duration = 0
```

---

### Step 11 — Mobile Polish

Confirm at 375px viewport:
- Summary banner wraps to 2 lines without overflow
- Disclaimer is visible without scrolling
- Fund pills wrap to 2 rows cleanly
- Chart is minimum 300px height and readable
- Input accordion opens without layout shift
- Data table is horizontally scrollable with Year column sticky

---

### Step 12 — Dark Mode Verification

Test with `document.documentElement.setAttribute('data-theme', 'dark')`:
- All new elements use CSS variables (no hardcoded colors)
- Chart axis colors, grid lines, and tooltip use dark mode palette
- Chart.js datasets reference CSS variables via `getCssVar()` — note that Chart.js reads these at render time, so re-call `updateInvestChart()` after theme toggle

---

## Edge States — Handle All of These

| State | Expected behavior |
|-------|------------------|
| All funds deselected | Chart shows only contributions line. Banner: "Select a fund type above to see projected growth." Stat returns card shows "—". |
| Initial = $0, Monthly = $0 | Banner: "Enter an investment amount to see projections." Chart shows empty state message. |
| Initial = $0, Monthly > $0 | Works normally (starting from scratch is valid). |
| Time horizon = 1 year | Chart X-axis shows 0–1 with monthly data points. |
| All rates set to 0% | Lines overlap contributions line. Banner shows "At 0% return..." Growth multiple = 1.0×. |
| ER > gross return (net return negative) | Line goes below contributions line. Banner shows projected value correctly (it will be less than contributions). Fee card highlights prominently. |

---

## Language Rules (Non-Negotiable)

These were decided by the Financial Advisor SME (Dana) and UXD Lead (Sam). Do not revert them.

| ❌ Never write | ✅ Always write |
|--------------|----------------|
| "grows to" | "projected value" or "estimated value" |
| "will be worth" | "could be projected at" |
| "you will have" | "your projected portfolio" |
| "guaranteed" | *(don't use this word at all)* |
| Just the fund name in column header | "[Fund Name] (Projected)" |
| "gains" alone in a hero context | "estimated gains" |

---

## What NOT to Build in This Pass

These are P1 and P2. Do not build them now.

- Inflation toggle (P1)
- Milestone markers on chart (P1)
- Cross-link callout from mortgage section (P1)
- URL parameter pre-fill (P2)
- Volatility bands / confidence intervals (P2)
- Tax-advantaged account toggle (P2)
- Retirement drawdown (P2)

---

## Acceptance Criteria Checklist

Before handing back to the user, verify all P0 requirements:

- [ ] **R-01** Multi-line chart renders correct projected values for all selected fund types
- [ ] **R-02** Contributions dashed line always visible, cannot be toggled off via legend
- [ ] **R-03** Banner uses "Projected" as eyebrow label; counter-animates on input change
- [ ] **R-04** Disclaimer is always visible, never behind a toggle or scroll barrier
- [ ] **R-05** 3 sliders + fund selector all work with real-time updates
- [ ] **R-06** Fund pill toggles on/off; chart updates immediately; pill color matches chart line
- [ ] **R-07** Default rates match spec table (net of fees): Stock 10.2%, Bonds 4.47%, HYSA 4.5%
- [ ] **R-08** Customize toggle reveals per-fund rate + ER sliders + contribution increase
- [ ] **R-09** Per-fund ER sliders present; HYSA shows N/A; net return badge updates live
- [ ] **R-10** Annual contribution increase slider at 0% default; computation switches to iterative when > 0
- [ ] **R-11** Fee card visible only when Advanced open; counter-animates; green callout when ER > 0.5%
- [ ] **R-12** Stat cards show growth multiple (hero) and projected final value
- [ ] **R-13** Data table collapses/expands; "Your Contributions" column always present; headers say "Projected"
- [ ] **R-14** ℹ️ opens popover (not native title tooltip); popover dismissable by click-outside or Escape
- [ ] **R-15** Tab nav in header; clicking either tab scrolls to correct section; active state updates on scroll
- [ ] **R-16** Layout correct at 375px, 768px, 1024px, 1440px
- [ ] **R-17** All recalculations < 50ms with 6 funds active + contribution increase on (test in DevTools)
- [ ] **R-18** Dark mode: toggle theme and verify no hardcoded colors, charts re-render correctly

**Validation numbers to verify:**
- Default state (Initial $10K, Monthly $500, 25 years): Stock ≈ $723K, Bonds ≈ $296K, HYSA ≈ $298K
- Growth multiple for Stock: ≈ 4.5×
- With 3% annual contribution increase: Stock ≈ $1.02M

---

*Handoff prepared March 14, 2026. All design decisions finalized in prior session — see `investment-calculator-spec.md` and `investment-calculator-ui-spec.md` for full rationale.*
