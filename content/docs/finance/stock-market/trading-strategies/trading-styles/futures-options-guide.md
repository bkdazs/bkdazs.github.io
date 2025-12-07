---
title: "Futures and Options Trading in India: A Complete Beginner's Guide"
date: 2025-12-05
weight: 11
description: "Complete guide to F&O trading in India. Learn about futures contracts, call and put options, Greeks, and strategies for trading derivatives on NSE."
keywords: ["futures and options India", "F&O trading", "options trading", "derivatives India", "call put options", "NSE derivatives"]
tags:
  - futures
  - options
  - derivatives
  - F&O trading
series:
  - "Stock Market Basics"
cover:
  image: ""
  alt: "Futures and Options Trading Guide"
  caption: "Master F&O trading in India"
  relative: false
ShowToc: true
TocOpen: true
---

## The Tale of Two Friends: Amit and Vikram

Amit and Vikram both believed Reliance would rise from ₹2,400 to ₹2,600 by month end.

**Amit** (Cash Market):
- Bought 100 shares at ₹2,400
- Investment: ₹2,40,000
- If right (stock hits ₹2,600): Profit ₹20,000 (8.3%)
- If wrong (stock falls to ₹2,300): Loss ₹10,000 (4.2%)

**Vikram** (Options Market):
- Bought Reliance 2500 Call Option at ₹50
- Investment: ₹50 × 250 (lot size) = ₹12,500
- If right: Option worth ₹100+ → Profit ₹12,500+ (100%+)
- If wrong: Maximum loss = ₹12,500 (premium paid)

Same view. Different instruments. Different outcomes.

This is the power – and danger – of derivatives. Let's understand them properly.

---

## What Are Derivatives?

Derivatives are financial contracts whose value is "derived" from an underlying asset (stock, index, commodity).

### Types of Derivatives in India

| Type | What It Does |
|------|--------------|
| **Futures** | Agreement to buy/sell at future date |
| **Options** | Right (not obligation) to buy/sell |
| **Swaps** | Exchange of cash flows (not for retail) |
| **Forwards** | Like futures, but OTC (not for retail) |

**For retail traders**: Focus on Futures and Options (F&O)

---

## Understanding Futures Contracts

### What is a Futures Contract?

An agreement to buy or sell an asset at a predetermined price on a specific future date.

### Key Terms

| Term | Meaning |
|------|---------|
| **Underlying** | The asset (Reliance, Nifty, etc.) |
| **Lot Size** | Minimum quantity per contract |
| **Expiry** | Contract end date (last Thursday of month) |
| **Margin** | Upfront deposit required (not full value) |

### Futures Example: Reliance

**Current Situation (Dec 2024)**:
- Reliance Spot Price: ₹2,450
- Reliance Dec Futures: ₹2,465 (premium for time value)
- Lot Size: 250 shares
- Margin Required: ~₹1,50,000 (varies)

**Scenario**: You buy 1 lot of Reliance Dec Futures

```
Contract Value: ₹2,465 × 250 = ₹6,16,250
Margin Paid: ₹1,50,000 (about 25%)
Leverage: 4x approximately
```

**On Expiry (if Reliance at ₹2,600)**:
```
Profit = (2,600 - 2,465) × 250 = ₹33,750
Return on Margin = 33,750/1,50,000 = 22.5%
```

**On Expiry (if Reliance at ₹2,300)**:
```
Loss = (2,465 - 2,300) × 250 = ₹41,250
Loss on Margin = 41,250/1,50,000 = 27.5%
```

### Types of Futures

1. **Stock Futures**: Based on individual stocks (Reliance, TCS, etc.)
2. **Index Futures**: Based on indices (Nifty, Bank Nifty)
3. **Currency Futures**: USD/INR, EUR/INR, etc.
4. **Commodity Futures**: Gold, Silver, Crude (MCX)

---

## Understanding Options

### What is an Option?

An option gives you the RIGHT (but not obligation) to buy or sell an asset at a specific price before a certain date.

### Call Option vs Put Option

| Type | Right To | When to Buy |
|------|----------|-------------|
| **Call Option** | BUY the underlying | Bullish view |
| **Put Option** | SELL the underlying | Bearish view |

### Option Terminology

| Term | Meaning |
|------|---------|
| **Strike Price** | Price at which you can buy/sell |
| **Premium** | Price you pay for the option |
| **Expiry** | Last day the option is valid |
| **ITM** | In The Money (profitable if exercised now) |
| **ATM** | At The Money (strike = current price) |
| **OTM** | Out of The Money (not profitable if exercised now) |

### Call Option Example

**Scenario**: Nifty at 20,000. You're bullish.

**Buy**: Nifty 20,200 Call @ ₹150
- Lot Size: 25
- Premium Paid: ₹150 × 25 = ₹3,750

**On Expiry**:

| Nifty Level | Option Value | Your P/L |
|-------------|--------------|----------|
| 19,800 | ₹0 (OTM, worthless) | -₹3,750 |
| 20,000 | ₹0 (OTM) | -₹3,750 |
| 20,200 | ₹0 (ATM) | -₹3,750 |
| 20,350 | ₹150 (breakeven) | ₹0 |
| 20,500 | ₹300 | +₹3,750 |
| 21,000 | ₹800 | +₹16,250 |

**Key Insight**: Maximum loss = Premium paid (₹3,750)
Maximum profit = Unlimited (as Nifty can rise indefinitely)

### Put Option Example

**Scenario**: You own Reliance at ₹2,500. Worried about fall.

**Buy**: Reliance 2,400 Put @ ₹30
- Lot Size: 250
- Premium Paid: ₹30 × 250 = ₹7,500

**Protection**: If Reliance falls to ₹2,200, your put is worth ₹200 × 250 = ₹50,000

This offsets your stock loss. It's like insurance!

---

## The Option Greeks

Options prices are affected by multiple factors, measured by "Greeks."

### Delta (Δ)

How much option price changes for ₹1 move in underlying.

| Option Type | Delta Range |
|-------------|-------------|
| Call | 0 to +1 |
| Put | -1 to 0 |
| ATM Call | ~0.5 |
| ATM Put | ~-0.5 |
| Deep ITM Call | ~0.9-1.0 |
| Deep OTM Call | ~0.1-0.2 |

**Example**: Nifty Call with Delta 0.5
- Nifty moves up ₹100
- Option price moves up ₹50 (approximately)

### Theta (θ)

Time decay – how much value an option loses each day.

**Key Point**: Options lose value daily due to time decay. This accelerates near expiry.

**Example**: Option premium ₹100, Theta = -5
- Tomorrow (all else equal): Option premium = ₹95

