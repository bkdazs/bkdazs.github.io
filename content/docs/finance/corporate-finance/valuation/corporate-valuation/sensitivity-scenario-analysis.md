---
title: "Sensitivity Analysis and Scenario Analysis: Managing Project Uncertainty"
date: 2025-01-15
weight: 4
description: "Master sensitivity and scenario analysis for capital budgeting. Learn to identify critical variables, build scenarios, and make better investment decisions under uncertainty."
tags: ["sensitivity analysis", "scenario analysis", "capital budgeting", "risk analysis", "project evaluation"]
series: ["Corporate Finance"]
series_order: 4
seo_title: "Sensitivity & Scenario Analysis | Project Risk Management"
seo_description: "Complete guide to sensitivity and scenario analysis in capital budgeting - identify risks, build scenarios, and make informed investment decisions."
keywords: ["sensitivity analysis", "scenario analysis", "capital budgeting", "risk management", "NPV analysis"]
ShowToc: true
TocOpen: true
---

## Introduction: The Illusion of Certainty

"Our project NPV is ₹85 lakh."

This single number looks precise and authoritative. But hidden within it are dozens of assumptions—sales growth of 15%, raw material costs at current levels, stable exchange rates, 5-year equipment life.

What if sales grow at only 10%? What if raw material prices spike? What if the equipment becomes obsolete in 3 years?

Sensitivity analysis and scenario analysis are powerful tools that expose the uncertainty hiding behind single-point estimates, helping managers make more informed decisions.

---

## The Problem with Single-Point Estimates

### Traditional NPV Calculation

**Typical Inputs:**
- Investment: ₹100 lakh
- Annual Revenue: ₹80 lakh
- Annual Costs: ₹50 lakh
- Project Life: 5 years
- Discount Rate: 12%

**NPV Calculation:**
Annual CF = ₹80L - ₹50L = ₹30 lakh
NPV = -100 + 30×PVIFA(12%, 5) = -100 + 30×3.605 = ₹8.15 lakh

**Decision:** Accept (NPV > 0)

### What Could Go Wrong?

| Variable | Assumed | Actual |
|----------|---------|--------|
| Revenue | ₹80L | ₹70L (competition) |
| Costs | ₹50L | ₹55L (inflation) |
| Life | 5 years | 4 years (obsolescence) |
| Rate | 12% | 14% (risk increases) |

**Reality:** The project might actually destroy value.

---

## What is Sensitivity Analysis?

### Definition

Sensitivity analysis examines how changes in one input variable affect the project's NPV, while holding all other variables constant.

### Purpose

- Identify which variables most impact project value
- Quantify the effect of estimation errors
- Focus attention on critical assumptions
- Improve decision-making under uncertainty

### Key Question

"What happens to NPV if [variable X] changes by ±10%, ±20%?"

---

## Conducting Sensitivity Analysis: Step-by-Step

### Step 1: Establish Base Case

**Base Case Assumptions:**
- Investment: ₹150 lakh
- Annual Sales: 10,000 units
- Price per Unit: ₹500
- Variable Cost per Unit: ₹300
- Fixed Costs: ₹10 lakh/year
- Project Life: 5 years
- Discount Rate: 12%

**Base Case NPV:**
Annual Revenue = 10,000 × ₹500 = ₹50 lakh
Annual Variable Cost = 10,000 × ₹300 = ₹30 lakh
Annual Fixed Cost = ₹10 lakh
Annual CF = ₹50L - ₹30L - ₹10L = ₹10 lakh

NPV = -150 + 10 × 3.605 = ₹-113.95 lakh

**Wait—this base case has negative NPV!** Let's adjust:

**Revised Base Case:**
- Investment: ₹50 lakh
- Annual Sales: 10,000 units
- Price per Unit: ₹500
- Variable Cost per Unit: ₹300
- Fixed Costs: ₹5 lakh/year
- Project Life: 5 years
- Discount Rate: 12%

Annual CF = 50L - 30L - 5L = ₹15 lakh
NPV = -50 + 15 × 3.605 = ₹4.08 lakh

