---
title: "Inventory Valuation Methods: FIFO, Weighted Average, and Specific Identification"
date: 2025-12-06
weight: 11
description: "Complete guide to inventory valuation methods in India. Learn FIFO, Weighted Average, and Specific Identification with practical examples and their impact on financial statements."
keywords: ["inventory valuation India", "FIFO method", "weighted average method", "inventory accounting", "cost of goods sold"]
tags:
  - inventory
  - FIFO
  - weighted average
  - cost accounting
  - inventory valuation
series:
  - "Accounting Fundamentals"
cover:
  image: ""
  alt: "Inventory Valuation Methods India"
  caption: "Same inventory, different values"
  relative: false
ShowToc: true
TocOpen: true
---

## Ramesh's Inventory Puzzle

Ramesh runs an electronics store. In March, he bought:
- 100 phones @ ₹10,000 each on March 5
- 100 phones @ ₹12,000 each on March 20

By March 31, he sold 120 phones at ₹15,000 each.

His accountant asked: "Which phones did you sell—the ₹10,000 ones or the ₹12,000 ones?"

Ramesh was puzzled. "They're all the same model. Does it matter?"

**It matters a lot.**

If he sold the ₹10,000 phones: Cost = ₹12 lakh, Profit = ₹6 lakh
If he sold the ₹12,000 phones: Cost = ₹14.4 lakh, Profit = ₹3.6 lakh

**Same sales, different profits. That's the power of inventory valuation.**

---

## Why Inventory Valuation Matters

### Impact on Financial Statements

| Higher Inventory Value | Lower Inventory Value |
|------------------------|----------------------|
| Lower COGS | Higher COGS |
| Higher Gross Profit | Lower Gross Profit |
| Higher Net Profit | Lower Net Profit |
| Higher Tax | Lower Tax |
| Higher Assets | Lower Assets |

### The Basic Formula

```
Opening Inventory + Purchases - Closing Inventory = Cost of Goods Sold

Therefore:
Higher Closing Inventory = Lower COGS = Higher Profit
Lower Closing Inventory = Higher COGS = Lower Profit
```

---

## Inventory Valuation Principle

### Lower of Cost or Net Realizable Value (NRV)

**As per Ind AS 2 / AS 2**:

```
Inventory Value = Lower of (Cost, NRV)
```

| Term | Definition |
|------|------------|
| **Cost** | Purchase price + Conversion costs + Other costs to bring to current location |
| **NRV** | Estimated selling price - Estimated costs to complete - Estimated selling costs |

### Example: NRV Application

| Item | Cost | Selling Price | Selling Costs | NRV | Inventory Value |
|------|------|---------------|---------------|-----|-----------------|
| Product A | ₹100 | ₹150 | ₹10 | ₹140 | ₹100 (Cost) |
| Product B | ₹100 | ₹90 | ₹10 | ₹80 | ₹80 (NRV) |

**Product B must be written down to ₹80.**

---

## Cost Flow Assumptions

When identical items are purchased at different prices, we need a method to determine which cost flows to COGS and which remains in inventory.

### Methods Allowed (Ind AS 2 / AS 2)

| Method | Description |
|--------|-------------|
| **FIFO** | First In, First Out |
| **Weighted Average** | Average of all purchases |
| **Specific Identification** | Actual cost of specific items |

### LIFO Not Allowed

**LIFO (Last In, First Out)** is explicitly prohibited under:
- Ind AS 2
- AS 2
- IFRS

---

## FIFO (First In, First Out)

### Concept

Items purchased first are assumed to be sold first. Closing inventory consists of most recent purchases.

### Characteristics

| Aspect | FIFO |
|--------|------|
| COGS | Based on oldest costs |
| Closing inventory | Based on newest costs |
| In rising prices | Lower COGS, higher profit |
| In falling prices | Higher COGS, lower profit |

### Example: FIFO Method

**Transactions**:

| Date | Particulars | Quantity | Rate | Amount |
|------|-------------|----------|------|--------|
| Mar 1 | Opening Stock | 100 | ₹10 | ₹1,000 |
| Mar 10 | Purchase | 200 | ₹12 | ₹2,400 |
| Mar 15 | Sale | (150) | | |
| Mar 20 | Purchase | 150 | ₹14 | ₹2,100 |
| Mar 25 | Sale | (200) | | |

**FIFO Calculation**:

**Sale on March 15 (150 units)**:
- 100 units @ ₹10 = ₹1,000 (from opening)
- 50 units @ ₹12 = ₹600 (from Mar 10 purchase)
- **Total COGS** = ₹1,600

