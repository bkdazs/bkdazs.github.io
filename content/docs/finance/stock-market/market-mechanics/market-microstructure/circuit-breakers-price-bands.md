---
title: "Circuit Breakers and Price Bands Explained"
date: 2025-01-15
weight: 24
description: "Understanding circuit breakers and price bands - how they work in Indian markets, index-wide halts, stock circuit limits, and their purpose in market stability."
tags: ["circuit breaker", "price band", "trading halt", "market volatility", "circuit limit"]
series: ["Financial Markets"]
series_order: 24
seo_title: "Circuit Breakers Price Bands | Market Trading Halts"
seo_description: "Complete guide to circuit breakers and price bands - how trading halts work in India, index-wide circuit breakers, stock price bands, and market stability."
keywords: ["circuit breaker", "price band", "trading halt", "circuit limit", "market stability"]
ShowToc: true
TocOpen: true
---

## Introduction: Market Safety Valves

"Circuit breakers are the emergency brakes of financial markets—they provide a pause during extreme movements, giving traders time to reassess and preventing panic-driven crashes."

When markets move too fast, circuit breakers kick in to halt trading temporarily. Understanding these mechanisms helps you know what to expect during volatile periods and why your orders might not execute at certain times.

---

## What Are Circuit Breakers?

### Definition

Circuit breakers are pre-defined rules that automatically halt trading when prices move beyond specified thresholds.

### Purpose

| Objective | How Circuit Breakers Help |
|-----------|---------------------------|
| Prevent panic | Cooling-off period |
| Allow information processing | Time to digest news |
| Reduce volatility | Break cascading moves |
| Protect investors | Prevent irrational decisions |
| Maintain orderly markets | Prevent manipulation |

### Types in India

| Type | Scope | Trigger |
|------|-------|---------|
| Index-wide circuit breakers | Entire market | Nifty/Sensex movement |
| Stock-specific price bands | Individual stock | Stock price movement |

---

## Index-Wide Circuit Breakers

### Trigger Levels

Based on movement in Sensex or Nifty 50 (whichever breaches first):

| Level | Movement | Description |
|-------|----------|-------------|
| Level 1 | 10% | First trigger |
| Level 2 | 15% | Second trigger |
| Level 3 | 20% | Final trigger |

### Halt Duration

| Trigger | Before 1:00 PM | 1:00 PM - 2:30 PM | After 2:30 PM |
|---------|----------------|-------------------|---------------|
| 10% | 45 minutes | 15 minutes | No halt |
| 15% | 1 hour 45 minutes | 45 minutes | Trading closed for day |
| 20% | Trading closed for day | Trading closed for day | Trading closed for day |

### How It Works

**Example - 10% Trigger at 11:30 AM:**
1. Market falls 10% from previous close
2. Trading halted for 45 minutes
3. Pre-open session of 15 minutes
4. Trading resumes

**Example - 20% Trigger Any Time:**
1. Market falls 20%
2. Trading closed for the day
3. Resumes next trading day

### Calculation Basis

**Reference Point:** Previous day's closing index value

**Example:**
| Metric | Value |
|--------|-------|
| Previous close (Nifty) | 20,000 |
| 10% trigger | 18,000 or 22,000 |
| 15% trigger | 17,000 or 23,000 |
| 20% trigger | 16,000 or 24,000 |

**Note:** Triggers work for both upside and downside movements.

---

## Stock-Specific Price Bands

### What Are Price Bands?

Maximum daily price movement allowed for individual stocks, expressed as percentage of previous close.

### Price Band Categories

| Band | Applicable To |
|------|--------------|
| ±2% | Extremely illiquid, new listings |
| ±5% | Illiquid stocks |
| ±10% | Most stocks |
| ±20% | Liquid stocks, F&O stocks initially |
| No band | F&O stocks, indices |

### Determining Price Band

**Factors Considered:**
- Liquidity (trading volume)
- Market capitalization
- F&O availability
- Volatility history
- Surveillance concerns

### Price Band Changes

**SEBI/Exchange Actions:**
| Change | When |
|--------|------|
| Narrower band | Unusual volatility, surveillance |
| Wider band | Increased liquidity |
| Removal | Entry into F&O |

### Graded Surveillance Measure (GSM)

**For Suspicious Price Movements:**
| Stage | Restriction |
|-------|-------------|
| Stage 1 | Warning, additional margin |
| Stage 2 | 5% price band |
| Stage 3 | 5% band, trade-to-trade |
| Stage 4 | Once weekly settlement |
| Stage 5 | Once monthly settlement |
| Stage 6 | Trading suspension |

---

## How Price Bands Work

### Upper Circuit

**Definition:** Maximum price the stock can reach in a day

**Formula:**
$$Upper\ Circuit = Previous\ Close \times (1 + Band\%)$$

**Example (10% band):**
| Metric | Value |
|--------|-------|
| Previous close | ₹100 |
| Upper circuit | ₹110 |
| Stock hits ₹110 | Cannot trade higher |

### Lower Circuit

**Definition:** Minimum price the stock can fall to in a day

**Formula:**
$$Lower\ Circuit = Previous\ Close \times (1 - Band\%)$$

**Example (10% band):**
| Metric | Value |
|--------|-------|
| Previous close | ₹100 |
| Lower circuit | ₹90 |
| Stock hits ₹90 | Cannot trade lower |

### At Circuit Price

**What Happens:**
| Scenario | Situation |
|----------|-----------|
| Upper circuit | Only sellers, no buyers willing at higher |
| Lower circuit | Only buyers, no sellers willing at lower |
| Trading continues | At circuit price only |
| Matched trades | If counterparty exists |

---

## Upper Circuit vs Lower Circuit

### Upper Circuit Hit

