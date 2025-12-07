---
title: "Discounted Cash Flow (DCF) Valuation: A Complete Guide"
date: 2025-01-15
weight: 22
description: "Master DCF valuation step-by-step. Learn to project cash flows, calculate WACC, determine terminal value, and build complete DCF models for company valuation."
tags: ["DCF valuation", "discounted cash flow", "company valuation", "intrinsic value", "financial modeling"]
series: ["Corporate Finance"]
series_order: 22
seo_title: "DCF Valuation Guide | Discounted Cash Flow Analysis"
seo_description: "Complete guide to DCF valuation - free cash flow projection, WACC calculation, terminal value, and step-by-step DCF model building for intrinsic value estimation."
keywords: ["DCF valuation", "discounted cash flow", "free cash flow", "terminal value", "WACC"]
ShowToc: true
TocOpen: true
---

## Introduction: The Gold Standard of Valuation

"An asset is worth the present value of its future cash flows." – Fundamental Finance Principle

The Discounted Cash Flow (DCF) method is considered the most theoretically sound approach to valuation. Unlike market-based methods that depend on what others are willing to pay, DCF focuses on fundamentals—the actual cash a business is expected to generate.

Mastering DCF is essential for anyone in investment analysis, corporate finance, or M&A.

---

## The DCF Concept

### Core Principle

The value of any asset is the present value of all future cash flows it will generate, discounted at an appropriate rate reflecting risk.

### The Formula

$$Enterprise\ Value = \sum_{t=1}^{n} \frac{FCF_t}{(1+WACC)^t} + \frac{Terminal\ Value}{(1+WACC)^n}$$

### Components

**1. Free Cash Flow (FCF):** Cash available to all capital providers
**2. Discount Rate (WACC):** Required return reflecting risk
**3. Terminal Value:** Value beyond explicit forecast period
**4. Present Value:** All future values brought to today

---

## Step 1: Project Free Cash Flows

### Free Cash Flow to Firm (FCFF)

$$FCFF = EBIT(1-T) + Depreciation - CapEx - \Delta Working\ Capital$$

**Breaking it down:**
- **EBIT(1-T):** Operating profit after tax (NOPAT)
- **+ Depreciation:** Add back non-cash expense
- **- CapEx:** Capital expenditures (cash outflow)
- **- ΔWC:** Change in working capital (investment in operations)

### Free Cash Flow to Equity (FCFE)

$$FCFE = Net\ Income + Depreciation - CapEx - \Delta WC + Net\ Borrowing$$

**FCFF vs FCFE:**
| Use FCFF | Use FCFE |
|----------|----------|
| Valuing enterprise | Valuing equity directly |
| Discount at WACC | Discount at cost of equity |
| More common in M&A | More common for equity analysis |

### Building the Forecast

**Forecast Period:** Typically 5-10 years

**Key Drivers to Forecast:**
1. Revenue growth
2. Operating margins
3. Tax rate
4. Capital expenditure
5. Working capital requirements

### Example: Cash Flow Projection

**Assumptions:**
- Base revenue: ₹100 crore
- Revenue growth: 15% → 10% (declining)
- EBITDA margin: 25%
- Depreciation: 10% of revenue
- Tax rate: 25%
- CapEx: 12% of revenue
- ΔWC: 5% of revenue growth

**Projection (₹ crore):**

| Year | 1 | 2 | 3 | 4 | 5 |
|------|---|---|---|---|---|
| Revenue | 115 | 130 | 146 | 161 | 175 |
| Growth % | 15% | 13% | 12% | 10% | 9% |
| EBITDA | 28.8 | 32.5 | 36.5 | 40.3 | 43.8 |
| D&A | 11.5 | 13.0 | 14.6 | 16.1 | 17.5 |
| EBIT | 17.3 | 19.5 | 21.9 | 24.2 | 26.3 |
| Tax (25%) | 4.3 | 4.9 | 5.5 | 6.0 | 6.6 |
| NOPAT | 13.0 | 14.6 | 16.4 | 18.1 | 19.7 |
| + D&A | 11.5 | 13.0 | 14.6 | 16.1 | 17.5 |
| - CapEx | -13.8 | -15.6 | -17.5 | -19.3 | -21.0 |
| - ΔWC | -0.8 | -0.8 | -0.8 | -0.8 | -0.7 |
| **FCFF** | **9.9** | **11.3** | **12.7** | **14.1** | **15.5** |

---

## Step 2: Determine Discount Rate (WACC)

### WACC Formula

$$WACC = w_E \times r_E + w_D \times r_D \times (1-T)$$

### Cost of Equity (CAPM)

$$r_E = R_f + \beta \times (R_m - R_f)$$