**Stock after March 15**: 150 units @ ₹12 = ₹1,800

**Sale on March 25 (200 units)**:
- 150 units @ ₹12 = ₹1,800 (remaining from Mar 10)
- 50 units @ ₹14 = ₹700 (from Mar 20 purchase)
- **Total COGS** = ₹2,500

**Closing Stock**: 100 units @ ₹14 = **₹1,400**

**Summary**:
| Item | Amount |
|------|--------|
| Opening Stock | ₹1,000 |
| Purchases | ₹4,500 |
| Goods Available | ₹5,500 |
| Less: Closing Stock | ₹1,400 |
| **Cost of Goods Sold** | **₹4,100** |

---

## Weighted Average Method

### Concept

Calculate average cost of all units available for sale. Use this average for both COGS and closing inventory.

### Types

| Type | When Average Calculated |
|------|-------------------------|
| **Periodic Weighted Average** | At end of period |
| **Moving Weighted Average** | After each purchase |

### Periodic Weighted Average Example

**Same transactions as FIFO example**:

| Date | Particulars | Quantity | Rate | Amount |
|------|-------------|----------|------|--------|
| Mar 1 | Opening Stock | 100 | ₹10 | ₹1,000 |
| Mar 10 | Purchase | 200 | ₹12 | ₹2,400 |
| Mar 20 | Purchase | 150 | ₹14 | ₹2,100 |
| **Total** | | **450** | | **₹5,500** |

**Weighted Average Cost**:
```
₹5,500 / 450 units = ₹12.22 per unit
```

**Calculation**:
| Item | Units | Rate | Amount |
|------|-------|------|--------|
| Total Available | 450 | ₹12.22 | ₹5,500 |
| Less: Sales | 350 | ₹12.22 | ₹4,277 |
| **Closing Stock** | **100** | **₹12.22** | **₹1,222** |

### Moving Weighted Average Example

**Stock Ledger with Moving Average**:

| Date | In | Out | Balance | Average Cost | Value |
|------|-----|-----|---------|--------------|-------|
| Mar 1 | 100 @ ₹10 | | 100 | ₹10.00 | ₹1,000 |
| Mar 10 | 200 @ ₹12 | | 300 | ₹11.33* | ₹3,400 |
| Mar 15 | | 150 | 150 | ₹11.33 | ₹1,700 |
| Mar 20 | 150 @ ₹14 | | 300 | ₹12.67** | ₹3,800 |
| Mar 25 | | 200 | 100 | ₹12.67 | ₹1,267 |

*Average after Mar 10: (1,000 + 2,400) / 300 = ₹11.33
**Average after Mar 20: (1,700 + 2,100) / 300 = ₹12.67

**Closing Stock**: 100 units @ ₹12.67 = **₹1,267**
**COGS**: ₹5,500 - ₹1,267 = **₹4,233**

---

## Specific Identification Method

### Concept

Track actual cost of each specific item. Used for unique, high-value items.

### When to Use

| Suitable For | Not Suitable For |
|--------------|------------------|
| Automobiles | Rice, wheat |
| Real estate | Electronics |
| Jewelry | Clothing |
| Art pieces | FMCG products |
| Custom machinery | Standard parts |

### Example

**Car Dealer**:

| Vehicle | Purchase Cost | Sold? | COGS |
|---------|---------------|-------|------|
| Honda City - MH01AB1234 | ₹12,00,000 | Yes | ₹12,00,000 |
| Maruti Swift - MH01AB1235 | ₹8,00,000 | No | - |
| Hyundai Creta - MH01AB1236 | ₹15,00,000 | Yes | ₹15,00,000 |
| **Total COGS** | | | **₹27,00,000** |
| **Closing Stock** | | | **₹8,00,000** |

---

## Comparison of Methods

### Same Data, Different Results

**Using our example (450 units purchased, 350 sold)**:

| Method | Closing Stock | COGS |
|--------|---------------|------|
| FIFO | ₹1,400 | ₹4,100 |
| Weighted Average (Periodic) | ₹1,222 | ₹4,278 |
| Weighted Average (Moving) | ₹1,267 | ₹4,233 |

### Impact in Rising Prices

| Method | COGS | Gross Profit | Tax | Cash Flow |
|--------|------|--------------|-----|-----------|
| FIFO | Lower | Higher | Higher | Lower |
| Weighted Average | Medium | Medium | Medium | Medium |

### Impact in Falling Prices

| Method | COGS | Gross Profit | Tax | Cash Flow |
|--------|------|--------------|-----|-----------|
| FIFO | Higher | Lower | Lower | Higher |
| Weighted Average | Medium | Medium | Medium | Medium |

