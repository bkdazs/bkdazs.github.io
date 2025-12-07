---
title: "Order Types and Trading Mechanisms Explained"
date: 2025-01-15
weight: 15
description: "Complete guide to order types and trading mechanisms - limit orders, market orders, stop loss, bracket orders, order matching, price discovery, and trading sessions."
tags: ["order types", "limit order", "market order", "stop loss", "trading mechanism"]
series: ["Financial Markets"]
series_order: 15
seo_title: "Order Types Guide | Trading Mechanisms Explained"
seo_description: "Complete guide to order types - limit, market, stop loss, bracket orders. Understand order matching, price discovery, and trading sessions in Indian markets."
keywords: ["order types", "limit order", "market order", "stop loss", "trading mechanism"]
ShowToc: true
TocOpen: true
---

## Introduction: Speaking the Market's Language

"Knowing the right order type is like having the right tool for the job—it can mean the difference between a successful trade and a missed opportunity."

Order types are the fundamental building blocks of trading. Each type serves a specific purpose, from ensuring execution to limiting losses. Understanding these tools helps you trade more effectively and manage risk better.

---

## Basic Order Types

### Market Order

**Definition:** Buy or sell at the best available price immediately

**Characteristics:**
| Aspect | Detail |
|--------|--------|
| Execution | Guaranteed (if liquidity) |
| Price | Not guaranteed |
| Speed | Immediate |
| Use case | When execution matters more than price |

**Example:**
- Current price: ₹100
- You place market buy order
- Executed at ₹100.10 (best available)

**Pros:**
- Instant execution
- Simple to use
- Fills completely in liquid stocks

**Cons:**
- Slippage possible
- Poor in illiquid stocks
- No price control

### Limit Order

**Definition:** Buy or sell only at a specified price or better

**Characteristics:**
| Aspect | Detail |
|--------|--------|
| Execution | Not guaranteed |
| Price | Guaranteed (or better) |
| Speed | Depends on market |
| Use case | When price matters more than execution |

**Buy Limit:** Execute at limit price or LOWER
**Sell Limit:** Execute at limit price or HIGHER

**Example:**
- Stock at ₹100
- Buy limit at ₹98
- Executes only if price falls to ₹98 or below

**Pros:**
- Price certainty
- No slippage
- Strategic entry/exit

**Cons:**
- May not execute
- Partial fills possible
- Misses opportunities if price moves away

---

## Stop Loss Orders

### Stop Loss (SL) Order

**Definition:** Becomes a market order when trigger price is reached

**Purpose:** Limit losses on existing position

**How It Works:**
1. You hold stock bought at ₹100
2. Set stop loss at ₹95
3. If price falls to ₹95, SL triggers
4. Becomes market order, executes at best price

### Stop Loss Limit (SL-L) Order

**Definition:** Becomes a limit order when trigger price is reached

**Two Prices:**
- Trigger price: When order activates
- Limit price: Maximum/minimum execution price

**Example - Stop Loss Sell:**
| Parameter | Value |
|-----------|-------|
| Holding price | ₹100 |
| Trigger price | ₹95 |
| Limit price | ₹94 |

**What Happens:**
- If price hits ₹95, order activates
- Sells at ₹94 or better
- Won't sell below ₹94

### Stop Loss vs Stop Loss Limit

| Aspect | SL (Market) | SL-L (Limit) |
|--------|------------|--------------|
| Execution | Guaranteed (usually) | Not guaranteed |
| Price | May slip | Controlled |
| Gap risk | Can execute at bad price | May not execute |
| Best for | Liquid stocks | Illiquid/volatile |

---

## Advanced Order Types

### Bracket Order (BO)

**Definition:** Main order + Target order + Stop loss order as a package

**Components:**
1. Entry order (buy/sell)
2. Target (profit booking)
3. Stop loss (loss limiting)

**Example - Buy Bracket:**
| Component | Price |
|-----------|-------|
| Buy order | ₹100 |
| Target | ₹105 (+5%) |
| Stop loss | ₹97 (-3%) |

**Behavior:**
- One of target or SL will execute
- Other cancels automatically
- Position closed either way

