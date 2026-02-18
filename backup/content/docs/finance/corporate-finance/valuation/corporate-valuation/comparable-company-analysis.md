---
title: "Comparable Company Analysis: Trading Multiples Guide"
date: 2025-01-15
weight: 23
description: "Master comparable company analysis (trading comps). Learn to select peer companies, calculate valuation multiples, and apply them to derive company valuations."
tags: ["comparable company analysis", "trading multiples", "valuation multiples", "relative valuation", "peer analysis"]
series: ["Corporate Finance"]
series_order: 23
seo_title: "Comparable Company Analysis | Trading Multiples Valuation"
seo_description: "Complete guide to comparable company analysis - peer selection, valuation multiples calculation, EV/EBITDA, P/E ratios, and applying trading comps for company valuation."
keywords: ["comparable company analysis", "trading multiples", "EV/EBITDA", "P/E ratio", "relative valuation"]
ShowToc: true
TocOpen: true
---

## Introduction: What the Market Is Willing to Pay

"What's the company worth? Let's see what similar companies are trading at."

Comparable Company Analysis, often called "trading comps" or "relative valuation," values a company by comparing it to similar publicly traded companies. It's one of the most widely used valuation methods in investment banking and equity research.

---

## The Logic of Comparable Analysis

### Fundamental Premise

Similar companies should trade at similar valuations relative to their financial metrics.

### How It Works

1. **Identify** similar publicly traded companies
2. **Calculate** their valuation multiples
3. **Apply** those multiples to your target company

### Formula

$$Target\ Value = Target\ Metric \times Comparable\ Multiple$$

### Example

If similar companies trade at 10x EBITDA, and your company has EBITDA of ₹50 crore:

$$Value = ₹50\ crore \times 10 = ₹500\ crore$$

---

## Step 1: Select Comparable Companies

### Selection Criteria

**Primary Criteria:**
1. **Industry:** Same sector and sub-sector
2. **Geography:** Similar market exposure
3. **Size:** Comparable revenue/market cap
4. **Growth:** Similar growth profiles
5. **Profitability:** Comparable margins

**Secondary Criteria:**
- Business model similarity
- Customer base overlap
- Competitive positioning
- Stage of business lifecycle

### Finding Comparables in India

**Sources:**
- BSE/NSE sector classifications
- Bloomberg/Reuters industry codes
- Industry reports
- Brokerage research

**Example: IT Services Company**

| Company | Revenue (₹ Cr) | EBITDA Margin | Growth |
|---------|---------------|---------------|--------|
| TCS | 2,40,000 | 28% | 9% |
| Infosys | 1,65,000 | 25% | 13% |
| Wipro | 90,000 | 17% | 8% |
| HCL Tech | 1,05,000 | 24% | 12% |
| Tech Mahindra | 55,000 | 15% | 10% |

### Creating the Peer Universe

**Narrowing Down:**

| Filter | Companies Remaining |
|--------|-------------------|
| IT Services sector | 30+ |
| Revenue > ₹5,000 Cr | 15 |
| Similar client mix | 10 |
| Comparable margins | 7 |
| **Final Peer Set** | **5-7** |

### How Many Comparables?

- **Minimum:** 3-4 companies
- **Optimal:** 5-8 companies
- **Maximum:** 10-12 companies

**Quality over quantity**—better to have 5 true comparables than 12 loose ones.

---

## Step 2: Calculate Valuation Multiples

### Enterprise Value Multiples

**Enterprise Value (EV):**
$$EV = Market\ Cap + Debt - Cash + Minority\ Interest + Preferred$$

**Common EV Multiples:**

| Multiple | Formula | Best For |
|----------|---------|----------|
| EV/Revenue | EV ÷ Revenue | High growth, negative EBITDA |
| EV/EBITDA | EV ÷ EBITDA | Most industries |
| EV/EBIT | EV ÷ EBIT | Asset-heavy industries |
| EV/FCFF | EV ÷ Free Cash Flow | Mature, stable companies |

### Equity Multiples

**Common Equity Multiples:**

| Multiple | Formula | Best For |
|----------|---------|----------|
| P/E | Price ÷ EPS | Stable earnings |
| P/B | Price ÷ Book Value | Banks, asset-intensive |
| P/S | Price ÷ Sales per share | High growth, no profits |
| PEG | P/E ÷ Growth rate | Growth comparison |

### Industry-Specific Multiples

**Banking:**
- P/B (Price to Book)
- P/E (Price to Earnings)
- P/PPOP (Price to Pre-Provision Operating Profit)

**Real Estate:**
- P/NAV (Price to Net Asset Value)
- EV/Asset Value

**Telecom:**
- EV/Subscriber
- EV/EBITDA