---

## Which Method to Choose?

### Factors to Consider

| Factor | FIFO Better | Weighted Average Better |
|--------|-------------|-------------------------|
| Price trend | Falling prices | Rising prices (lower tax) |
| Physical flow | Actual FIFO flow | Random/mixed flow |
| Complexity | Lower volume | High volume, many transactions |
| Industry practice | Perishables | Manufacturing |
| Balance sheet | More current values | Smoothed values |

### Industry Practices

| Industry | Common Method | Reason |
|----------|---------------|--------|
| **Retail** | FIFO | Physical flow matches |
| **Manufacturing** | Weighted Average | Many components |
| **Pharmaceuticals** | FIFO | Expiry dates |
| **Automobiles** | Specific Identification | Unique items |
| **Trading** | FIFO or Weighted Average | Depends on goods |

---

## Accounting Entries

### Purchase Entry

```
Purchases A/c (or Inventory A/c)    Dr.    50,000
Input GST A/c                       Dr.     9,000
    To Supplier A/c                              59,000
(Being goods purchased)
```

### Sale Entry

```
Customer A/c                        Dr.    70,800
    To Sales A/c                                 60,000
    To Output GST A/c                            10,800
(Being goods sold)
```

### Cost of Goods Sold Entry (Perpetual System)

```
Cost of Goods Sold A/c              Dr.    40,000
    To Inventory A/c                             40,000
(Being COGS recorded)
```

### Closing Stock Entry (Periodic System)

```
Closing Stock A/c                   Dr.    15,000
    To Trading A/c                               15,000
(Being closing stock transferred)
```

### Write-down Entry

```
Loss on Inventory Write-down A/c    Dr.     5,000
    To Inventory A/c                              5,000
(Being inventory written down to NRV)
```

---

## Perpetual vs Periodic Inventory System

### Comparison

| Aspect | Perpetual System | Periodic System |
|--------|------------------|-----------------|
| **Stock record** | Continuous updates | End of period count |
| **COGS** | Known immediately | Calculated at period end |
| **Physical count** | Verification purpose | Determines inventory |
| **Technology** | Requires software | Manual possible |
| **Cost** | Higher setup cost | Lower setup cost |
| **Accuracy** | Higher | Lower (no continuous tracking) |

### Perpetual System Example

**Stock Ledger Card**:

| Date | Description | In | Out | Balance | Rate | Value |
|------|-------------|-----|-----|---------|------|-------|
| Apr 1 | Opening | | | 100 | ₹10 | ₹1,000 |
| Apr 5 | Purchase | 50 | | 150 | ₹12* | ₹1,600 |
| Apr 10 | Sale | | 80 | 70 | ₹10.67 | ₹747 |
| Apr 15 | Purchase | 100 | | 170 | ₹11.16** | ₹1,897 |
| Apr 25 | Sale | | 120 | 50 | ₹11.16 | ₹558 |

*New average: (1,000 + 600) / 150 = ₹10.67
**New average: (747 + 1,150) / 170 = ₹11.16

---

## Special Inventory Situations

### Joint Products

Products from same process with significant value.

**Example**: Crude oil → Petrol, Diesel, LPG

**Cost Allocation Methods**:
- Physical quantity method
- Sales value method
- Net realizable value method

### By-Products

Products from same process with minor value.

**Treatment**: Credit by-product NRV to main product cost.

### Scrap

Material with minimal value.

**Treatment**: Credit scrap value when sold.

### Work-in-Progress (WIP)

Partially completed goods.

**Valuation**: Include proportionate conversion costs.

---

## Inventory Disclosures

### Required Disclosures (Ind AS 2)

| Disclosure | Requirement |
|------------|-------------|
| Accounting policies | Cost formula used |
| Carrying amount | Total and by category |
| COGS | Amount recognized as expense |
| Write-downs | Amount recognized in period |
| Reversals | Write-down reversals |
| Pledged inventory | Inventory as security |

### Example Disclosure

**Note X: Inventories**

| Particulars | 31 March 2024 | 31 March 2023 |
|-------------|---------------|---------------|
| Raw Materials | ₹50 lakhs | ₹45 lakhs |
| Work-in-Progress | ₹20 lakhs | ₹18 lakhs |
| Finished Goods | ₹80 lakhs | ₹75 lakhs |
| Stores and Spares | ₹10 lakhs | ₹12 lakhs |
| **Total** | **₹160 lakhs** | **₹150 lakhs** |