**Benefits:**
- Automated exit
- Defined risk-reward
- Discipline enforced

### Cover Order (CO)

**Definition:** Market/limit order with compulsory stop loss

**Characteristics:**
- Lower margin than regular order
- Stop loss mandatory
- Intraday only
- Position auto-squared off

### After Market Order (AMO)

**Definition:** Orders placed after market hours for next day

**Timing:**
| Broker | AMO Window |
|--------|------------|
| Typical | 4:00 PM to 9:00 AM |
| Execution | At market open |

**Use Case:**
- Can't trade during market hours
- React to overnight news
- Pre-plan trades

### Good Till Cancelled (GTC)

**Definition:** Order remains active until executed or cancelled

**Typical Validity:**
- Up to 365 days
- Some brokers limit to 90 days

**Use Case:**
- Long-term entry points
- Patient buying at specific levels

### Good Till Date (GTD)

**Definition:** Order valid until specified date

**Use Case:**
- Event-specific trading
- Time-bound strategies

---

## Order Validity

### Day Order

**Definition:** Valid only for current trading day

**Default:** Most orders are day orders

**Behavior:**
- Cancelled at market close if not executed
- Most common type

### IOC (Immediate or Cancel)

**Definition:** Execute immediately (full or partial) or cancel

**Behavior:**
- Whatever fills, fills
- Remaining cancelled instantly
- No waiting in order book

**Use Case:**
- Large orders in liquid markets
- When quick execution needed

### FOK (Fill or Kill)

**Definition:** Execute full quantity immediately or cancel entire order

**Behavior:**
- All or nothing
- No partial fills
- Cancelled if full quantity unavailable

**Use Case:**
- When partial fills not acceptable
- Large institutional orders

---

## Order Matching Mechanism

### Price-Time Priority

**Primary Rule:** Best price gets priority
**Secondary Rule:** Earlier order at same price gets priority

**Example - Buy Orders:**
| Order | Price | Time | Priority |
|-------|-------|------|----------|
| A | ₹101 | 9:20 | 1st |
| B | ₹100 | 9:15 | 2nd |
| C | ₹100 | 9:17 | 3rd |

Order A matches first (highest buy price)
Then B before C (same price, earlier time)

### Order Book

**Bid Side (Buy Orders):**
| Price | Quantity |
|-------|----------|
| ₹100.00 | 5,000 |
| ₹99.95 | 3,000 |
| ₹99.90 | 7,000 |

**Ask Side (Sell Orders):**
| Price | Quantity |
|-------|----------|
| ₹100.05 | 2,000 |
| ₹100.10 | 4,000 |
| ₹100.15 | 6,000 |

### Best Bid and Offer (BBO)

**Best Bid:** Highest buy price (₹100.00)
**Best Offer:** Lowest sell price (₹100.05)
**Spread:** ₹0.05 (difference)

### Trade Execution

**Matching Condition:**
- Buy price ≥ Sell price

**Example:**
- Best bid: ₹100
- Best ask: ₹100.05
- New market buy comes in
- Matches with ₹100.05 sell
- Trade at ₹100.05

---

## Trading Sessions

### Pre-Open Session (9:00 - 9:15 AM)

**Purpose:** Price discovery before regular trading

**Phases:**
| Time | Phase |
|------|-------|
| 9:00 - 9:08 | Order entry/modification |
| 9:08 - 9:12 | Order matching |
| 9:12 - 9:15 | Buffer for transition |

**Opening Price Calculation:**
- Equilibrium price where maximum volume trades
- All orders at or better than opening price execute

### Normal Trading (9:15 AM - 3:30 PM)

**Continuous Matching:**
- Orders matched throughout
- Price-time priority
- Real-time execution

### Closing Session (3:40 - 4:00 PM)

**Purpose:** Determine closing price

**Mechanism:**
- Volume-weighted average of last 30 minutes
- Or call auction if eligible

---

## Price Bands and Circuit Breakers

### Stock-Specific Circuits

| Category | Circuit Limit |
|----------|--------------|
| High volatility stocks | ±2%, ±5% |
| Normal stocks | ±10% |
| Some stocks | ±20% |
| F&O stocks | No circuit |