**Indian Context:**
- Risk-free rate (Rf): 10-year G-Sec yield (~7%)
- Beta: Company specific (industry + leverage)
- Market risk premium (Rm - Rf): 5-7%

**Example:**
$$r_E = 7\% + 1.2 \times 6\% = 14.2\%$$

### Cost of Debt

$$r_D = Interest\ Rate \times (1-T)$$

**Example:**
$$r_D = 10\% \times (1-0.25) = 7.5\%$$

### Calculating Weights

**Use market values:**
- Market cap for equity
- Book value for debt (approximation)

**Example:**
- Market Cap: ₹200 crore
- Debt: ₹50 crore
- Total: ₹250 crore
- Weight of Equity: 80%
- Weight of Debt: 20%

### WACC Calculation

$$WACC = 0.80 \times 14.2\% + 0.20 \times 7.5\% = 11.36\% + 1.5\% = 12.86\%$$

**Round to:** 13%

---

## Step 3: Calculate Terminal Value

### Why Terminal Value?

- Can't forecast forever
- Business continues beyond explicit forecast
- Terminal value often 50-80% of total DCF value

### Method 1: Perpetuity Growth Model

$$TV = \frac{FCF_{n+1}}{WACC - g}$$

**Where:**
- FCFn+1 = FCF in year after forecast period
- g = Perpetual growth rate

**Choosing Growth Rate (g):**
- Should be ≤ long-term economy growth
- India: 4-6% nominal GDP growth
- Typically: 3-5%

**Example:**
- Year 5 FCF: ₹15.5 crore
- Year 6 FCF: ₹15.5 × 1.04 = ₹16.1 crore
- WACC: 13%
- g: 4%

$$TV = \frac{16.1}{0.13 - 0.04} = \frac{16.1}{0.09} = ₹179\ crore$$

### Method 2: Exit Multiple

$$TV = EBITDA_n \times Exit\ Multiple$$

**Choosing Exit Multiple:**
- Based on comparable company multiples
- Or implied multiple from perpetuity method

**Example:**
- Year 5 EBITDA: ₹43.8 crore
- Exit multiple: 8x EV/EBITDA

$$TV = 43.8 \times 8 = ₹350\ crore$$

### Which Method to Use?

| Perpetuity Growth | Exit Multiple |
|-------------------|---------------|
| More theoretically pure | Market-based |
| Requires growth assumption | Requires multiple assumption |
| Can cross-check with implied multiple | Can cross-check with implied growth |

**Best Practice:** Calculate both and compare

---

## Step 4: Calculate Present Value

### Discount Cash Flows

**Present Value Formula:**
$$PV = \frac{FV}{(1+r)^t}$$

**Example with 13% WACC:**

| Year | FCF | Discount Factor | PV |
|------|-----|-----------------|-----|
| 1 | 9.9 | 0.885 | 8.8 |
| 2 | 11.3 | 0.783 | 8.8 |
| 3 | 12.7 | 0.693 | 8.8 |
| 4 | 14.1 | 0.613 | 8.6 |
| 5 | 15.5 | 0.543 | 8.4 |
| **PV of FCFs** | | | **43.4** |

### Discount Terminal Value

$$PV(TV) = \frac{179}{(1.13)^5} = \frac{179}{1.842} = ₹97.2\ crore$$

### Sum to Get Enterprise Value

$$EV = PV(FCFs) + PV(TV) = 43.4 + 97.2 = ₹140.6\ crore$$

---

## Step 5: Calculate Equity Value

### From Enterprise to Equity

$$Equity\ Value = EV - Net\ Debt$$

**Example:**
- Enterprise Value: ₹140.6 crore
- Debt: ₹50 crore
- Cash: ₹10 crore
- Net Debt: ₹40 crore

$$Equity\ Value = 140.6 - 40 = ₹100.6\ crore$$

### Per Share Value

$$Value\ per\ Share = \frac{Equity\ Value}{Shares\ Outstanding}$$

**Example:**
- Equity Value: ₹100.6 crore
- Shares: 10 crore

$$Value\ per\ Share = \frac{100.6}{10} = ₹10.06$$

---

## DCF Sensitivity Analysis

### Why Sensitivity Matters

Small changes in key assumptions can significantly change the valuation.

### Key Sensitivities

**1. WACC Sensitivity**

| WACC | Terminal Value | Enterprise Value |
|------|---------------|-----------------|
| 11% | ₹230 | ₹173 |
| 12% | ₹201 | ₹155 |
| 13% | ₹179 | ₹141 |
| 14% | ₹161 | ₹129 |
| 15% | ₹146 | ₹119 |

**2. Terminal Growth Sensitivity**

| Growth (g) | Terminal Value | Enterprise Value |
|------------|---------------|-----------------|
| 3% | ₹161 | ₹131 |
| 4% | ₹179 | ₹141 |
| 5% | ₹201 | ₹153 |
| 6% | ₹230 | ₹168 |