**Who Theta Helps**: Option sellers (they want decay)
**Who Theta Hurts**: Option buyers (they're against time)

### Vega (V)

How option price changes with volatility changes.

High Vega = Option price sensitive to volatility
Budget, Election, Results = Volatility increases = Option premiums increase

### Gamma (Γ)

Rate of change of Delta. Important for advanced trading.

---

## Important Concepts

### Intrinsic Value vs Time Value

```
Option Premium = Intrinsic Value + Time Value
```

**Intrinsic Value**: Real value if exercised now
- Call: Max(Spot - Strike, 0)
- Put: Max(Strike - Spot, 0)

**Time Value**: Extra premium for potential future movement

**Example**: Nifty at 20,000
- 19,800 Call Premium: ₹300
- Intrinsic Value: 20,000 - 19,800 = ₹200
- Time Value: ₹300 - ₹200 = ₹100

### Open Interest (OI)

Total number of outstanding contracts.

| OI Signal | Interpretation |
|-----------|----------------|
| Rising OI + Price Rise | Bullish strength |
| Rising OI + Price Fall | Bearish strength |
| Falling OI + Price Rise | Short covering (weak rally) |
| Falling OI + Price Fall | Long unwinding (weak fall) |

### PCR (Put Call Ratio)

```
PCR = Put OI / Call OI
```

| PCR Value | Market Sentiment |
|-----------|------------------|
| Above 1.2 | Oversold (potential bottom) |
| 0.8 - 1.2 | Neutral |
| Below 0.8 | Overbought (potential top) |

---

## Basic F&O Strategies

### Strategy 1: Long Call (Bullish)

**When**: Very bullish on stock/index
**Risk**: Premium paid
**Reward**: Unlimited

### Strategy 2: Long Put (Bearish)

**When**: Very bearish
**Risk**: Premium paid
**Reward**: High (limited to strike price)

### Strategy 3: Covered Call

**Setup**: Own stock + Sell Call
**When**: Neutral to slightly bullish
**Example**:
- Own 250 Reliance at ₹2,500
- Sell 2,600 Call @ ₹40
- Income: ₹10,000
- If stock stays below ₹2,600: Keep stock + premium

### Strategy 4: Protective Put

**Setup**: Own stock + Buy Put
**When**: Own stock but worried about downside
**Example**: Like insurance for your holdings

### Strategy 5: Bull Call Spread

**Setup**: Buy lower strike Call + Sell higher strike Call
**Example**:
- Buy Nifty 20,000 Call @ ₹200
- Sell Nifty 20,500 Call @ ₹50
- Net Premium: ₹150
- Max Profit: ₹500 - ₹150 = ₹350
- Max Loss: ₹150

### Strategy 6: Iron Condor (Range-bound view)

**Setup**: Sell OTM Put + Buy further OTM Put + Sell OTM Call + Buy further OTM Call

**When**: Expecting stock to stay in range

---

## F&O Trading in India: Practical Details

### Eligible Stocks for F&O

Not all stocks have F&O. Only ~180 stocks are in F&O segment.

**Examples**: Reliance, TCS, Infosys, HDFC Bank, SBI, Tata Motors, etc.

### Lot Sizes (2024)

| Stock/Index | Lot Size |
|-------------|----------|
| Nifty 50 | 25 |
| Bank Nifty | 15 |
| Fin Nifty | 25 |
| Reliance | 250 |
| TCS | 175 |
| HDFC Bank | 550 |
| Infosys | 300 |

### Expiry Cycles

**Weekly Expiries**: Every Thursday
- Nifty
- Bank Nifty
- Fin Nifty

**Monthly Expiries**: Last Thursday of month
- All F&O stocks

### Margin Requirements

**For Option Buying**: Full premium (no margin)
**For Option Selling**: SPAN + Exposure margin (significant)

**Example**: Selling Nifty 19,500 Put
- Premium Received: ₹8,000
- Margin Required: ₹1,00,000+

---

## Taxation of F&O

### F&O as Business Income

F&O profits/losses are treated as **Business Income** (not capital gains).

**Tax Rate**: Your income tax slab rate

### Turnover Calculation

```
Turnover = Absolute profit + Absolute loss + Premium (for options)
```

**If Turnover < ₹2 Crore**: Can use presumptive taxation (6% of turnover as profit)

### ITR Filing

- File ITR-3 (if F&O income)
- Maintain books of accounts if turnover > ₹1 Crore (50 lakhs for non-audit)

---

## Risks of F&O Trading

### 1. Leverage Risk

Small moves can cause large % gains/losses.

**Example**: With 5x leverage
- 2% adverse move = 10% capital loss

### 2. Time Decay (Options)

Options lose value daily. You can be right on direction but wrong on timing and still lose.

### 3. Unlimited Loss (Option Selling)

Selling naked options = potentially unlimited losses.

### 4. Liquidity Risk

Illiquid options can have wide bid-ask spreads. Entry/exit becomes expensive.

### 5. Assignment Risk (Option Selling)

If your sold option goes ITM, you may be assigned.

---

## Who Should Trade F&O?

### It's For You If:

- You have adequate capital (₹2-5 lakhs minimum)
- You understand risk management deeply
- You've traded cash market successfully
- You can dedicate time to learning
- You can afford to lose

### It's NOT For You If:

- You're using borrowed money
- You don't understand options Greeks
- You're looking for quick money
- You haven't traded cash market
- You can't accept losses

---

## Learning Path for F&O

### Phase 1: Foundation (1-2 months)

1. Understand futures pricing
2. Learn options basics
3. Study the Greeks
4. Paper trade

### Phase 2: Simulation (1-2 months)

1. Use virtual trading platforms
2. Practice different strategies
3. Track your hypothetical P&L

### Phase 3: Small Real Trades (3-6 months)

1. Start with single lot
2. Only buy options (limited risk)
3. Focus on learning, not earning

### Phase 4: Strategy Expansion (6+ months)

1. Add spreads
2. Learn option selling (with proper margin)
3. Develop your edge

---

## Resources for Learning

### Free Resources

| Resource | What You'll Learn |
|----------|-------------------|
| Zerodha Varsity | Complete F&O education |
| NSE Academy | Official courses |
| Sensibull Blog | Options analysis |
| Opstra | Strategy builder |

### Tools

| Tool | Use |
|------|-----|
| Sensibull | Options analysis |
| Opstra | Payoff diagrams |
| Quantsapp | OI analysis |
| Shoonya | Free trading |

---

## Risk Disclaimer

F&O trading involves substantial risk of loss. Leveraged positions can result in losses exceeding your initial investment. Options can expire worthless. This content is for educational purposes only and not trading advice. Only trade with capital you can afford to lose. Consult a SEBI-registered advisor.

---

## Summary

Futures and Options are powerful but risky:

**Futures**: Leveraged bets on direction (unlimited profit/loss)
**Options**: Asymmetric payoffs (limited loss for buyers, unlimited for sellers)

Start small. Learn constantly. Respect the risk.

The F&O market will always be there. Your capital may not be if you rush in unprepared.

---

## Social Media Posts

**LinkedIn**:
"Started learning F&O 6 months ago. Here's my honest update: Still paper trading. Haven't lost real money. Why? Because I'm learning that the real edge in options isn't knowing strategies – it's understanding risk. Taking it slow. Will update in 6 more months. #OptionsTrading #LearningJourney"

**Twitter/X**:
"Options Trading Reality:
✅ Can make 100% returns
❌ Can also lose 100% of premium
✅ Defined risk for buyers
❌ Unlimited risk for sellers
✅ Hedge your portfolio
❌ Not a get-rich-quick scheme

Learn before you earn. #FnO #OptionsTrading"

**Instagram**:
"Friend: 'I made 500% return on options!'
Me: 'What about your last 10 trades?'
Friend: 😶

Options trading truth:
• Beginners get lucky once
• Then lose it all (and more)
• Survivors learn risk management first

Which trader do you want to be? 🤔 #OptionsTrading #TradingReality"