**Base Case NPV: ₹4.08 lakh (Positive)**

### Step 2: Vary Each Input Individually

**Variable 1: Sales Volume (±20%)**

| Sales Volume | Annual CF | NPV | Change |
|-------------|-----------|-----|--------|
| 8,000 (−20%) | ₹11L | -₹10.35L | -₹14.43L |
| 10,000 (base) | ₹15L | ₹4.08L | — |
| 12,000 (+20%) | ₹19L | ₹18.50L | +₹14.42L |

**Variable 2: Selling Price (±10%)**

| Price | Annual CF | NPV | Change |
|-------|-----------|-----|--------|
| ₹450 (−10%) | ₹10L | -₹13.95L | -₹18.03L |
| ₹500 (base) | ₹15L | ₹4.08L | — |
| ₹550 (+10%) | ₹20L | ₹22.10L | +₹18.02L |

**Variable 3: Variable Cost (±10%)**

| Variable Cost | Annual CF | NPV | Change |
|---------------|-----------|-----|--------|
| ₹270 (−10%) | ₹18L | ₹14.89L | +₹10.81L |
| ₹300 (base) | ₹15L | ₹4.08L | — |
| ₹330 (+10%) | ₹12L | -₹6.74L | -₹10.82L |

### Step 3: Create Sensitivity Table

| Variable | -20% | -10% | Base | +10% | +20% |
|----------|------|------|------|------|------|
| Sales Volume | -₹10.35L | -₹3.13L | ₹4.08L | ₹11.28L | ₹18.50L |
| Selling Price | -₹31.98L | -₹13.95L | ₹4.08L | ₹22.10L | ₹40.13L |
| Variable Cost | ₹25.70L | ₹14.89L | ₹4.08L | -₹6.74L | -₹17.55L |
| Fixed Cost | ₹7.69L | ₹5.88L | ₹4.08L | ₹2.27L | ₹0.47L |

### Step 4: Identify Critical Variables

**Ranking by Sensitivity:**
1. **Selling Price** – Highest impact (±10% causes ₹18L swing)
2. **Variable Cost** – Second highest (±10% causes ₹10.8L swing)
3. **Sales Volume** – Moderate impact
4. **Fixed Cost** – Lowest impact

**Critical Insight:** Selling price is the most critical variable. Small pricing errors can turn a profitable project unprofitable.

---

## Spider Diagram: Visualizing Sensitivity

### Creating a Spider Chart

Plot NPV on Y-axis against % change in each variable on X-axis.

**Interpretation:**
- Steeper lines = More sensitive variables
- Lines crossing zero = Variables that can flip the decision
- Intersection point = Base case NPV

### Key Observations from Spider Diagrams

1. **Steep slope for selling price** – Most sensitive
2. **NPV crosses zero** at certain price/volume levels – Breakeven points
3. **Fixed cost line is relatively flat** – Less sensitive

---

## Breakeven Analysis

### Definition

The value of a variable at which NPV equals zero.

### Calculating Breakeven Values

**Breakeven Sales Volume:**
Set NPV = 0 and solve for quantity

₹0 = -50 + (Q × 200 - 5) × 3.605
50 = (200Q - 5) × 3.605
13.87 = 200Q - 5
Q = 9,435 units

**Breakeven Selling Price:**
₹0 = -50 + (10,000 × P - 30L - 5L) × 3.605
P = ₹488.87 per unit

### Breakeven Summary

| Variable | Breakeven Value | Margin from Base |
|----------|-----------------|-----------------|
| Sales Volume | 9,435 units | 565 units (5.7%) |
| Selling Price | ₹488.87 | ₹11.13 (2.2%) |
| Variable Cost | ₹311.13 | ₹11.13 (3.7%) |

**Warning:** Very thin margin on selling price—only 2.2% decrease makes NPV zero!

---

## Scenario Analysis: A Holistic Approach

### Limitation of Sensitivity Analysis

Variables don't change in isolation!