**E-commerce:**
- EV/GMV (Gross Merchandise Value)
- EV/Revenue

### Calculating Multiples: Example

**Company: ABC Ltd**
- Market Cap: ₹500 crore
- Debt: ₹100 crore
- Cash: ₹20 crore
- Revenue: ₹200 crore
- EBITDA: ₹40 crore
- Net Income: ₹25 crore
- Shares: 10 crore

**Calculations:**
- EV = 500 + 100 - 20 = ₹580 crore
- EPS = 25/10 = ₹2.5
- EV/Revenue = 580/200 = 2.9x
- EV/EBITDA = 580/40 = 14.5x
- P/E = 50/2.5 = 20x (Price = 500/10 = ₹50)

---

## Step 3: Build the Comps Table

### Standard Comps Output

**Trading Comparables (₹ crore except per share):**

| Company | Market Cap | EV | Revenue | EBITDA | EV/Rev | EV/EBITDA | P/E |
|---------|-----------|-----|---------|--------|--------|-----------|-----|
| Comp 1 | 8,000 | 9,200 | 3,500 | 850 | 2.6x | 10.8x | 18x |
| Comp 2 | 5,500 | 6,100 | 2,800 | 600 | 2.2x | 10.2x | 16x |
| Comp 3 | 4,200 | 4,800 | 2,000 | 480 | 2.4x | 10.0x | 15x |
| Comp 4 | 7,000 | 8,000 | 3,200 | 720 | 2.5x | 11.1x | 19x |
| Comp 5 | 3,800 | 4,400 | 1,600 | 350 | 2.8x | 12.6x | 22x |
| **Mean** | | | | | **2.5x** | **10.9x** | **18x** |
| **Median** | | | | | **2.5x** | **10.8x** | **18x** |

### Statistics to Calculate

1. **Mean (Average):** Sum ÷ Count
2. **Median:** Middle value (less sensitive to outliers)
3. **High:** Maximum in range
4. **Low:** Minimum in range
5. **Standard Deviation:** Measure of spread

### Which Statistic to Use?

**Mean:** When comparables are similar
**Median:** When outliers exist
**Range:** For valuation range presentation

---

## Step 4: Apply Multiples to Target

### The Application Process

**Target Company Metrics:**
- Revenue: ₹150 crore
- EBITDA: ₹35 crore
- Net Income: ₹20 crore

### Valuation Matrix

| Method | Target Metric | Multiple | Implied Value |
|--------|--------------|----------|---------------|
| EV/Revenue | ₹150 Cr | 2.5x | ₹375 Cr |
| EV/EBITDA | ₹35 Cr | 10.9x | ₹382 Cr |
| P/E → Equity | ₹20 Cr | 18x | ₹360 Cr |

### From Enterprise to Equity Value

**For EV multiples:**
$$Equity\ Value = EV - Net\ Debt$$

**Example:**
- Implied EV: ₹380 crore
- Target Debt: ₹30 crore
- Target Cash: ₹5 crore
- Net Debt: ₹25 crore

$$Equity\ Value = 380 - 25 = ₹355\ crore$$

### Valuation Range

**Based on Multiple Range:**

| Multiple | Low | Median | High |
|----------|-----|--------|------|
| EV/EBITDA | 10.0x | 10.9x | 12.6x |
| Implied EV | ₹350 Cr | ₹382 Cr | ₹441 Cr |
| Equity Value | ₹325 Cr | ₹357 Cr | ₹416 Cr |

---

## Adjustments and Considerations

### Calendarization

Align fiscal years when companies have different year-ends.

**Example:**
- Company A: March year-end
- Company B: December year-end

**Solution:** Use LTM (Last Twelve Months) or NTM (Next Twelve Months) metrics.

### Non-Recurring Items

**Normalize for:**
- One-time gains/losses
- Restructuring charges
- Unusual legal settlements
- Discontinued operations

**Example:**
- Reported EBITDA: ₹40 crore
- Less: One-time insurance recovery: ₹5 crore
- Normalized EBITDA: ₹35 crore

### Size Premium/Discount

Smaller companies typically trade at lower multiples due to:
- Lower liquidity
- Higher risk
- Less market coverage

**Adjustment:** Apply size discount (10-20%) for significantly smaller targets.

### Growth Differential

Higher growth = Higher multiple

**Using PEG Ratio:**
$$PEG = \frac{P/E}{Growth\ Rate}$$

**If target grows faster than comps:**
Adjust multiple upward

### Control Premium

Trading comps reflect minority stake prices. For control acquisition:

**Add Control Premium:** Typically 20-40%

---

## Common Multiples Deep Dive

### EV/EBITDA

**Most Widely Used Because:**
- Capital structure neutral
- Less affected by accounting differences
- Focuses on operating performance