*Inventories are valued at lower of cost and net realizable value. Cost is determined using weighted average method.*

---

## Common Mistakes in Inventory Valuation

### Mistake 1: Inconsistent Method

**Error**: Switching between FIFO and Weighted Average
**Impact**: Non-comparable financials
**Solution**: Use consistent method; disclose changes

### Mistake 2: Ignoring NRV

**Error**: Not writing down slow-moving inventory
**Impact**: Overstated inventory and profit
**Solution**: Regular NRV assessment

### Mistake 3: Excluding Costs

**Error**: Not including freight, duties in cost
**Impact**: Understated inventory
**Solution**: Include all costs to bring inventory to present location

### Mistake 4: Including Non-Inventoriable Costs

**Error**: Including abnormal waste, storage costs
**Impact**: Overstated inventory
**Solution**: Expense such costs immediately

### Mistake 5: Poor Cut-off

**Error**: Including goods in transit not owned
**Impact**: Misstated inventory
**Solution**: Verify ownership at cut-off date

---

## Inventory Management Ratios

### Key Ratios

| Ratio | Formula | Interpretation |
|-------|---------|----------------|
| **Inventory Turnover** | COGS / Average Inventory | Times inventory sold |
| **Days Inventory Outstanding** | 365 / Inventory Turnover | Days to sell inventory |
| **Gross Margin** | (Sales - COGS) / Sales | Markup percentage |
| **Inventory to Sales** | Inventory / Annual Sales | Stock as % of sales |

### Example Analysis

| Metric | Year 1 | Year 2 | Trend |
|--------|--------|--------|-------|
| Inventory Turnover | 6x | 5x | Worsening |
| DIO | 61 days | 73 days | Worsening |
| Gross Margin | 25% | 23% | Worsening |

**Interpretation**: Inventory is moving slower; possible obsolescence risk.

---

## Practical Tips

### For Traders

- Use FIFO for perishables
- Conduct quarterly NRV reviews
- Track slow-moving items separately
- Implement proper cut-off procedures

### For Manufacturers

- Segregate raw materials, WIP, finished goods
- Apply consistent overhead allocation
- Review WIP valuation quarterly
- Document cost allocation basis

### For Auditors

- Test physical count accuracy
- Verify NRV calculations
- Check cut-off procedures
- Review cost build-up
- Confirm pledged inventory

---

## Disclaimer

This guide is for educational purposes. Inventory valuation depends on specific business circumstances and applicable accounting standards (Ind AS / IGAAP). Consult a qualified Chartered Accountant for compliance matters.

---

## Summary

Key inventory valuation principles:

1. **Valuation Rule**: Lower of Cost or NRV
2. **FIFO**: First items purchased are first sold
3. **Weighted Average**: Average cost of all units
4. **Specific Identification**: Actual cost of specific items
5. **LIFO**: Not allowed in India
6. **Consistency**: Use same method consistently
7. **Disclosure**: Transparent reporting of policies

**Remember**: The method chosen impacts profit, tax, and balance sheet values. Choose wisely and apply consistently.

---

## Social Media Posts

**LinkedIn**:
"Same inventory. Same sales. Different profits.

Ramesh sold 120 phones for ₹18 lakhs. His profit?
• FIFO method: ₹6 lakhs
• Weighted Average: ₹5.5 lakhs

Nothing changed in the business. Only the accounting assumption.

Key points:
• FIFO: Older costs flow to COGS first
• Weighted Average: Smoothed cost
• LIFO: Not allowed in India

In rising prices, FIFO shows higher profit (and higher tax).

Choose your inventory method wisely. #InventoryAccounting #Finance"

**Twitter/X**:
"Inventory Valuation 101:

FIFO: First bought = First sold
Weighted Avg: Average of all purchases
Specific ID: Actual cost per item

LIFO: ❌ Not allowed in India

Rising prices:
FIFO → Lower COGS → Higher profit → Higher tax

Always value at: Lower of Cost or NRV

#AccountingBasics"

**Instagram**:
"Why do accountants argue about inventory? 🤔

THE PUZZLE:
Bought phones at ₹10K and ₹12K
Sold some phones
Which ones did we sell?

METHOD 1 - FIFO:
Old ones first → Lower COGS → Higher profit

METHOD 2 - Weighted Average:
Average cost → Medium profit

SAME GOODS. DIFFERENT PROFIT. 📊

Why it matters:
• Higher profit = Higher tax 💸
• Lower inventory = Lower assets 📉

Pro tip: Be consistent. Don't switch methods!

#InventoryValuation #AccountingTips"