**3. Combined Sensitivity (Data Table)**

| WACC \ g | 3% | 4% | 5% |
|----------|-----|-----|-----|
| 11% | ₹157 | ₹173 | ₹195 |
| 12% | ₹141 | ₹155 | ₹172 |
| 13% | ₹129 | ₹141 | ₹155 |
| 14% | ₹118 | ₹129 | ₹141 |

---

## DCF Best Practices

### Model Structure

**1. Separate Inputs**
- All assumptions in one place
- Easy to change and trace

**2. Clear Logic Flow**
- Revenue → Costs → EBITDA → FCF
- Each line has clear formula

**3. Error Checks**
- Balance sheet balances
- FCF reconciles
- Sanity checks on outputs

### Forecasting Guidelines

**Revenue:**
- Bottom-up (units × price) when possible
- Industry growth + market share
- Historical growth as anchor

**Margins:**
- Historical trends
- Industry benchmarks
- Realistic improvement trajectory

**CapEx:**
- Maintenance vs growth
- As % of revenue or depreciation multiple
- Industry norms

**Working Capital:**
- Days ratios (DSO, DIO, DPO)
- As % of revenue
- Historical patterns

### Terminal Value Discipline

**Perpetuity Growth:**
- Cannot exceed economy growth long-term
- Typical range: 2-5%
- Check implied exit multiple

**Exit Multiple:**
- Use current comparable multiples
- Adjust for cycle position
- Check implied growth rate

---

## Common DCF Mistakes

### Mistake 1: Inconsistent Growth

**Problem:** Revenue grows 30% but capex is flat
**Fix:** Link capex to revenue or capacity needs

### Mistake 2: Terminal Value Too High

**Problem:** TV is 90% of DCF value
**Fix:** Extend forecast period, lower growth rate, stress-test

### Mistake 3: Wrong Cash Flow

**Problem:** Using EBITDA instead of FCF
**Fix:** Properly calculate FCFF with capex and working capital

### Mistake 4: Mixing Equity and Enterprise

**Problem:** Discounting FCFF at cost of equity
**Fix:** FCFF → WACC, FCFE → Cost of Equity

### Mistake 5: Forgetting Non-Operating Items

**Problem:** Not adding excess cash or subtracting non-operating liabilities
**Fix:** Bridge from EV to Equity correctly

### Mistake 6: Ignoring Scenario Analysis

**Problem:** Single point estimate
**Fix:** Build multiple scenarios (base, bull, bear)

---

## DCF in Practice

### When DCF Works Best

- Stable, mature businesses
- Predictable cash flows
- Long investment horizons
- Fundamental value focus

### When DCF Struggles

- Early-stage companies (negative cash flows)
- Highly cyclical businesses
- Rapidly changing industries
- Distressed situations

### Complementing DCF

**Always triangulate with:**
- Comparable company analysis
- Precedent transactions (for M&A)
- Sanity checks (implied growth, margins)

---

## Key Takeaways

1. **DCF = PV of future FCF** – The fundamental valuation equation
2. **Four steps:** Project FCF → Determine WACC → Calculate TV → Sum PVs
3. **Terminal value is critical** – Often dominates, must be disciplined
4. **WACC reflects risk** – Higher risk = higher discount rate
5. **Sensitivity analysis is essential** – Small changes → big impact
6. **Triangulate results** – DCF alone is not enough
7. **Judgment matters** – Inputs require informed assumptions

---

## Disclaimer

*This article is for educational purposes only. DCF valuation requires professional judgment. Consult qualified professionals for actual valuations. This is not investment advice.*

---

## Frequently Asked Questions

**Q: Why is terminal value so large?**
A: Because it captures all value beyond the explicit forecast period (potentially infinite). Extend forecast period and be conservative with assumptions to reduce TV proportion.

**Q: FCFF or FCFE?**
A: FCFF (discount at WACC) is more common for enterprise valuation. FCFE (discount at cost of equity) for direct equity valuation. They should give same equity value if done correctly.

**Q: How do I forecast growth?**
A: Use historical trends, industry growth rates, company-specific factors (market share, new products), and management guidance. Be realistic, especially for later years.

**Q: What if cash flows are negative?**
A: DCF can still work if company eventually becomes cash flow positive. Model the path to profitability. For persistent negatives, other methods may be more appropriate.

**Q: How precise is DCF?**
A: DCF provides a range, not a precise number. Key value is in the analysis process and understanding value drivers, not in the final number.

*DCF is like a GPS for valuation—it doesn't guarantee you'll reach your destination perfectly, but it gives you direction based on your assumptions about the road ahead. The quality of your DCF depends on the quality of your assumptions, your understanding of the business, and your judgment in applying the framework.*
