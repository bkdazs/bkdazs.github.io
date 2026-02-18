---
title: "NPV vs IRR: Choosing the Right Project Evaluation Method"
date: 2025-01-15
weight: 2
description: "Detailed comparison of NPV and IRR methods in capital budgeting. Learn when to use each method, how they differ, conflicts between methods, and best practices for project evaluation."
tags: ["NPV", "IRR", "capital budgeting", "project evaluation", "investment analysis", "corporate finance"]
series: ["Corporate Finance"]
series_order: 2
seo_title: "NPV vs IRR Comparison | Project Evaluation Methods Guide"
seo_description: "Complete comparison of NPV vs IRR in capital budgeting - learn differences, when methods conflict, which to prefer, and practical applications for Indian businesses."
keywords: ["NPV vs IRR", "net present value", "internal rate of return", "project evaluation", "capital budgeting methods"]
ShowToc: true
TocOpen: true
---

## Introduction: The Two Pillars of Project Evaluation

A finance manager presents two projects to the board. "Project A has an IRR of 25%, and Project B has an IRR of 18%," she says. "But Project B has a higher NPV."

The board is confused. Which project is better?

This scenario plays out in boardrooms across India. NPV and IRR are the two most important capital budgeting tools, yet they can give conflicting signals. Understanding when and why they differ—and which to trust—is essential for sound investment decisions.

---

## Net Present Value (NPV) Explained

### Definition

NPV is the sum of present values of all cash flows associated with a project, including the initial investment.

### Formula

$$NPV = \sum_{t=0}^{n} \frac{CF_t}{(1+r)^t} = -Initial\ Investment + \sum_{t=1}^{n} \frac{CF_t}{(1+r)^t}$$

### Calculation Example

**Project Data:**
- Initial investment: ₹100 lakh
- Cash flows: ₹35 lakh per year for 4 years
- Required return: 12%

**NPV Calculation:**

$$NPV = -100 + \frac{35}{1.12} + \frac{35}{1.12^2} + \frac{35}{1.12^3} + \frac{35}{1.12^4}$$

$$NPV = -100 + 31.25 + 27.90 + 24.91 + 22.24$$

$$NPV = ₹6.30\ lakh$$

### Decision Rule

| NPV Value | Decision | Interpretation |
|-----------|----------|----------------|
| NPV > 0 | Accept | Project adds value |
| NPV = 0 | Indifferent | Project earns exactly required return |
| NPV < 0 | Reject | Project destroys value |

### What NPV Represents

- **Absolute value creation** in rupee terms
- **Wealth addition** to shareholders
- **Present value** of economic profit

---

## Internal Rate of Return (IRR) Explained

### Definition

IRR is the discount rate that makes the NPV of a project equal to zero. It represents the project's actual rate of return.

### Formula

$$0 = \sum_{t=0}^{n} \frac{CF_t}{(1+IRR)^t}$$

IRR must be solved through trial and error (or Excel/calculator).

### Calculation Example

**Same Project:**
- Initial investment: ₹100 lakh
- Cash flows: ₹35 lakh per year for 4 years

**Finding IRR:**

At 15%: NPV = ₹-0.07 lakh
At 14.9%: NPV ≈ ₹0

**IRR ≈ 14.9%**

### Decision Rule

| Comparison | Decision | Interpretation |
|------------|----------|----------------|
| IRR > Required Return | Accept | Project earns more than cost of capital |
| IRR = Required Return | Indifferent | Project earns exactly required return |
| IRR < Required Return | Reject | Project earns less than cost of capital |

### What IRR Represents

- **Percentage return** on investment
- **Break-even discount rate**
- **Internal earning power** of project

---

## NPV vs IRR: Head-to-Head Comparison

### Summary Table

| Aspect | NPV | IRR |
|--------|-----|-----|
| Result | Rupee amount | Percentage |
| Reinvestment Assumption | At required return | At IRR |
| Multiple Solutions | Never | Possible |
| For Mutually Exclusive | Reliable | May mislead |
| Capital Rationing | Less suitable | More suitable |
| Ease of Understanding | Moderate | Easy |
| Theoretical Correctness | Superior | Has limitations |
| Practical Usage | High | Very High |

### Key Differences

**1. Absolute vs Relative**
- NPV gives absolute value (₹ lakh)
- IRR gives relative return (%)

**2. Reinvestment Assumption**
- NPV assumes reinvestment at required return (realistic)
- IRR assumes reinvestment at IRR (often unrealistic)