**When Hit:**
- Trading continues but only at circuit price
- Prevents further move beyond circuit

### Index-Wide Circuit Breakers

| Trigger | Before 1 PM | 1-2:30 PM | After 2:30 PM |
|---------|-------------|-----------|---------------|
| 10% | 45 min halt | 15 min halt | No halt |
| 15% | 1:45 halt | 45 min halt | Rest of day |
| 20% | Rest of day | Rest of day | Rest of day |

### Price Bands vs Circuit Breakers

| Aspect | Price Band | Circuit Breaker |
|--------|-----------|-----------------|
| Scope | Individual stock | Entire index |
| Purpose | Prevent manipulation | Systemic risk |
| Action | Limit price movement | Halt trading |

---

## Order Modification and Cancellation

### Modifying Orders

**What Can Be Modified:**
- Price
- Quantity (reduce only usually)
- Order type

**What Can't Be Modified:**
- Buy/sell direction
- Stock symbol

**Impact:**
- May lose time priority

### Cancelling Orders

**When Possible:**
- Before execution
- Before market close (day orders)

**Partial Execution:**
- Remaining quantity can be cancelled
- Executed portion stands

---

## Practical Trading Tips

### Choosing Right Order Type

| Situation | Recommended Order |
|-----------|-------------------|
| Liquid stock, quick entry | Market order |
| Specific entry price wanted | Limit order |
| Protecting existing position | Stop loss |
| Intraday with defined risk | Bracket/Cover order |
| Not available during hours | AMO |
| Large order in illiquid stock | Limit with patience |

### Avoiding Common Mistakes

| Mistake | Solution |
|---------|----------|
| Market order in illiquid stock | Use limit order |
| SL-L too tight in volatile stock | Give buffer or use SL-M |
| Forgetting to cancel GTC | Review pending orders regularly |
| Not using stop loss | Always define risk |

### Slippage Management

**What is Slippage:**
Difference between expected and actual execution price

**Minimize By:**
- Using limit orders
- Trading liquid stocks
- Avoiding market orders at open/close
- Breaking large orders

---

## Order Types by Broker

### Common Availability

| Order Type | Zerodha | Groww | Upstox |
|------------|---------|-------|--------|
| Market | ✓ | ✓ | ✓ |
| Limit | ✓ | ✓ | ✓ |
| SL | ✓ | ✓ | ✓ |
| SL-L | ✓ | ✓ | ✓ |
| BO | ✓ | ✗ | ✓ |
| CO | ✓ | ✗ | ✓ |
| AMO | ✓ | ✓ | ✓ |
| GTT | ✓ | ✓ | ✓ |

*Note: Features vary; check with your broker*

---

## Key Takeaways

1. **Market order** – Guaranteed execution, price may slip
2. **Limit order** – Price guaranteed, execution not
3. **Stop loss** – Essential for risk management
4. **Bracket order** – Complete trade management
5. **Price-time priority** – How orders match
6. **Pre-open session** – Determines opening price
7. **Circuit breakers** – Protect against extreme moves

---

## Disclaimer

*This article is for educational purposes only. Trading involves significant risk of loss. Order placement is trader's responsibility. This is not investment advice.*

---

## Frequently Asked Questions

**Q: Should I use market or limit order?**
A: For liquid stocks where quick execution matters, market order works. For less liquid stocks or when you want a specific price, use limit. When in doubt, use limit.

**Q: Why did my stop loss not execute at my exact price?**
A: If you used SL (market), it executes at best available after trigger—may be worse in gaps/volatility. Use SL-L for price control, but risk non-execution.

**Q: Can I place orders before market opens?**
A: Yes, through pre-open session (9-9:08) or AMO (after market hours for next day). Pre-open orders participate in opening price discovery.

**Q: What happens to my pending order at market close?**
A: Day orders get cancelled. GTC/GTD orders remain for next session. Check your pending orders regularly.

**Q: Why does my order show "pending" for long time?**
A: Limit order at price away from current market won't execute until market reaches your price. You can modify price or cancel if needed.

*Mastering order types is like learning a new language—once fluent, you can express exactly what you want the market to do, and manage your risk with precision.*
