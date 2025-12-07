---
title: "Repo and Reverse Repo: RBI's Liquidity Tools"
date: 2025-01-15
weight: 29
description: "Understanding Repo and Reverse Repo operations - how RBI manages liquidity, the LAF framework, policy rate transmission, and impact on interest rates."
tags: ["repo rate", "reverse repo", "RBI", "liquidity", "monetary policy"]
series: ["Financial Markets"]
series_order: 29
seo_title: "Repo Reverse Repo Guide | RBI Liquidity Operations"
seo_description: "Complete guide to Repo and Reverse Repo - how RBI manages liquidity, LAF framework, policy rate transmission, and their impact on interest rates."
keywords: ["repo rate", "reverse repo", "RBI", "LAF", "liquidity management"]
ShowToc: true
TocOpen: true
---

## Introduction: The Plumbing of Monetary Policy

"When the RBI changes the repo rate, it makes headlines. But understanding how repo actually works reveals the elegant mechanism through which monetary policy reaches the real economy."

Repo and reverse repo are the primary tools through which the Reserve Bank of India manages liquidity in the banking system. These operations determine short-term interest rates and form the foundation of monetary policy transmission.

---

## What is Repo?

### Definition

**Repo (Repurchase Agreement):** A transaction where one party sells securities to another with an agreement to repurchase at a predetermined price on a future date.

**In RBI Context:** Banks sell government securities to RBI and get cash, agreeing to buy back at repo rate.

### How Repo Works

| Step | Action |
|------|--------|
| 1 | Bank needs cash (liquidity) |
| 2 | Bank sells G-secs to RBI |
| 3 | RBI gives cash to bank |
| 4 | At maturity, bank buys back G-secs |
| 5 | Bank pays original amount + interest (repo rate) |

### Example

| Parameter | Value |
|-----------|-------|
| G-secs sold | ₹100 crore |
| Repo rate | 6.5% |
| Tenor | Overnight |
| Interest | ₹100 cr × 6.5% × (1/365) = ₹1.78 lakh |
| Repurchase amount | ₹100.0178 crore |

### Why Banks Use Repo

| Reason | Benefit |
|--------|---------|
| Short-term liquidity | Meet daily obligations |
| CRR shortfall | Cover reserve requirements |
| Payment settlement | Clear large payments |
| Better rate | Cheaper than unsecured borrowing |

---

## What is Reverse Repo?

### Definition

**Reverse Repo:** Banks lend money to RBI by buying securities with agreement that RBI will repurchase.

**In RBI Context:** Banks park excess cash with RBI, earning interest (reverse repo rate).

### How Reverse Repo Works

| Step | Action |
|------|--------|
| 1 | Bank has excess cash |
| 2 | Bank buys G-secs from RBI |
| 3 | RBI takes cash from bank |
| 4 | At maturity, RBI buys back G-secs |
| 5 | Bank receives original amount + interest |

### Why Banks Use Reverse Repo

| Reason | Benefit |
|--------|---------|
| Park surplus funds | Earn safe return |
| No credit risk | RBI counterparty |
| Liquidity | Overnight access |
| Capital efficiency | Better than idle cash |

---

## Standing Deposit Facility (SDF)

### Recent Addition (April 2022)

**Replaced Reverse Repo as Floor:**

| Feature | Details |
|---------|---------|
| Purpose | Absorb excess liquidity |
| Collateral | No collateral needed |
| Rate | SDF rate = Repo - 25 bps |
| Introduced | April 2022 |

### Why SDF Introduced

| Reason | Explanation |
|--------|-------------|
| No G-sec constraint | RBI doesn't need to sell G-secs |
| Simpler | No collateral management |
| Flexibility | Can absorb large liquidity |

---

## Marginal Standing Facility (MSF)

### Definition

Emergency overnight borrowing window for banks at MSF rate.

### Features

| Feature | Details |
|---------|---------|
| Rate | Repo rate + 25 bps |
| Collateral | G-secs (including SLR) |
| Dip into SLR | Can use 2% of SLR |
| Purpose | Emergency liquidity |

### When Used

| Situation | Action |
|-----------|--------|
| Tight liquidity | Banks can't get funds elsewhere |
| CRR pressure | Need to meet reserve |
| Penalty avoidance | Cheaper than CRR penalty |

---

## LAF Corridor

### Interest Rate Corridor

**The Framework:**

| Rate | Function |
|------|----------|
| MSF Rate | Ceiling (upper bound) |
| Repo Rate | Policy rate (middle) |
| SDF Rate | Floor (lower bound) |

**Corridor Width:** 50 bps (±25 bps from repo)

### How Corridor Works

| Overnight Rate | What Happens |
|----------------|--------------|
| Above MSF | Banks borrow from MSF (ceiling) |
| Between MSF-Repo | LAF repo borrowing |
| At Repo | Normal operations |
| Between Repo-SDF | Some excess liquidity |
| At SDF | Excess parked with RBI (floor) |

### Corridor Diagram

```
MSF Rate (6.75%) -------- Ceiling
      |
      |    
Repo Rate (6.50%) ------- Policy Rate
      |
      |
SDF Rate (6.25%) -------- Floor
```

---

## RBI's Liquidity Operations

### Liquidity Adjustment Facility (LAF)

**Daily Operations:**
| Operation | Purpose |
|-----------|---------|
| Repo | Inject liquidity |
| Reverse Repo/SDF | Absorb liquidity |