**3. Scale Consideration**
- NPV accounts for project size
- IRR ignores scale (a 50% return on ₹1 lakh vs ₹100 lakh)

---

## When NPV and IRR Agree

### Independent Projects with Conventional Cash Flows

**Conventional Cash Flow:** One sign change (negative initially, then positive)

```
Year:    0       1       2       3       4
CF:    -100    +30     +35     +40     +45
```

**For such projects:**
- If NPV > 0, then IRR > Required Return
- If NPV < 0, then IRR < Required Return
- Decision is the same!

### Example

**Project Alpha:**
- Investment: ₹80 lakh
- Annual CF: ₹25 lakh for 5 years
- Required return: 10%

**Analysis:**
- NPV = ₹14.77 lakh ✓
- IRR = 16.9% ✓

**Decision:** Both methods say Accept!

---

## When NPV and IRR Conflict

### Scenario 1: Mutually Exclusive Projects with Different Scales

**Project A:**
- Investment: ₹50 lakh
- NPV: ₹15 lakh
- IRR: 25%

**Project B:**
- Investment: ₹200 lakh
- NPV: ₹40 lakh
- IRR: 18%

| Method | Recommends | Logic |
|--------|-----------|-------|
| IRR | Project A | Higher percentage return |
| NPV | Project B | Higher absolute value |

**Which is correct?**

If you can only do one: **Choose Project B** (higher NPV = more wealth)

The extra ₹150 lakh invested earns more than the required return.

### Scenario 2: Different Cash Flow Timing

**Project X:** Early cash flows
```
Year:    0      1      2      3
CF:    -100   +60    +50    +30
```

**Project Y:** Late cash flows
```
Year:    0      1      2      3
CF:    -100   +20    +40    +100
```

At 10% required return:
- Project X: NPV = ₹22.4 lakh, IRR = 25.8%
- Project Y: NPV = ₹24.2 lakh, IRR = 22.1%

**NPV Profile (Crossover):**

At low discount rates: Y has higher NPV
At high discount rates: X has higher NPV
Crossover rate ≈ 14.5%

**Decision depends on required return:**
- If required return < 14.5%: Choose Y (NPV is correct)
- If required return > 14.5%: Both methods agree on X

### Scenario 3: Different Project Lives

**Short Project (3 years):**
- Investment: ₹100 lakh
- NPV: ₹25 lakh
- IRR: 22%

**Long Project (6 years):**
- Investment: ₹100 lakh
- NPV: ₹40 lakh
- IRR: 18%

**IRR misleads:** Higher IRR for short project doesn't account for:
- What happens after 3 years?
- Reinvestment opportunities

**NPV is correct:** Use equivalent annual annuity or assume project repetition.

---

## The Multiple IRR Problem

### Non-Conventional Cash Flows

When cash flows change sign more than once, multiple IRRs can exist.

**Example: Oil Well Project**
```
Year 0: -₹50 lakh (drilling)
Year 1: +₹200 lakh (oil revenue)
Year 2: -₹150 lakh (cleanup/abandonment)
```

**Two sign changes → Potentially two IRRs**

Solving: IRR = 20% and IRR = 50%

**Problem:** Which IRR to use?

**Solution:** 
1. Use NPV instead
2. Use Modified IRR (MIRR)
3. Apply decision rule to NPV profile

### NPV Profile for Multiple IRRs

```
NPV
 ↑
 |      /\
 |     /  \
 |----/----\---- Required Return
 |   /      \
 |  /        \
 | /          \
 +---------------→ Discount Rate
    IRR₁     IRR₂
```

**Decision:** Accept if required return is between the two IRRs (where NPV > 0)

---

## Modified Internal Rate of Return (MIRR)

### Why MIRR?

MIRR addresses IRR's reinvestment assumption problem.

### How MIRR Works

1. **All negative cash flows** → Discounted to present at financing rate
2. **All positive cash flows** → Compounded to terminal year at reinvestment rate
3. **MIRR** → Rate connecting PV of costs to FV of benefits

### Formula

$$MIRR = \left(\frac{Terminal\ Value\ of\ Inflows}{PV\ of\ Outflows}\right)^{1/n} - 1$$

### Example

**Project:**
```
Year 0: -₹100 lakh
Year 1: +₹40 lakh
Year 2: +₹50 lakh
Year 3: +₹60 lakh
```

**Assumptions:**
- Required return: 12%
- Reinvestment rate: 12%

