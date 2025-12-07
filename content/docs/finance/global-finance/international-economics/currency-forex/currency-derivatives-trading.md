---
title: "Currency Derivatives Trading on NSE and BSE"
date: 2025-01-15
weight: 17
description: "Complete guide to currency derivatives trading - USD/INR futures and options on NSE and BSE, contract specifications, trading strategies, and margin requirements."
tags: ["currency derivatives", "USD INR futures", "forex trading", "currency options", "NSE currency"]
series: ["Financial Markets"]
series_order: 17
seo_title: "Currency Derivatives Trading | USD INR Futures Options"
seo_description: "Complete guide to currency derivatives on NSE and BSE - USD/INR futures and options, contract specs, trading strategies, margins, and how to start trading."
keywords: ["currency derivatives", "USD INR futures", "currency options", "forex trading India", "NSE currency"]
ShowToc: true
TocOpen: true
---

## Introduction: Legal Forex Trading in India

"Currency derivatives on Indian exchanges offer a regulated way to trade forex, hedge currency risk, or speculate on rupee movements—all within the legal framework."

While offshore forex trading is restricted, Indian investors can legally trade currency derivatives on NSE and BSE. These instruments allow hedging and speculation on exchange rate movements with lower capital requirements due to leverage.

---

## What Are Currency Derivatives?

### Definition

Financial contracts whose value is derived from exchange rates between currencies.

### Types Available in India

| Type | Description |
|------|-------------|
| Currency Futures | Standardized contracts to buy/sell currency at future date |
| Currency Options | Right (not obligation) to buy/sell currency |

### Currency Pairs Available

| Pair | Exchange |
|------|----------|
| USD/INR | NSE, BSE, MCX-SX |
| EUR/INR | NSE, BSE |
| GBP/INR | NSE, BSE |
| JPY/INR | NSE, BSE |
| EUR/USD | NSE (cross currency) |
| GBP/USD | NSE (cross currency) |
| USD/JPY | NSE (cross currency) |

---

## USD/INR Futures

### Contract Specifications (NSE)

| Parameter | Specification |
|-----------|---------------|
| Lot Size | $1,000 |
| Tick Size | 0.25 paise (₹0.0025) |
| Quote | ₹ per USD |
| Trading Hours | 9:00 AM - 5:00 PM |
| Contract Months | 12 monthly contracts |
| Expiry | Two working days before last business day |
| Settlement | Cash settled (RBI reference rate) |

### Contract Value Calculation

**Formula:**
$$Contract\ Value = Lot\ Size \times Exchange\ Rate$$

**Example:**
- USD/INR rate: 83.2500
- Lot size: $1,000
- Contract value: 1,000 × 83.25 = ₹83,250

### Margin Requirements

| Margin Type | Approximate |
|-------------|-------------|
| Initial Margin | 2-3% |
| Exposure Margin | 1% |
| Total | ~3-4% |

**Example:**
- Contract value: ₹83,250
- Margin @ 3%: ₹2,498

**Leverage:** ~25-30 times

### Profit/Loss Calculation

**Formula:**
$$P\&L = (Exit\ Rate - Entry\ Rate) \times Lot\ Size \times Number\ of\ Lots$$

**Long Position Example:**
| Item | Value |
|------|-------|
| Entry rate | 83.2500 |
| Exit rate | 83.5000 |
| Move | +0.2500 |
| Lots | 1 |
| P&L | 0.25 × 1,000 = ₹250 profit |

**Short Position Example:**
| Item | Value |
|------|-------|
| Entry rate | 83.5000 |
| Exit rate | 83.2500 |
| Move | -0.2500 |
| Lots | 1 |
| P&L | 0.25 × 1,000 = ₹250 profit |

---

## USD/INR Options

### Contract Specifications

| Parameter | Specification |
|-----------|---------------|
| Lot Size | $1,000 |
| Premium Quote | ₹ per USD |
| Strike Interval | ₹0.25 |
| Exercise Style | European |
| Settlement | Cash settled |

### Option Types

| Type | Right | Obligation |
|------|-------|------------|
| Call | Buy USD | None |
| Put | Sell USD | None |

### Premium Calculation

