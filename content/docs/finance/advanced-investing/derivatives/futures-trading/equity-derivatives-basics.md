---
title: "Equity Derivatives: Futures and Options Basics"
date: 2025-01-15
weight: 5
description: "Understand equity derivatives fundamentals - futures and options basics, mechanics, pricing, strategies, and risk management for F&O trading in Indian markets."
tags: ["equity derivatives", "futures", "options", "F&O trading", "derivatives basics"]
series: ["Financial Markets"]
series_order: 5
seo_title: "Equity Derivatives Guide | Futures and Options Basics India"
seo_description: "Complete guide to equity derivatives - futures and options basics, mechanics, pricing, payoffs, basic strategies, and risk management for F&O trading in India."
keywords: ["equity derivatives", "futures trading", "options trading", "F&O basics", "derivatives India"]
ShowToc: true
TocOpen: true
---

## Introduction: Beyond Buying and Selling Stocks

"Derivatives let you bet on price movements without owning the underlying asset."

When you hear traders talk about "Nifty futures," "call options," or "hedging with puts," they're discussing derivatives. These instruments derive their value from underlying assets (stocks, indices) and enable trading, hedging, and speculation with leverage. Understanding derivatives opens up a new dimension of market participation.

---

## What Are Derivatives?

### Definition

A derivative is a financial contract whose value is derived from an underlying asset (stock, index, commodity, currency). The contract specifies rights and obligations based on the underlying's price movements.

### Key Characteristics

| Feature | Description |
|---------|-------------|
| Underlying | Asset from which value is derived |
| Expiry | Contract end date |
| Contract Size | Fixed quantity per contract |
| Settlement | Cash or physical delivery |
| Leverage | Control large position with small margin |

### Types of Derivatives

| Type | Description | Obligation |
|------|-------------|------------|
| Futures | Agreement to buy/sell at future date | Binding on both parties |
| Options | Right (not obligation) to buy/sell | Binding on seller only |
| Forwards | Like futures, but customized OTC | Binding on both parties |
| Swaps | Exchange of cash flows | Binding on both parties |

---

## Futures Contracts

### What Is a Futures Contract?

A standardized agreement to buy or sell an asset at a predetermined price on a specified future date.

### Futures Terminology

| Term | Meaning |
|------|---------|
| Lot Size | Number of units per contract |
| Expiry | Contract maturity date |
| Long Position | Buy futures (expecting price rise) |
| Short Position | Sell futures (expecting price fall) |
| Open Interest | Total outstanding contracts |
| Settlement | Cash (India) or delivery |

### Example: Nifty Futures

**Specifications:**
- Underlying: Nifty 50 Index
- Lot Size: 25 units
- Expiry: Last Thursday of month
- Settlement: Cash settled

**Position Example:**
- Nifty Spot: 22,000
- Nifty Futures (Near Month): 22,050
- Buy 1 lot (25 units)
- Contract Value: 25 × 22,050 = ₹5,51,250

**Margin Required:** ~₹1,10,000 (approximately 20%)

**Leverage:** Control ₹5.5 lakh position with ₹1.1 lakh

### Futures Pricing

**Cost of Carry Model:**
$$Futures\ Price = Spot\ Price + Cost\ of\ Carry$$

$$F = S \times e^{(r-d) \times t}$$

Where:
- F = Futures price
- S = Spot price
- r = Risk-free rate
- d = Dividend yield
- t = Time to expiry

**Typically:** Futures trade at premium to spot (contango) due to cost of carry.

### Profit/Loss Calculation

$$P\&L = (Exit\ Price - Entry\ Price) \times Lot\ Size$$

**Example:**
- Buy Nifty Futures at 22,000
- Exit at 22,300
- Lot Size: 25
- Profit: (22,300 - 22,000) × 25 = ₹7,500

---

## Options Contracts

### What Is an Option?

A contract giving the buyer the right (not obligation) to buy or sell an underlying at a specified price before/on a specified date.

### Options Terminology

| Term | Meaning |
|------|---------|
| Strike Price | Price at which option can be exercised |
| Premium | Price paid for the option |
| Call Option | Right to buy |
| Put Option | Right to sell |
| Expiry | Contract maturity date |
| Exercise | Using the option right |

### Call Options

**Definition:** Right to buy the underlying at strike price.

**Buyer's View:** Bullish (expects price to rise)