**Example:**
If economy slows down:
- Sales volume decreases
- Selling price falls (competitive pressure)
- Variable costs might increase (suppliers squeeze)

### What is Scenario Analysis?

Scenario analysis examines the impact of multiple variables changing simultaneously, representing coherent "states of the world."

### Typical Scenarios

1. **Best Case (Optimistic)**
2. **Base Case (Most Likely)**
3. **Worst Case (Pessimistic)**

Advanced versions may include:
4. **Recession Scenario**
5. **High Growth Scenario**
6. **Competitive Disruption Scenario**

---

## Conducting Scenario Analysis

### Step 1: Define Scenarios

| Variable | Worst | Base | Best |
|----------|-------|------|------|
| Sales Volume | 7,000 | 10,000 | 13,000 |
| Selling Price | ₹450 | ₹500 | ₹550 |
| Variable Cost | ₹330 | ₹300 | ₹280 |
| Fixed Cost | ₹7L | ₹5L | ₹4L |
| Project Life | 4 years | 5 years | 6 years |

### Step 2: Calculate NPV for Each Scenario

**Worst Case:**
Annual Revenue = 7,000 × ₹450 = ₹31.5L
Annual Variable Cost = 7,000 × ₹330 = ₹23.1L
Annual Fixed Cost = ₹7L
Annual CF = ₹31.5L - ₹23.1L - ₹7L = ₹1.4L
NPV = -50 + 1.4 × PVIFA(12%, 4) = -50 + 1.4 × 3.037 = **-₹45.75L**

**Base Case:**
NPV = **₹4.08L** (calculated earlier)

**Best Case:**
Annual Revenue = 13,000 × ₹550 = ₹71.5L
Annual Variable Cost = 13,000 × ₹280 = ₹36.4L
Annual Fixed Cost = ₹4L
Annual CF = ₹71.5L - ₹36.4L - ₹4L = ₹31.1L
NPV = -50 + 31.1 × PVIFA(12%, 6) = -50 + 31.1 × 4.111 = **₹77.85L**

### Step 3: Summarize Results

| Scenario | NPV | Probability | Weighted NPV |
|----------|-----|-------------|--------------|
| Worst Case | -₹45.75L | 20% | -₹9.15L |
| Base Case | ₹4.08L | 60% | ₹2.45L |
| Best Case | ₹77.85L | 20% | ₹15.57L |
| **Expected NPV** | | **100%** | **₹8.87L** |

### Step 4: Analyze Results

**Key Insights:**
1. Wide range: -₹45.75L to +₹77.85L
2. Downside (₹45.75L loss) is significant
3. Expected NPV is positive (₹8.87L)
4. 20% chance of substantial loss

---

## Probability-Weighted Expected NPV

### Formula

$$Expected\ NPV = \sum_{i=1}^{n} (NPV_i \times P_i)$$

### Interpretation

The expected NPV incorporates all scenarios and their likelihoods, providing a single decision metric.

**If Expected NPV > 0:** Accept (on average, project creates value)
**If Expected NPV < 0:** Reject

**Caution:** Expected value doesn't capture risk—a ₹10L expected NPV with huge variance is riskier than ₹10L with low variance.

---

## Indian Business Applications

### Case: Manufacturing Plant Expansion

**Context:** A Gujarat-based textile company evaluating ₹5 crore expansion

**Key Variables Identified:**
1. Cotton prices (volatile commodity)
2. Export demand (depends on global economy)
3. Rupee/Dollar rate (affects export competitiveness)
4. Labor costs (rising in Gujarat)
5. Power costs (state electricity tariffs)

**Sensitivity Analysis Results:**
- Most sensitive: Cotton prices (±15% impact on NPV)
- Second: USD/INR rate
- Third: Export demand

**Scenario Analysis:**

| Scenario | Description | NPV |
|----------|-------------|-----|
| Strong Export | Rupee weak, high demand | ₹3.2 Cr |
| Base | Current conditions | ₹1.1 Cr |
| Domestic Focus | Rupee strong, export weak | ₹0.3 Cr |
| Input Cost Surge | Cotton + power spike | -₹1.5 Cr |