**Signals:**
- Strong buying demand
- Positive news/development
- Potentially manipulated

**For Buyers:**
- May not get shares
- Queued orders may not fill
- Tomorrow's price uncertain

**For Holders:**
- Can't sell at higher price
- Profit booking limited
- Wait for next day

### Lower Circuit Hit

**Signals:**
- Heavy selling pressure
- Negative news/development
- Panic or manipulation

**For Sellers:**
- May not get out
- Queued orders may not fill
- Forced to hold

**For Buyers:**
- Might be opportunity
- But risk of continued fall
- Due diligence needed

---

## Trading During Circuit Situations

### Order Placement

| Scenario | Your Order |
|----------|------------|
| Stock at upper circuit | Buy orders queue, unlikely fill |
| Stock at upper circuit | Sell orders execute instantly |
| Stock at lower circuit | Sell orders queue, unlikely fill |
| Stock at lower circuit | Buy orders execute instantly |

### Queue Priority

**At Circuit Price:**
1. Price priority (same for all at circuit)
2. Time priority (earlier orders first)

**Implication:** If you want to buy at upper circuit, place order early.

### Continuous vs Periodic Auction

**Some Exchanges Use:**
- Call auction at circuit
- Periodic price discovery
- More orderly price formation

---

## Circuit Breaker History in India

### Notable Triggers

| Date | Event | Trigger |
|------|-------|---------|
| May 17, 2004 | UPA government formation | 10%, 15% |
| Oct 24, 2008 | Global financial crisis | 10% |
| March 13, 2020 | COVID-19 pandemic | 10% |
| March 23, 2020 | COVID-19 lockdown | 10%, 15% |

### March 2020 Episode

**March 13, 2020:**
- Markets fell 10% at open
- 45-minute halt triggered
- Resumed and fell further

**March 23, 2020:**
- Markets fell over 10% again
- Circuit breaker activated twice in a week

### Lessons Learned

| Lesson | Implication |
|--------|-------------|
| Circuits don't prevent decline | Just pause it |
| Information processing | Traders reassess |
| Panic can continue | Next day may see more fall |
| Entry opportunity | For contrarians |

---

## Criticisms and Debates

### Arguments For Circuit Breakers

| Argument | Rationale |
|----------|-----------|
| Cooling off | Emotional traders calm down |
| Information processing | Time to understand news |
| Prevent manipulation | Stop pump and dump |
| Orderly markets | Prevent chaos |

### Arguments Against

| Argument | Rationale |
|----------|-----------|
| Delay inevitable | Price will adjust anyway |
| Uncertainty | Traders don't know true price |
| Gaming | Traders anticipate circuits |
| Magnet effect | May attract price to circuit |

### Magnet Effect

**Phenomenon:**
- Prices accelerate toward circuit as it approaches
- Traders rush to trade before halt
- Self-fulfilling prophecy

---

## International Comparison

### US Markets

| Mechanism | Details |
|-----------|---------|
| Market-wide | 7%, 13%, 20% S&P 500 |
| Individual stock | Limit up/limit down (LULD) |
| Duration | 15 minutes typically |

### China

| Feature | Details |
|---------|---------|
| Index circuit | 5%, 7% triggers |
| Stock limit | ±10% daily |
| History | Suspended in 2016 after issues |

### Comparison

| Market | Index Trigger | Stock Limit |
|--------|---------------|-------------|
| India | 10%, 15%, 20% | ±2% to ±20% |
| USA | 7%, 13%, 20% | Dynamic bands |
| China | 5%, 7% | ±10% |

---

## Practical Tips

### For Traders

| Situation | Action |
|-----------|--------|
| Stock approaching circuit | Consider early action |
| Market near 10% | Prepare for halt |
| Stock at circuit | Use limit orders for next day |
| Illiquid stock | Expect frequent circuits |

### News-Based Circuits

**When to Expect:**
- Earnings surprises
- Major announcements
- Sector-specific news
- Global events

### Avoiding Circuit Stocks

**For Risk-Averse:**
- Check circuit band before buying
- Prefer F&O stocks (no band)
- Avoid GSM/ASM stocks
- Check liquidity

---

## Key Takeaways

1. **Index circuits** – 10%, 15%, 20% halt entire market
2. **Stock bands** – ±2% to ±20% limit daily movement
3. **F&O stocks** – No daily price band
4. **At circuit** – Trading continues, only one side active
5. **Purpose** – Prevent panic, allow information processing
6. **Not prevention** – Circuits don't stop trends
7. **Queue priority** – Time matters at circuit price

---

## Disclaimer

*This article is for educational purposes only. Circuit breakers are regulatory mechanisms. Trading during volatile periods carries significant risk. This is not trading advice.*

---

## Frequently Asked Questions

**Q: Can I trade when market hits circuit breaker?**
A: No, during index-wide circuit breaker, all trading is halted. For stock-specific, trading continues at circuit price, but only one side (buy or sell) will find counterparties.

**Q: Why do F&O stocks have no circuit?**
A: F&O stocks have high liquidity and institutional participation. Price discovery is better, and derivative margins provide risk management. However, they can still be affected by index-wide circuits.

**Q: How do I know a stock's circuit limit?**
A: Check NSE/BSE website, broker platform, or stock info section. Circuit percentages are displayed for each stock.

**Q: If I have a stop loss at lower circuit, will it execute?**
A: It will trigger as a market order, but execution depends on available buyers. In a one-sided circuit with only sellers, your order may queue.

**Q: Do circuits apply to commodities too?**
A: Yes, MCX has circuit limits for commodities. They're based on percentage movement and can trigger daily price limits or trading halts.

*Circuit breakers are the market's safety mechanism—understanding them helps you navigate volatile periods without panic and make more informed trading decisions.*
