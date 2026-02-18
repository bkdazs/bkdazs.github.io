---
title: "Foreign Exchange Risk Management: A Treasury Guide"
date: 2025-01-15
weight: 29
description: "Master forex risk management for Indian businesses. Learn FX exposure identification, hedging strategies, forward contracts, options, and RBI guidelines for currency risk."
tags: ["forex risk management", "currency hedging", "FX exposure", "forward contracts", "currency options"]
series: ["Corporate Finance"]
series_order: 29
seo_title: "Foreign Exchange Risk Management | Currency Hedging Guide India"
seo_description: "Complete guide to forex risk management - identifying FX exposures, hedging strategies, forward contracts, options, and RBI regulations for Indian corporates."
keywords: ["forex risk management", "currency hedging", "FX exposure", "forward contracts", "currency risk"]
ShowToc: true
TocOpen: true
---

## Introduction: The Currency Wild Card

"We made profits on our operations but lost ₹10 crore on currency movements."

For companies with international operations, currency fluctuations can make or break results. A 5% move in USD/INR can wipe out operating margins on export sales. Foreign exchange risk management is no longer optional—it's essential.

---

## Understanding FX Exposure

### Types of FX Exposure

**1. Transaction Exposure:**
Impact of currency movement on existing contractual obligations.

*Example:* Export receivable of $1 million due in 90 days. If INR strengthens from 83 to 80, you receive ₹3 crore less.

**2. Translation Exposure:**
Impact on consolidated financial statements when converting foreign subsidiary results.

*Example:* US subsidiary profits of $1 million. If INR weakens, translated profits increase (but no cash impact).

**3. Economic Exposure:**
Long-term impact of currency changes on competitive position.

*Example:* Rupee depreciation makes Indian exports more competitive but imports more expensive.

### Quantifying Exposure

**Transaction Exposure Register:**

| Exposure Type | Currency | Amount | Due Date | INR Equivalent |
|---------------|----------|--------|----------|----------------|
| Export receivable | USD | 2.0 M | 90 days | ₹166 Cr |
| Import payable | EUR | 0.5 M | 60 days | ₹46 Cr |
| FCY loan | USD | 5.0 M | Various | ₹415 Cr |
| **Net USD** | | **2.0 M** | | Long |
| **Net EUR** | | **(0.5 M)** | | Short |

---

## Hedging Instruments

### Forward Contracts

**What It Is:**
Agreement to buy/sell currency at predetermined rate on future date.

**Key Features:**
- Fixed exchange rate
- Obligatory contract
- Customizable amount and date
- Available through banks

**Example:**
- Export receivable: $1 million
- Spot rate: ₹83.00
- 90-day forward rate: ₹83.50
- Book forward to sell $1M at ₹83.50 in 90 days

**Outcome:**
- If spot at maturity is ₹82: You gain ₹1.50 per dollar
- If spot at maturity is ₹85: You lose ₹1.50 per dollar

**Forward Premium/Discount:**
$$Forward\ Points = \frac{(INR\ Rate - USD\ Rate) \times Days}{365 \times 100}$$

Typically, USD/INR trades at premium (forward > spot) due to interest rate differential.

### Currency Options

**What It Is:**
Right (not obligation) to buy/sell currency at specified rate.

**Types:**

| Type | Right | Use For |
|------|-------|---------|
| Call Option | Buy currency | Import hedging |
| Put Option | Sell currency | Export hedging |

**Key Terms:**
- **Strike Price:** Rate at which you can exercise
- **Premium:** Cost of the option
- **Expiry:** When option expires
- **American:** Can exercise anytime
- **European:** Exercise only at expiry

**Example (Put Option for Exporter):**
- Export receivable: $1 million
- Spot: ₹83.00
- 90-day put option at ₹82.50 strike
- Premium: ₹1.00 per dollar (₹1 crore total)

**Outcomes:**
- If INR strengthens to ₹80: Exercise put, get ₹82.50 (net ₹81.50 after premium)
- If INR weakens to ₹86: Let option expire, sell at ₹86 (net ₹85 after premium)

**Best For:**
- Protection with upside participation
- Uncertain cash flows
- When view exists on direction

### Currency Swaps