**Decision:** Proceed cautiously with hedging for cotton and currency risks.

### Case: Tech Startup Product Launch

**Context:** Bangalore startup launching SaaS product, ₹80 lakh investment

**Critical Variables:**
1. Customer acquisition cost (CAC)
2. Monthly churn rate
3. Average revenue per user (ARPU)
4. Time to profitability

**Sensitivity:**
Churn rate most critical—1% change in monthly churn dramatically impacts lifetime value.

**Scenarios:**

| Scenario | Monthly Churn | 5-Year NPV |
|----------|---------------|------------|
| Viral Growth | 2% | ₹4.5 Cr |
| Steady Growth | 5% | ₹60L |
| Competitive Pressure | 8% | -₹40L |

---

## Common Mistakes to Avoid

### Mistake 1: Unrealistic Ranges

**Wrong:** Testing ±50% on all variables
**Right:** Use historically observed or reasonably expected ranges

### Mistake 2: Ignoring Correlations

**Wrong:** Assuming variables are independent
**Right:** In scenario analysis, change correlated variables together

**Example:** If GDP falls, both sales volume AND selling price likely fall.

### Mistake 3: Forgetting Second-Order Effects

**Variable change may trigger other changes:**
- Lower sales → need to cut prices → further lower margins
- Higher costs → need to raise prices → lose customers

### Mistake 4: Over-Relying on Base Case

**The base case is just one possibility**, not a prediction.
- Present range of outcomes, not just base case
- Discuss risk management for adverse scenarios

---

## Integration with Other Techniques

### Sensitivity + NPV

Use NPV as the metric being analyzed.

### Sensitivity + IRR

Examine how IRR changes with key variables.

### Scenario Analysis + Decision Trees

Combine with decision trees for projects with sequential decisions.

### Monte Carlo Simulation

Advanced technique:
- Run thousands of scenarios
- Each variable varies within probability distribution
- Get full distribution of possible NPVs

---

## Key Takeaways

1. **Single-point estimates hide uncertainty** – Always test your assumptions
2. **Sensitivity analysis identifies critical variables** – Focus management attention
3. **Breakeven analysis shows margin of safety** – How much can go wrong before NPV turns negative
4. **Scenario analysis captures correlated changes** – More realistic than isolated variable changes
5. **Expected NPV incorporates probabilities** – But doesn't capture risk fully
6. **Indian context matters** – Commodity prices, currency, policy changes are critical variables
7. **Use both techniques together** – Sensitivity for focus, scenarios for holistic view

---

## Disclaimer

*This article is for educational purposes only. Actual investment analysis should be performed by qualified professionals. This is not investment advice.*

---

## Frequently Asked Questions

**Q: How many variables should I include in sensitivity analysis?**
A: Focus on 5-8 most uncertain and impactful variables. Too many becomes unwieldy.

**Q: What percentage range should I use?**
A: Use realistic ranges based on historical volatility or expert judgment. ±10-20% is common for most variables.

**Q: How do I assign probabilities to scenarios?**
A: Use historical data, expert judgment, or market research. Be transparent about subjectivity.

**Q: Which is better—sensitivity or scenario analysis?**
A: Both serve different purposes. Sensitivity identifies critical variables; scenario analysis shows realistic combinations. Use both.

**Q: Can Excel do this analysis?**
A: Yes. Use Data Tables for sensitivity, separate calculations for scenarios. Excel's Scenario Manager helps organize.

**Q: Should I always present worst-case to management?**
A: Yes, transparently. Show the range of outcomes and risk mitigation strategies.

*Sensitivity and scenario analysis are like stress-testing a bridge before opening it to traffic. The bridge might look solid in normal conditions, but you need to know if it can withstand an earthquake, heavy loads, or extreme weather. Similarly, your project might look profitable under base assumptions, but you need to know how it performs when things don't go as planned.*