**Example:**
- Nifty Spot: 22,000
- Buy Call Option: Strike 22,200
- Premium: ₹150 per unit
- Cost: 25 × ₹150 = ₹3,750

**At Expiry:**
- If Nifty at 22,500: Profit = (22,500 - 22,200 - 150) × 25 = ₹3,750
- If Nifty at 22,000: Loss = Premium = ₹3,750

### Put Options

**Definition:** Right to sell the underlying at strike price.

**Buyer's View:** Bearish (expects price to fall)

**Example:**
- Nifty Spot: 22,000
- Buy Put Option: Strike 21,800
- Premium: ₹120 per unit
- Cost: 25 × ₹120 = ₹3,000

**At Expiry:**
- If Nifty at 21,500: Profit = (21,800 - 21,500 - 120) × 25 = ₹4,500
- If Nifty at 22,000: Loss = Premium = ₹3,000

### Moneyness

| Status | Call Option | Put Option |
|--------|-------------|------------|
| In-the-Money (ITM) | Spot > Strike | Spot < Strike |
| At-the-Money (ATM) | Spot ≈ Strike | Spot ≈ Strike |
| Out-of-Money (OTM) | Spot < Strike | Spot > Strike |

**Example (Nifty at 22,000):**
- Call 21,500: ITM (₹500 intrinsic value)
- Call 22,000: ATM
- Call 22,500: OTM

---

## Options Pricing

### Premium Components

$$Premium = Intrinsic\ Value + Time\ Value$$

**Intrinsic Value:**
- Call: Max(Spot - Strike, 0)
- Put: Max(Strike - Spot, 0)

**Time Value:**
- Additional premium for time remaining
- Decreases as expiry approaches (time decay)
- Maximum for ATM options

### Factors Affecting Premium

| Factor | Call Impact | Put Impact |
|--------|-------------|------------|
| Spot Price ↑ | Premium ↑ | Premium ↓ |
| Strike Price ↑ | Premium ↓ | Premium ↑ |
| Time to Expiry ↑ | Premium ↑ | Premium ↑ |
| Volatility ↑ | Premium ↑ | Premium ↑ |
| Interest Rate ↑ | Premium ↑ | Premium ↓ |

### The Greeks

| Greek | Measures | Symbol |
|-------|----------|--------|
| Delta | Price sensitivity to underlying | Δ |
| Gamma | Rate of change of Delta | Γ |
| Theta | Time decay | Θ |
| Vega | Volatility sensitivity | ν |
| Rho | Interest rate sensitivity | ρ |

**Most Important:**
- **Delta:** How much premium changes for ₹1 move in underlying
- **Theta:** How much premium erodes daily (negative for buyers)

---

## F&O Segments in India

### Index Derivatives

| Index | Exchange | Lot Size |
|-------|----------|----------|
| Nifty 50 | NSE | 25 |
| Bank Nifty | NSE | 15 |
| Nifty Midcap Select | NSE | 75 |
| FinNifty | NSE | 40 |
| Sensex | BSE | 10 |

### Stock Derivatives

- ~200+ stocks with F&O
- Individual lot sizes vary
- Higher margins than index F&O

### Expiry Schedule

**Index Options:**
- Weekly expiry (Nifty, Bank Nifty)
- Monthly expiry

**Stock Options:**
- Monthly expiry only

**Futures:**
- Monthly expiry (3 consecutive months)

---

## Basic Strategies

### Long Call (Bullish)

**View:** Strongly bullish
**Action:** Buy Call Option
**Max Profit:** Unlimited
**Max Loss:** Premium paid

**Payoff Diagram:**
```
Profit
  |     /
  |    /
  |___/____Strike_____ Spot
  |Premium Lost
Loss
```

### Long Put (Bearish)

**View:** Strongly bearish
**Action:** Buy Put Option
**Max Profit:** Strike - Premium (if spot goes to 0)
**Max Loss:** Premium paid

### Covered Call

**View:** Mildly bullish
**Action:** Own stock + Sell Call
**Purpose:** Generate income from holdings
**Risk:** Give up upside above strike

### Protective Put

**View:** Bullish but want protection
**Action:** Own stock + Buy Put
**Purpose:** Insurance against downside
**Cost:** Premium paid reduces returns

### Straddle

