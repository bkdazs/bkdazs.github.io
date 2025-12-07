---
title: "Stock Market Order Types: Market, Limit, Stop Loss, and More"
date: 2025-12-05
weight: 17
description: "Complete guide to stock market order types in India. Learn about market orders, limit orders, stop loss, bracket orders, and GTT for NSE and BSE trading."
keywords: ["stock market order types", "limit order", "market order", "stop loss order", "bracket order", "GTT order India"]
tags:
  - order types
  - trading
  - limit order
  - market order
series:
  - "Stock Market Basics"
cover:
  image: ""
  alt: "Stock Market Order Types Explained"
  caption: "Master different order types for better trading"
  relative: false
ShowToc: true
TocOpen: true
---

## The ₹10,000 Mistake Kavya Made

Kavya wanted to buy 100 shares of Tata Motors at ₹600. She placed a "Market Order" without understanding what it meant.

At that exact moment, due to some news, the stock was rapidly rising. Her order executed at ₹620.

She paid ₹62,000 instead of ₹60,000 – a ₹2,000 unexpected cost.

Had she used a "Limit Order" at ₹600, she would have either bought at ₹600 or not bought at all.

Understanding order types isn't optional. It's essential.

---

## Why Order Types Matter

Different situations require different orders:

| Situation | Best Order Type |
|-----------|-----------------|
| Need to buy immediately | Market Order |
| Want specific price | Limit Order |
| Protect against loss | Stop Loss |
| Lock in profit & limit loss | Bracket Order |
| Long-term price alerts | GTT |

Using the wrong order type can cost you money or missed opportunities.

---

## Order Type 1: Market Order

### What It Is

An instruction to buy or sell immediately at the **best available price** in the market.

### When to Use

- Highly liquid stocks (Reliance, HDFC Bank, TCS)
- When you need to enter/exit urgently
- Market moving fast and you don't want to miss

### When NOT to Use

- Illiquid stocks (low volume)
- High volatility moments
- Large quantity orders

### Example

```
Stock: Infosys
Current Bid: ₹1,545
Current Ask: ₹1,546

You place: Market Buy Order for 100 shares
Execution: ₹1,546 (best available ask)

Cost: ₹1,54,600
```

### Slippage Risk

**Slippage** = Difference between expected and actual execution price.

**Low slippage** (Liquid stocks):
- Order: Market Buy
- Expected: ₹1,546
- Actual: ₹1,546
- Slippage: ₹0

**High slippage** (Illiquid stocks):
- Order: Market Buy
- Expected: ₹150
- Actual: ₹155
- Slippage: ₹5 per share = 3.3%!

### How to Place (Zerodha)

1. Select stock
2. Click Buy/Sell
3. Order Type: "Market"
4. Quantity: Enter
5. Review & Submit

---

## Order Type 2: Limit Order

### What It Is

An instruction to buy or sell only at your **specified price or better**.

### How It Works

**Buy Limit**: Will execute only at your price or LOWER.
**Sell Limit**: Will execute only at your price or HIGHER.

### When to Use

- You're not in a hurry
- You want to control your entry price
- Stock is volatile
- Illiquid stocks

### Example: Buy Limit Order

```
Stock: Reliance
Current Price: ₹2,450

You place: Buy Limit Order at ₹2,430

Scenario 1: Stock drops to ₹2,430 → Order executes at ₹2,430 ✓
Scenario 2: Stock stays above ₹2,430 → Order doesn't execute
Scenario 3: Stock drops to ₹2,420 → Order executes at ₹2,420 (better!) ✓
```

### Example: Sell Limit Order

```
Stock: TCS
Current Price: ₹3,800
Your Buy Price: ₹3,500

You place: Sell Limit Order at ₹4,000

Scenario 1: Stock rises to ₹4,000 → Order executes at ₹4,000 ✓
Scenario 2: Stock stays below ₹4,000 → Order pending
Scenario 3: Stock jumps to ₹4,050 → Executes at ₹4,050 (better!) ✓
```

### Risk of Non-Execution

The main risk: Your order may never execute if price doesn't reach your limit.

---

## Order Type 3: Stop Loss Order

### What It Is

An order that triggers only when stock reaches a **specified trigger price**, then converts to market or limit order.

### Two Variants

| Type | Trigger + Execution |
|------|---------------------|
| **SL-M (Stop Loss Market)** | Triggers at price, executes at market |
| **SL-L (Stop Loss Limit)** | Triggers at price, executes at limit price |

### Example: Stop Loss for Protection