**Timing:** Daily at 10:00 AM and 2:30 PM

### Variable Rate Operations

**VRR (Variable Rate Repo):**
- Longer tenor (14 days, 28 days)
- Auction-based rate
- Manage durable liquidity

**VRRR (Variable Rate Reverse Repo):**
- Absorb excess liquidity
- Auction-based rate

### Open Market Operations (OMO)

| Type | Purpose |
|------|---------|
| OMO Purchase | Inject durable liquidity |
| OMO Sale | Absorb durable liquidity |

**Difference from LAF:** OMO = permanent; LAF = temporary

---

## Liquidity Conditions

### System Liquidity

**Net Liquidity = Total deposits with RBI - Total borrowing from RBI**

| Condition | Indicator |
|-----------|-----------|
| Surplus | More deposits than borrowings |
| Deficit | More borrowings than deposits |
| Neutral | Balanced |

### Tracking Liquidity

| Source | Data |
|--------|------|
| RBI press release | Daily LAF data |
| Money market rates | Call rate vs repo |
| WACR | Weighted average call rate |

### Impact of Liquidity

| Liquidity | Market Rates | Impact |
|-----------|--------------|--------|
| Surplus | Below repo | Easy conditions |
| Deficit | Above repo | Tight conditions |
| Extreme surplus | Near SDF | Very easy |
| Extreme deficit | Near MSF | Very tight |

---

## Repo Rate and Monetary Policy

### Policy Rate

**Repo rate is THE policy rate:**
- Announced by MPC every 2 months
- Signals monetary policy stance
- Benchmark for economy-wide rates

### Transmission Mechanism

| Stage | What Happens |
|-------|--------------|
| 1 | RBI changes repo rate |
| 2 | Bank borrowing costs change |
| 3 | Banks adjust deposit/lending rates |
| 4 | Borrowers face new rates |
| 5 | Consumption/investment affected |
| 6 | Inflation/growth impacted |

### Rate Linkage

| Rate | Links To |
|------|----------|
| Repo rate | MCLR, EBR |
| Call money | Tracks near repo |
| T-Bill yields | Influenced by repo |
| Corporate borrowing | Based on repo |

---

## Repo in Non-RBI Context

### Inter-Bank Repo

**Banks with Banks:**
- Banks lend to each other
- Using G-secs as collateral
- CBLO market (now TREPS)

### Corporate Repo

**Tri-Party Repo (TREPS):**
| Feature | Details |
|---------|---------|
| Participants | Banks, MFs, corporates |
| Collateral | G-secs |
| Clearing | CCIL guaranteed |
| Rates | Market-determined |

### Repo in Liquid Funds

**Reverse Repo/TREPS Exposure:**
- Safe component of liquid funds
- Near risk-free
- Lower yield, high safety

---

## Current Framework

### Rate Structure (Example)

| Rate | Level |
|------|-------|
| MSF | 6.75% |
| Repo | 6.50% |
| SDF | 6.25% |

### Recent Changes

| Period | Key Changes |
|--------|-------------|
| COVID period | Large surplus, cut rates |
| 2022 onward | Surplus reduction, rate hikes |
| Current | Inflation management focus |

---

## Practical Implications

### For Borrowers

| Repo Change | Impact |
|-------------|--------|
| Rate cut | Loan rates may fall |
| Rate hike | Loan rates may rise |
| Lag | Takes 1-3 months to transmit |

### For Depositors

| Repo Change | Impact |
|-------------|--------|
| Rate cut | FD rates may fall |
| Rate hike | FD rates may rise |
| New FDs | Consider tenor |

### For Investors

| Indicator | What It Shows |
|-----------|---------------|
| Repo rate | Direction of rates |
| Liquidity | Near-term rate pressure |
| RBI stance | Future rate path |

---

## Key Takeaways

1. **Repo = borrowing** – Banks borrow from RBI against G-secs
2. **Reverse repo = lending** – Banks park money with RBI
3. **SDF is floor** – Lower bound for overnight rates
4. **MSF is ceiling** – Upper bound for overnight rates
5. **Corridor framework** – Keeps rates within band
6. **Policy transmission** – Repo rate affects all rates
7. **Watch liquidity** – Determines actual market rates

---

## Disclaimer

*This article is for educational purposes only. Monetary policy is complex and its effects uncertain. This is not financial advice.*

---

## Frequently Asked Questions

**Q: Why is repo rate important for me?**
A: Repo rate influences your loan EMIs (especially floating rate) and FD returns. When repo rises, EMIs may increase and FD rates improve.

**Q: What's the difference between repo and MSF?**
A: Both involve borrowing from RBI against G-secs. Repo is normal facility; MSF is emergency at 25 bps higher. Banks use MSF when desperate.

**Q: Why did RBI introduce SDF?**
A: With large liquidity surplus, RBI was running out of G-secs for reverse repo. SDF absorbs liquidity without needing collateral.

**Q: How often does RBI change repo rate?**
A: MPC meets 6 times a year (every 2 months). Rate changes happen during these meetings, though not every meeting results in a change.

**Q: Where can I track RBI's liquidity operations?**
A: RBI website publishes daily Money Market Operations data. Shows LAF volumes, liquidity position, and rates.

*Understanding repo and reverse repo is like understanding the engine of monetary policy—these operations determine the price of money in the economy and affect every financial decision from home loans to corporate bonds.*