**View:** Expect big move, unsure of direction
**Action:** Buy Call + Buy Put (same strike)
**Max Profit:** Unlimited
**Max Loss:** Both premiums

### Iron Condor

**View:** Range-bound market
**Action:** Sell OTM Call + Sell OTM Put + Buy further OTM Call + Buy further OTM Put
**Max Profit:** Net premium received
**Max Loss:** Defined by strikes

---

## Risk Management

### Margin Requirements

**SPAN Margin + Exposure Margin**

| Component | Purpose |
|-----------|---------|
| SPAN Margin | Worst-case loss estimate |
| Exposure Margin | Additional buffer |
| MTM | Daily mark-to-market settlement |

### Position Sizing

**Rule of Thumb:**
- Risk only 1-2% of capital per trade
- Never risk more than you can afford to lose
- Account for leverage amplification

### Stop Losses

**Essential for:**
- Futures (unlimited loss potential)
- Sold options (unlimited loss potential)

**Option Buyers:**
- Loss limited to premium (built-in stop loss)
- But can still set stops to preserve capital

### Common Mistakes

1. **Over-leveraging:** Taking too large positions
2. **No stop loss:** Hoping losing trade will recover
3. **Fighting the trend:** Picking tops and bottoms
4. **Ignoring time decay:** Holding OTM options too long
5. **Not understanding:** Trading without knowledge

---

## Taxation

### Futures

**Treated as speculative business income:**
- Profits/losses: Business income
- Taxed at slab rate
- Can offset against other business income
- Carry forward losses 8 years

### Options

**Similar treatment as futures:**
- Business income
- Premium received = income
- Premium paid = expense

### STT (Securities Transaction Tax)

| Transaction | STT Rate |
|-------------|----------|
| Futures (sell) | 0.0125% |
| Options (sell) | 0.0625% on premium |
| Options (exercise) | 0.125% on settlement |

**Note:** STT makes frequent trading costly.

---

## Getting Started

### Prerequisites

1. **Knowledge:** Understand basics before trading
2. **Trading Account:** F&O enabled with broker
3. **Capital:** Adequate margin money
4. **Risk Appetite:** Accept potential losses

### Learning Path

1. **Paper trading:** Practice without real money
2. **Small positions:** Start with 1 lot
3. **Index options:** Begin here (more liquid)
4. **Simple strategies:** Buy calls/puts first
5. **Complex strategies:** Only after mastery

### Capital Requirements

**Minimum Practical Capital:**
- Index Options: ₹50,000-1,00,000
- Index Futures: ₹1,50,000-2,00,000
- Stock F&O: ₹2,00,000+

---

## Key Takeaways

1. **Futures = Binding agreement** – Both parties obligated
2. **Options = Right, not obligation** – Buyer has choice
3. **Leverage cuts both ways** – Amplifies gains and losses
4. **Time decay hurts buyers** – Options lose value daily
5. **Risk management essential** – Stop losses, position sizing
6. **Start small** – Learn with limited capital
7. **Knowledge first** – Understand before trading

---

## Disclaimer

*This article is for educational purposes only. Derivatives trading involves substantial risk of loss. Most retail traders lose money in F&O. Only trade with capital you can afford to lose. This is not trading advice or recommendation.*

---

## Frequently Asked Questions

**Q: Can I lose more than I invest in options?**
A: As an option buyer, maximum loss is the premium paid. As an option seller, losses can be unlimited. Futures positions can also result in losses exceeding initial margin.

**Q: Why do most traders lose money in F&O?**
A: Leverage amplifies losses, time decay works against option buyers, emotional trading, inadequate knowledge, and poor risk management. Statistics show 90%+ retail traders lose money.

**Q: When should I use futures vs options?**
A: Futures for directional views with higher leverage. Options for defined risk (buying) or income generation (selling). Options also useful for hedging.

**Q: What's the best strategy for beginners?**
A: Start with buying options (limited loss) on indices (more liquid). Learn single-leg strategies before complex ones. Paper trade first.

**Q: How much capital do I need?**
A: Technically, you can start with one lot margin (~₹50,000 for index options). Practically, ₹2-3 lakh gives more flexibility and risk management room.

*Derivatives are like power tools—incredibly useful in the right hands, but dangerous without proper training. They can help you hedge risk, generate income, and profit from market movements. But they can also quickly destroy capital if misused. Respect the leverage.*