```
You bought: Infosys at ₹1,550
Risk tolerance: 5% loss max

Stop Loss Setup (SL-L):
- Trigger Price: ₹1,475 (when to activate)
- Limit Price: ₹1,470 (execution price)

What happens:
- Stock at ₹1,550 → Order pending
- Stock drops to ₹1,475 → Order triggers
- Order converts to Sell Limit at ₹1,470
- Executes at ₹1,470 or better
```

### SL-M vs SL-L: Which to Choose?

| Feature | SL-M | SL-L |
|---------|------|------|
| **Execution** | Guaranteed | Not guaranteed |
| **Price** | May slip | Controlled |
| **Use case** | Fast-moving stocks | Stable stocks |
| **Risk** | Bad price in crash | Non-execution |

### For Intraday Trading

SL-M is often preferred (execution certainty matters more than exact price).

### For Delivery/Swing

SL-L with buffer (wider limit price) provides balance.

---

## Order Type 4: Bracket Order (BO)

### What It Is

A three-in-one order that automatically places:
1. Entry order (your buy/sell)
2. Stop loss order
3. Target (take profit) order

When either stop loss OR target is hit, the other is cancelled.

### Example

```
Stock: HDFC Bank
View: Bullish

Bracket Order Setup:
- Buy: ₹1,600
- Stop Loss: ₹1,580 (2.5% risk)
- Target: ₹1,650 (3.1% reward)

Outcomes:
A) Stock rises to ₹1,650 → Target hit, sold at profit ₹50
B) Stock falls to ₹1,580 → Stop loss hit, sold at loss ₹20
```

### Advantages

- Enforces discipline
- Automatic risk-reward setup
- No need to monitor constantly

### Disadvantages

- Square off mandatory (intraday/short-term only)
- Can't modify extensively
- May close position prematurely in volatile moves

### How to Calculate

```
Risk-Reward Ratio = Target Distance / Stop Loss Distance

Example:
Entry: ₹1,600
Stop Loss: ₹1,580 → Distance: ₹20
Target: ₹1,650 → Distance: ₹50

Risk-Reward = 50/20 = 1:2.5 ✓ (Good!)
```

---

## Order Type 5: Cover Order (CO)

### What It Is

Similar to bracket order but with **only stop loss** (no target).

### When to Use

- Intraday trading
- When you want trailing stop instead of fixed target
- Manual target management preferred

### Example

```
Cover Order Setup:
- Buy: ₹2,450 (Reliance)
- Stop Loss: ₹2,420

You manually exit when you want to book profit.
Stop loss protects if things go wrong.
```

### Advantage Over Bracket Order

- More flexibility on exit
- Lower margin requirement than regular order

---

## Order Type 6: GTT (Good Till Triggered)

### What It Is

A long-term order that stays active until your trigger price is hit – even across days, weeks, or months.

### Use Cases

1. **Buying at support**: Set GTT buy at lower price
2. **Stop loss for holdings**: Protect long-term investments
3. **Target exits**: Set sell target and forget

### Example: Long-Term Stop Loss

```
You hold: HDFC Bank bought at ₹1,400
Current Price: ₹1,600

GTT Setup:
- Trigger: ₹1,350 (below your buy)
- Order: Sell at ₹1,340

This stays active for 1 year.
If stock crashes to ₹1,350, your shares are sold automatically.
No daily re-entry needed!
```

### Example: Buying on Dip

```
Stock you want: Infosys
Current Price: ₹1,550
Your Target: ₹1,400

GTT Setup:
- Trigger: ₹1,410
- Order: Buy at ₹1,400

You don't need to watch the market daily.
If Infosys drops to ₹1,410, order activates and buys at ₹1,400.
```

### GTT Validity

- Most brokers: 1 year
- Can be cancelled anytime
- Re-check and renew if needed

---

## Order Type 7: AMO (After Market Order)

### What It Is

Orders placed outside market hours (after 3:30 PM or before 9:00 AM).

### When to Use

- Working professionals who can't trade during market hours
- News-based orders (place at night, executes next morning)
- Planning trades in advance

### How It Works

```
8:00 PM: You place AMO to buy Reliance at ₹2,450
9:15 AM (next day): Order enters the system
9:15 AM onwards: Executes if price matches
```

### AMO Order Types

You can place AMO as:
- Market Order
- Limit Order
- Stop Loss Order

### Limitations

- Can be modified/cancelled only before market opens
- Gap risk: Stock may open very different from previous close

---

## Order Validity: Day vs IOC vs GTC

### Day Order

- Valid for current trading day only
- Cancelled automatically at 3:30 PM if not executed

### IOC (Immediate or Cancel)

- Must execute immediately (full or partial)
- Unexecuted portion cancelled instantly

**Use case**: You want 1000 shares but only 500 available at your price. IOC will buy 500, cancel remaining 500.

### GTC (Good Till Cancelled)