**What It Is:**
Exchange of principal and interest payments in different currencies.

**Types:**

**1. Cross-Currency Swap:**
Exchange principal and interest between currencies.

**2. Currency Coupon Swap:**
Exchange fixed rate in one currency for floating in another.

**Example:**
- Company has USD loan: $10 million at SOFR + 2%
- Wants INR exposure
- Enter swap: Pay INR fixed, receive USD SOFR + 2%

**Best For:**
- Long-term exposures
- Loan restructuring
- Asset-liability matching

### Range Forwards (Zero-Cost Options)

**What It Is:**
Combination of buying and selling options to create zero-cost hedge.

**Structure:**
- Buy put at lower strike (protection)
- Sell call at higher strike (finance the put)
- Net premium: Zero

**Example:**
- Spot: ₹83.00
- Buy put at ₹82.00
- Sell call at ₹84.50
- Net premium: Zero

**Outcome:**
- If rate < ₹82: Exercise put, get ₹82
- If rate between ₹82-₹84.50: Sell at market
- If rate > ₹84.50: Counter-party exercises call, you sell at ₹84.50

---

## Hedging Strategies

### Full Hedging

**Approach:** Hedge 100% of exposures

**Pros:**
- Complete certainty
- Budget protection
- Simple policy

**Cons:**
- May miss favorable moves
- Continuous management
- Cost of hedging

### Partial Hedging

**Approach:** Hedge portion of exposures (e.g., 50-75%)

**Pros:**
- Some protection
- Some participation
- Balanced approach

**Cons:**
- Residual risk
- More complex tracking

### Layered Hedging

**Approach:** Hedge in layers over time

**Example:**
- Month 1: Hedge 30%
- Month 2: Hedge additional 30%
- Month 3: Hedge additional 20%
- Leave 20% unhedged

**Pros:**
- Average rate achieved
- Reduces timing risk
- Systematic approach

### View-Based Hedging

**Approach:** Hedge based on market view

**Pros:**
- Can be profitable
- Exploits expertise

**Cons:**
- Requires forecasting ability
- May result in losses
- Speculative element

**Generally not recommended** for corporates without sophisticated treasury capabilities.

---

## Policy Framework

### FX Risk Management Policy

**Key Elements:**

**1. Objective:**
- Reduce earnings volatility
- Protect budget rates
- Not speculative gains

**2. Scope:**
- Covered exposures
- Currencies included
- Minimum threshold

**3. Hedging Guidelines:**
- Minimum/maximum hedge ratios
- Approved instruments
- Tenor limits

**4. Governance:**
- Approval matrix
- Reporting requirements
- Exception process

### Sample Policy Framework

| Exposure Type | Hedge Ratio | Instruments | Tenor |
|---------------|-------------|-------------|-------|
| Firm commitments | 75-100% | Forwards, Options | Match exposure |
| Forecast transactions | 50-75% | Options, Range forwards | Up to 12 months |
| Translation | 0-50% | Forwards | Balance sheet date |

### Approval Matrix

| Hedge Amount | Instrument | Approver |
|--------------|------------|----------|
| < ₹10 Cr | All approved | Treasury Manager |
| ₹10-50 Cr | Forwards, Options | CFO |
| > ₹50 Cr | Any | Board/Committee |
| Structured products | Any | Board approval |

---

## RBI Guidelines

### Regulatory Framework

**Who Can Hedge:**
- Residents with FX exposure
- Must have underlying exposure
- No naked speculation

**Hedging Requirements:**
- Underlying exposure must exist
- Documentation required
- Mark-to-market reporting

### Permitted Products (For Corporates)

| Product | Availability |
|---------|-------------|
| Forward contracts | Yes |
| Plain vanilla options | Yes |
| Currency swaps | Yes |
| Cost reduction structures | Limited |
| Exotic options | Restricted |

### Documentation Requirements

- Board resolution authorizing FX derivatives
- Risk management policy
- Underlying exposure evidence
- KYC/AML compliance
- Derivative transaction documentation

### Past Performance Facility

Companies with good track record can book forwards:
- Up to 100% of average past 3 years' performance
- Without underlying documentation each time
- Subject to overall limits

---

## Managing FX in Practice