**Premium per lot:**
$$Premium = Option\ Price \times Lot\ Size$$

**Example:**
- Call option price: ₹0.50 per USD
- Lot size: $1,000
- Premium: 0.50 × 1,000 = ₹500

### Intrinsic and Time Value

**Call Option:**
$$Intrinsic = Max(0, Spot - Strike)$$

**Put Option:**
$$Intrinsic = Max(0, Strike - Spot)$$

**Time Value:**
$$Time\ Value = Premium - Intrinsic\ Value$$

---

## Cross Currency Derivatives

### What Are Cross Currency Pairs?

Pairs that don't involve INR—trade purely between foreign currencies.

### Available Pairs on NSE

| Pair | Lot Size |
|------|----------|
| EUR/USD | €1,000 |
| GBP/USD | £1,000 |
| USD/JPY | $1,000 |

### Settlement

- Cash settled in INR
- P&L converted at RBI reference rate

### Use Cases

| Scenario | Cross Currency Use |
|----------|-------------------|
| Export to Europe, pay in USD | Hedge EUR/USD |
| US subsidiary with Yen costs | Hedge USD/JPY |
| Diversified currency exposure | Multiple pair hedging |

---

## Trading Strategies

### Hedging Strategies

**Importer Hedge (Long Futures):**
| Scenario | Action |
|----------|--------|
| Need to pay $100,000 in 3 months | Buy 100 lots USD/INR futures |
| If USD rises | Futures profit offsets higher payment |
| If USD falls | Futures loss, but cheaper payment |

**Exporter Hedge (Short Futures):**
| Scenario | Action |
|----------|--------|
| Will receive $50,000 in 2 months | Sell 50 lots USD/INR futures |
| If USD falls | Futures profit offsets lower receipt |
| If USD rises | Futures loss, but higher receipt |

### Speculative Strategies

**Directional View - Bullish USD:**
| Strategy | Instrument |
|----------|------------|
| Buy futures | Unlimited profit potential |
| Buy calls | Limited risk (premium) |
| Sell puts | Premium income, risk if USD falls |

**Directional View - Bearish USD:**
| Strategy | Instrument |
|----------|------------|
| Sell futures | Unlimited profit potential |
| Buy puts | Limited risk (premium) |
| Sell calls | Premium income, risk if USD rises |

### Option Strategies

**Long Straddle (Expect Volatility):**
- Buy ATM call + Buy ATM put
- Profit if big move either way
- Loss limited to premium paid

**Covered Call (Hold Long, Earn Premium):**
- Long futures + Sell OTM call
- Earn premium
- Cap upside but reduce cost

**Protective Put (Hedge Long Position):**
- Long futures + Buy put
- Protection below put strike
- Unlimited upside

---

## Margin and Settlement

### Margin Types

| Margin | Purpose |
|--------|---------|
| Initial Margin (SPAN) | Cover potential loss |
| Exposure Margin | Additional buffer |
| MTM Margin | Daily profit/loss |
| Premium Margin | For option buyers |

### Daily Mark-to-Market

**Example:**
| Day | Settlement Price | MTM |
|-----|-----------------|-----|
| Day 1 (Buy) | 83.25 | - |
| Day 2 | 83.40 | +₹150 credit |
| Day 3 | 83.30 | -₹100 debit |
| Day 4 | 83.50 | +₹200 credit |

### Final Settlement

- On expiry day
- Against RBI reference rate
- Cash settled (no physical delivery)

---

## Who Can Trade?

### Participant Categories

| Category | Purpose | Documentation |
|----------|---------|---------------|
| Hedgers | Genuine exposure | Underlying exposure proof |
| Speculators | Trading | Regular KYC |
| Arbitrageurs | Price differences | Regular KYC |

### Position Limits

| Category | Limit (USD/INR) |
|----------|----------------|
| Client (Speculative) | $100 million or 15% of OI |
| Hedger | Underlying exposure |
| Trading Member | $500 million |

### Documentation for Hedgers

**Required:**
- Proof of underlying exposure
- Import/export contracts
- Loan agreements (for FCY loans)
- Declaration forms

---

## Comparison: OTC vs Exchange