- Stays active until manually cancelled or executed
- Note: Indian exchanges don't support pure GTC; GTT serves similar purpose

---

## Order Types Quick Reference

| Order Type | When Price... | Best For |
|------------|---------------|----------|
| **Market** | Any | Urgent entry/exit |
| **Limit** | Reaches your price | Patient entry |
| **SL-M** | Triggers, then market | Fast protection |
| **SL-L** | Triggers, then limit | Controlled protection |
| **Bracket** | Entry + SL + Target | Disciplined trading |
| **Cover** | Entry + SL only | Flexible intraday |
| **GTT** | Long-term trigger | Holdings protection |
| **AMO** | Next day | After-hours planning |

---

## Common Mistakes with Order Types

### Mistake 1: Market Order in Illiquid Stock

```
You: Market order for obscure small-cap
Expected: ₹50
Got: ₹58
Loss: 16% slippage!
```
**Fix**: Always use limit orders for illiquid stocks.

### Mistake 2: Tight Stop Loss Limit

```
Your SL-L:
- Trigger: ₹100
- Limit: ₹99

Stock crashes from ₹105 to ₹95 in seconds.
Your order triggers at ₹100 but doesn't execute (no buyers at ₹99).
You're stuck with the falling stock!
```
**Fix**: Keep reasonable gap between trigger and limit (2-3%).

### Mistake 3: Forgetting to Set Stop Loss

"I'll set it later" → You get busy → Stock crashes → Huge loss.

**Fix**: Always set stop loss IMMEDIATELY after entry.

### Mistake 4: Not Using GTT for Long-Term Holdings

You hold 10 stocks for long-term. No protection. One company has fraud.
Stock crashes 80% before you notice.

**Fix**: Set GTT stop loss for all holdings.

---

## Practical Scenarios

### Scenario 1: Buying Quality Stock on Dip

**Stock**: Reliance (CMP: ₹2,500)
**Your Target**: Buy at ₹2,400

**Action**: Place GTT
- Trigger: ₹2,410
- Buy Limit: ₹2,400

Wait patiently. Either you get good entry or you don't buy.

### Scenario 2: Intraday Breakout Trade

**Stock**: Bank Nifty futures
**Breakout level**: 44,000

**Action**: Bracket Order
- Buy: 44,020 (above breakout)
- Stop Loss: 43,900
- Target: 44,300

Clear risk-reward. Automatic management.

### Scenario 3: Protecting Your Portfolio

**Holdings**: 5 stocks worth ₹10 lakhs each

**Action**: GTT stop loss for each
- Below 15-20% from current price
- Different triggers for different stocks

Sleep peacefully knowing extreme losses are capped.

---

## Broker-Specific Features

| Feature | Zerodha | Groww | Upstox |
|---------|---------|-------|--------|
| Market Order | ✓ | ✓ | ✓ |
| Limit Order | ✓ | ✓ | ✓ |
| SL-M | ✓ | ✓ | ✓ |
| SL-L | ✓ | ✓ | ✓ |
| Bracket Order | ✓ | ✗ | ✓ |
| Cover Order | ✓ | ✗ | ✓ |
| GTT | ✓ | ✓ | ✓ |
| AMO | ✓ | ✓ | ✓ |

---

## Risk Disclaimer

Order types are tools that help execute your trading strategy but don't guarantee profits. Stop loss orders may not execute at your specified price during extreme volatility or gaps. Markets can move against you rapidly. This guide is educational only. Practice with small amounts first.

---

## Summary

| Your Need | Order Type |
|-----------|------------|
| Buy NOW | Market |
| Buy at MY price | Limit |
| Protect position | Stop Loss |
| Automatic targets | Bracket |
| Long-term protection | GTT |
| Plan after hours | AMO |

Master these order types, and you'll trade with precision instead of guessing.

---

## Social Media Posts

**LinkedIn**:
"Lost ₹5,000 on slippage because I used market order on an illiquid stock. Lesson: Know your order types. Market order = immediate but unpredictable. Limit order = your price or nothing. For illiquid stocks: ALWAYS limit order. #TradingTips #StockMarket"

**Twitter/X**:
"Order Types 101:
🟢 Market: Execute NOW (any price)
🟡 Limit: Execute at MY price only
🔴 Stop Loss: Execute when price triggers
🎯 Bracket: Entry + SL + Target
⏰ GTT: Long-term trigger

Wrong order type = avoidable losses. #Trading"

**Instagram**:
"Me before learning order types: 'Why did I pay ₹655 when stock was ₹650?'

That's called slippage. Market orders in illiquid stocks = paying more than you should.

Solution: Limit Orders! ✅

You set the price. Either you get it, or you don't buy.

Save this! 📌 #OrderTypes #TradingForBeginners"