**Terminal Value of Inflows:**
$$TV = 40(1.12)^2 + 50(1.12)^1 + 60 = 50.18 + 56 + 60 = ₹166.18\ lakh$$

**MIRR:**
$$MIRR = \left(\frac{166.18}{100}\right)^{1/3} - 1 = 18.5\%$$

**Interpretation:** More realistic than IRR because it uses achievable reinvestment rate.

---

## Practical Guidance: Which Method to Use?

### Decision Framework

| Situation | Recommended Method |
|-----------|-------------------|
| Single independent project | Either (both work) |
| Mutually exclusive projects | NPV (absolute value) |
| Capital rationing | Profitability Index + NPV |
| Non-conventional cash flows | NPV or MIRR |
| Quick communication | IRR (easier to explain) |
| Precise value calculation | NPV |

### Best Practice: Use Both

**Step 1:** Calculate NPV
- Is project worth doing? (NPV > 0?)
- How much value added?

**Step 2:** Calculate IRR
- What's the return?
- How much cushion above required return?

**Step 3:** If they conflict
- Trust NPV for value maximization
- Investigate why they differ

---

## NPV and IRR in Indian Practice

### Corporate Usage

| Method | Usage Rate (Large Indian Corporates) |
|--------|-------------------------------------|
| NPV | 65-70% |
| IRR | 80-85% |
| Payback | 85-90% |
| Profitability Index | 40-50% |

**Observation:** Indian companies use multiple methods (not just one)

### Why IRR is Popular

Despite theoretical limitations:
- Easy to communicate to board
- Intuitive for non-finance managers
- Benchmarking across projects
- Quick mental calculation

### Adapting to Indian Context

**Higher Discount Rates:**
- Indian risk-free rate: 6-7%
- Equity risk premium: 5-7%
- WACC often 12-16%

**Impact:** 
- More projects rejected
- NPV more sensitive to rate
- IRR margin of safety matters

---

## Common Mistakes to Avoid

### Mistake 1: Using IRR for Mutually Exclusive Projects

**Wrong:** "Project A has 22% IRR vs 18% for B, so choose A"
**Right:** Compare NPVs; larger NPV = more value

### Mistake 2: Ignoring Scale

**Wrong:** "₹1 crore at 30% IRR beats ₹10 crore at 20% IRR"
**Right:** ₹10 crore at 20% creates more value

### Mistake 3: Not Checking for Multiple IRRs

**Wrong:** Using IRR without checking cash flow pattern
**Right:** Verify conventional cash flows; use MIRR if needed

### Mistake 4: Misunderstanding Reinvestment Assumption

**Wrong:** "IRR of 25% means I'll earn 25% on everything"
**Right:** IRR assumes reinvestment at 25% (often unrealistic)

---

## Key Takeaways

1. **NPV = Absolute value creation** – Best for maximizing shareholder wealth
2. **IRR = Percentage return** – Great for communication
3. **They usually agree** – For independent, conventional projects
4. **NPV wins conflicts** – Especially for mutually exclusive projects
5. **Watch for multiple IRRs** – With non-conventional cash flows
6. **MIRR is more realistic** – Better reinvestment assumption
7. **Use both methods** – NPV for decision, IRR for insight

---

## Disclaimer

*This article is for educational purposes only. Investment decisions should be based on comprehensive analysis including factors beyond NPV and IRR. Consult financial professionals for specific investment decisions. This is not investment advice.*

---

## Frequently Asked Questions

**Q: If NPV is better, why do companies still use IRR?**
A: IRR is intuitive, easy to communicate, and allows quick comparison. Most companies use both.

**Q: Can IRR be negative?**
A: Yes, if project loses money. Negative IRR means NPV is negative at any positive discount rate.

**Q: What if IRR equals required return?**
A: NPV = 0. Project earns exactly what shareholders require—neither creates nor destroys value.

**Q: How do I handle inflation?**
A: Be consistent—use nominal cash flows with nominal rate, or real cash flows with real rate.

**Q: Which method do IIMs teach as primary?**
A: NPV is taught as theoretically superior, but both are extensively covered.

**Q: What if my Excel shows #NUM! for IRR?**
A: Likely non-conventional cash flows with multiple sign changes. Use NPV or provide guess value.

*NPV and IRR are not competitors—they're complementary tools. Like a doctor using both blood pressure and pulse, a good financial analyst uses both NPV and IRR to get a complete picture. Master both, understand their differences, and know when each is most appropriate.*