| Aspect | OTC Forex | Exchange Derivatives |
|--------|----------|---------------------|
| Counterparty | Bank | Clearing corporation |
| Customization | High | Standardized |
| Transparency | Low | High |
| Margin | Higher | Lower |
| Settlement Risk | Higher | Lower (guaranteed) |
| Documentation | Extensive | Standard KYC |

### When to Use Exchange

| Situation | Exchange Preferred |
|-----------|-------------------|
| Small hedges | Lower minimum size |
| Speculative trading | Easier access |
| No banking relationship | Open through broker |
| Price transparency | Exchange quotes |

### When to Use OTC

| Situation | OTC Preferred |
|-----------|--------------|
| Large hedge | Customized size |
| Exact date needed | Flexible expiry |
| Complex structure | Tailored products |
| Relationship pricing | Bank offers better rates |

---

## How to Start Trading

### Step 1: Open Currency Trading Account

**With Broker:**
- Equity broker offering currency segment
- Separate segment activation may be needed
- Currency derivative KYC

### Step 2: Fund Account

- Transfer margin money
- Maintain adequate balance for MTM

### Step 3: Understand Basics

- Contract specifications
- Margin requirements
- Settlement process

### Step 4: Start Small

- Begin with 1-2 lots
- Understand P&L dynamics
- Practice with paper trades first

### Step 5: Risk Management

- Define stop losses
- Never over-leverage
- Understand worst-case scenarios

---

## Taxation

### Tax Treatment

| Type | Classification |
|------|----------------|
| Futures P&L | Speculative business income |
| Options P&L | Non-speculative business income |

**Note:** Option taxation classification may vary; consult CA

### ITR Filing

- Report as business income
- ITR-3 for individual traders
- Maintain trading records

### STT

**Securities Transaction Tax:**
- 0.0001% on sell side (futures)
- 0.05% on sell side (options premium)
- 0.125% on option exercise

---

## Key Risks

### Market Risk

| Risk | Description |
|------|-------------|
| Adverse movement | Currency moves against position |
| Gap risk | Opening at different level |
| Volatility spike | Sudden large moves |

### Leverage Risk

**Example:**
- Margin: ₹2,500
- Contract value: ₹83,000
- 1% adverse move = ₹830 loss = 33% of margin

### Liquidity Risk

| Pair | Liquidity |
|------|-----------|
| USD/INR | Very high |
| EUR/INR | Moderate |
| GBP/INR | Moderate |
| JPY/INR | Low |
| Cross pairs | Lower |

---

## Key Takeaways

1. **Legal trading** – Exchange currency derivatives are legal
2. **USD/INR most liquid** – Focus here initially
3. **Cash settled** – No physical currency delivery
4. **Leverage ~25x** – Amplifies gains and losses
5. **Hedging tool** – Useful for importers/exporters
6. **Daily MTM** – Monitor positions daily
7. **Risk management** – Essential due to leverage

---

## Disclaimer

*This article is for educational purposes only. Currency derivatives trading involves significant risk due to leverage. You can lose more than your initial margin. Only trade with capital you can afford to lose. This is not trading advice.*

---

## Frequently Asked Questions

**Q: Is currency trading on NSE different from forex?**
A: Yes. NSE currency trading is exchange-traded, regulated, rupee-settled derivatives. Offshore forex trading is OTC, typically unregulated for Indian residents, and not legal for speculation.

**Q: Can I trade USD/INR with ₹10,000?**
A: Yes, with margin of ~3%, you can trade 3-4 lots. But be aware of leverage risk—small moves can cause significant losses.

**Q: What happens on expiry if I don't square off?**
A: Position is cash-settled at RBI reference rate. No action needed, automatic settlement occurs.

**Q: Can I hedge without underlying exposure?**
A: Yes, for speculative purposes within position limits. No documentation needed for speculative trading within limits.

**Q: Why is USD/INR futures price different from spot?**
A: Futures price reflects forward rate (interest rate differential). Usually futures > spot (premium) due to higher India interest rates.

*Currency derivatives democratize forex access for Indian traders and businesses, offering a regulated platform to manage currency risk or profit from exchange rate movements.*