**Limitations:**
- Ignores capex requirements
- EBITDA can be manipulated
- Doesn't work for negative EBITDA

**Typical Ranges by Sector:**

| Sector | EV/EBITDA Range |
|--------|-----------------|
| IT Services | 10-20x |
| FMCG | 20-35x |
| Pharma | 12-25x |
| Industrials | 6-12x |
| Utilities | 5-8x |

### P/E Ratio

**Advantages:**
- Simple, widely quoted
- Directly relates to stock price
- Good for stable earnings

**Limitations:**
- Affected by capital structure
- EPS can be manipulated
- Doesn't work for losses

**When to Use:**
- Profitable companies
- Similar leverage levels
- Stable earnings

### P/B Ratio

**Best for Banks Because:**
- Balance sheet is the business
- Book value reflects tangible equity
- Traditional bank metric

**Interpretation:**
- P/B < 1: Trading below book value
- P/B = 1: Trading at book value
- P/B > 1: Premium to book value

### EV/Revenue

**When to Use:**
- Companies without profits
- High-growth companies
- Cross-border comparisons

**Limitations:**
- Ignores profitability
- Misleading for different margin profiles

---

## Quality of Comparables Analysis

### Red Flags in Comps

1. **Wide multiple range:** Comps may not be truly comparable
2. **Outliers:** Need investigation and potential exclusion
3. **Negative multiples:** Company issues, exclude
4. **Recent M&A:** May distort trading prices

### Improving Comparability

**Segment Analysis:**
When conglomerates are involved, use sum-of-parts with segment-specific comparables.

**Adjustment Example:**

| Segment | Revenue | Segment Multiple | Segment Value |
|---------|---------|-----------------|---------------|
| IT Services | ₹100 Cr | 2.5x EV/Rev | ₹250 Cr |
| Products | ₹50 Cr | 1.5x EV/Rev | ₹75 Cr |
| **Total** | **₹150 Cr** | | **₹325 Cr** |

---

## Comps vs Other Methods

### Comps vs DCF

| Aspect | Comps | DCF |
|--------|-------|-----|
| Basis | Market prices | Fundamental value |
| Data required | Less | More |
| Subjectivity | In comparable selection | In assumptions |
| Time required | Hours | Days |
| When markets are efficient | Works well | May differ |

### When to Use Comps

**Best For:**
- Quick valuations
- Market-based pricing
- When good comparables exist
- IPO pricing

**Not Ideal For:**
- Unique companies (no good comps)
- Distressed situations
- When markets are dislocated

---

## Best Practices

### Peer Selection

1. **Start broad, narrow down:** Industry → Similar size → Similar profile
2. **Document selection criteria:** Why included, why excluded
3. **Update regularly:** Markets and companies change

### Multiple Selection

1. **Use multiple multiples:** Don't rely on just one
2. **Understand drivers:** Why are multiples different?
3. **Check for reasonableness:** Do multiples make sense?

### Presentation

1. **Show your work:** Full comps table
2. **Provide ranges:** Not single point estimates
3. **Disclose adjustments:** Calendarization, normalization
4. **Compare to DCF:** Triangulation

---

## Key Takeaways

1. **Comps = Relative valuation** – Based on market prices
2. **Peer selection is critical** – Quality of comps drives quality of valuation
3. **Multiple choice matters** – Different multiples for different situations
4. **Mean or Median** – Depends on outliers and distribution
5. **Adjust for differences** – Size, growth, profitability
6. **Show a range** – Not a single number
7. **Triangulate** – Use with DCF and precedents

---

## Disclaimer

*This article is for educational purposes only. Valuation requires professional judgment and understanding of company specifics. Consult qualified professionals for actual valuations. This is not investment advice.*

---

## Frequently Asked Questions

**Q: What if there are no good comparables?**
A: Consider adjacent industries, international comparables, or rely more heavily on DCF and other methods. Document limitations clearly.

**Q: Should I use forward or trailing multiples?**
A: Forward (NTM) multiples are more relevant for growth companies and price discovery. Trailing (LTM) for accuracy and verification. Present both when possible.

**Q: How do I handle a comp with much higher/lower multiples?**
A: Investigate the reason (growth, margins, one-time factors). Consider excluding if truly not comparable. Document the decision.

**Q: EV/EBITDA or P/E?**
A: EV/EBITDA for comparing companies with different capital structures. P/E when leverage is similar and for equity-focused analysis.

**Q: How often should comps be updated?**
A: For live transactions—daily or weekly. For research—monthly or quarterly. Market prices change, so multiples should be current.

*Trading comps are like finding the price of a house by looking at what similar houses sold for in your neighborhood. The key is defining "similar" correctly—same neighborhood, similar size, comparable condition. The better your comparables, the more reliable your valuation.*