### Daily Activities

**1. Position Monitoring:**
- Track all FX exposures
- Update for new transactions
- Calculate net exposure by currency

**2. Market Monitoring:**
- Track spot rates
- Review forward rates
- Note market developments

**3. Hedge Management:**
- Monitor existing hedges
- Execute new hedges as needed
- Roll over maturing contracts

### Hedge Accounting

**Why It Matters:**
Without hedge accounting, derivative MTM flows through P&L, creating volatility even when hedging effectively.

**IND AS 109 Requirements:**
- Hedge documentation
- Effectiveness testing
- Prospective and retrospective tests

**Types of Hedge Accounting:**

| Type | What's Hedged | P&L Treatment |
|------|---------------|---------------|
| Cash flow hedge | Forecast transactions | MTM in OCI |
| Fair value hedge | Recognized assets/liabilities | MTM in P&L |
| Net investment hedge | Foreign subsidiary investment | MTM in OCI |

### Effectiveness Testing

**Methods:**
- Dollar offset method
- Regression analysis
- Critical terms match

**Threshold:**
80-125% effectiveness ratio typically required.

---

## Common Mistakes

### Mistake 1: No Hedging Policy

**Problem:** Ad-hoc decisions based on market views
**Fix:** Document and follow consistent policy

### Mistake 2: Over-Hedging

**Problem:** Hedging more than actual exposure
**Fix:** Regular exposure reconciliation, hedge limits

### Mistake 3: Speculation

**Problem:** Taking positions beyond hedging needs
**Fix:** Clear policy prohibition, audit trails

### Mistake 4: Complexity

**Problem:** Using exotic structures not understood
**Fix:** Stick to plain vanilla instruments

### Mistake 5: Ignoring Basis Risk

**Problem:** Hedge doesn't match exposure timing/amount
**Fix:** Match hedge to underlying carefully

---

## Natural Hedging

### What Is Natural Hedging?

Matching FX inflows and outflows to reduce net exposure.

### Strategies

**1. Revenue-Cost Matching:**
- Price exports in same currency as imports
- Match currency of costs and revenues

**2. Financing Match:**
- Borrow in currency of revenue
- Creates natural offset

**3. Asset-Liability Match:**
- USD assets funded by USD liabilities

**Example:**
- Export revenue: $2 million annually
- Import cost: $1.5 million annually
- Net exposure: $0.5 million (80% naturally hedged)

### Benefits

- No transaction costs
- No counterparty risk
- Automatic hedge
- Simpler management

---

## Key Takeaways

1. **Identify all exposures** – Transaction, translation, economic
2. **Have a policy** – Document and follow consistently
3. **Forwards are workhorses** – Simple, effective, widely available
4. **Options for flexibility** – When direction uncertain or upside wanted
5. **Natural hedge first** – Match revenues and costs where possible
6. **Follow RBI rules** – Underlying requirement, approved instruments
7. **Don't speculate** – Hedging ≠ profiting from FX views

---

## Disclaimer

*This article is for educational purposes only. FX risk management requires professional expertise. Consult with treasury specialists and refer to current RBI guidelines. This is not financial advice.*

---

## Frequently Asked Questions

**Q: Should we hedge all FX exposure?**
A: Not necessarily. Consider materiality, natural hedges, cost of hedging, and policy. Most companies hedge 50-75% of material exposures.

**Q: Forward or option?**
A: Forwards for definite exposures with no view on direction. Options when you want protection but also upside participation, or when cash flows are uncertain.

**Q: How far out should we hedge?**
A: Depends on exposure visibility. Firm commitments: fully. Forecasts: 6-12 months typical. Beyond 12 months: selectively and strategically.

**Q: What about translation exposure?**
A: Often not hedged (no cash impact). Some companies hedge for earnings stability, particularly if analysts focus on consolidated results.

**Q: Can we profit from FX hedging?**
A: Hedging is about reducing uncertainty, not generating profits. Occasional "gains" happen, but systematic profits require forecasting ability most corporates don't have.

*FX hedging is like insurance—you hope you don't need it, but you're glad you have it when the unexpected happens. The goal isn't to beat the market, but to sleep well at night knowing your business results won't be determined by currency traders.*
