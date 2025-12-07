---
title: "GST Accounting in India: Complete Guide to Goods and Services Tax"
date: 2025-12-06
weight: 5
description: "Master GST accounting in India with this comprehensive guide. Learn about CGST, SGST, IGST, input tax credit, GST returns, and practical journal entries."
keywords: ["GST accounting India", "CGST SGST IGST", "input tax credit", "GST returns", "GST journal entries"]
tags:
  - GST
  - taxation
  - indirect tax
  - input tax credit
  - GST returns
series:
  - "Indian Taxation"
cover:
  image: ""
  alt: "GST Accounting Guide India"
  caption: "One nation, one tax - master GST accounting"
  relative: false
ShowToc: true
TocOpen: true
---

## The Great GST Confusion

Prakash owned a textile shop in Surat. Before July 2017, he dealt with:
- VAT (4% to 14.5%)
- Central Excise (12.5%)
- Service Tax (15%)
- Entry Tax
- Octroi
- Multiple returns and compliance

Then GST arrived. **One Tax, One Nation.** But one massive confusion.

His CA handed him a bill for "GST compliance services."

"₹50,000? For what?"

"GSTR-1, GSTR-3B, GSTR-9, GSTR-2A reconciliation, input tax credit claims, e-way bills..."

Prakash realized: GST simplified taxation. But accounting? That got complicated.

---

## What is GST?

### Definition

**Goods and Services Tax (GST)** is a comprehensive indirect tax levied on the supply of goods and services in India, replacing multiple cascading taxes.

### Key Date

**1st July 2017** - GST rollout

### Structure of GST

| Type | Full Form | When Applied |
|------|-----------|--------------|
| **CGST** | Central GST | Intra-state supply (Central share) |
| **SGST** | State GST | Intra-state supply (State share) |
| **IGST** | Integrated GST | Inter-state supply |
| **UTGST** | Union Territory GST | For Union Territories |

### Example

**Intra-State Sale** (Delhi to Delhi):
- Sale: ₹10,000
- GST @ 18%: ₹1,800
  - CGST @ 9%: ₹900
  - SGST/UTGST @ 9%: ₹900

**Inter-State Sale** (Delhi to Mumbai):
- Sale: ₹10,000
- IGST @ 18%: ₹1,800

---

## GST Rate Structure

### Current GST Slabs

| Rate | Examples |
|------|----------|
| **0%** | Essential food items, education, healthcare |
| **5%** | Packaged food, economy hotels, transport |
| **12%** | Processed food, business class hotels, computers |
| **18%** | Most items - electrical, electronics, services |
| **28%** | Luxury items, tobacco, aerated drinks, cement |

### Composition Scheme

| Turnover | Rate | Restrictions |
|----------|------|--------------|
| < ₹1.5 Cr (goods) | 1% (CGST+SGST) | No ITC, no interstate supply |
| < ₹50 L (services) | 6% (CGST+SGST) | Limited to restaurants |

---

## GST Registration

### Mandatory Registration

| Criterion | Threshold |
|-----------|-----------|
| Normal States | Turnover > ₹40 lakhs |
| Special Category States | Turnover > ₹20 lakhs |
| Interstate Supply | Mandatory regardless of turnover |
| E-commerce Operators | Mandatory |
| TDS Deductors | Mandatory |
| Input Service Distributors | Mandatory |

### GSTIN Structure

**Format**: 22AAAAA0000A1Z5

| Position | Meaning | Example |
|----------|---------|---------|
| 1-2 | State Code | 22 (Chhattisgarh) |
| 3-12 | PAN | AAAAA0000A |
| 13 | Entity Number | 1 |
| 14 | Default | Z |
| 15 | Check Digit | 5 |

---

## Key GST Concepts for Accounting

### Place of Supply

| Transaction | Place of Supply | Tax |
|-------------|-----------------|-----|
| Goods - movement | Where goods reach | IGST if different state |
| Goods - no movement | Location of goods | CGST+SGST |
| Services - B2B | Location of recipient | IGST if different state |
| Services - B2C | Location of supplier | CGST+SGST |

### Time of Supply

**Goods**: Earlier of
- Invoice date
- Receipt of payment
- Removal of goods

**Services**: Earlier of
- Invoice date (if issued within 30 days)
- Date of completion of service
- Receipt of payment

### Value of Supply

```
Transaction Value
+ Other levies (excluding GST)
+ Incidental expenses charged
+ Interest, late fees
+ Subsidies (except government subsidies)
= Value for GST
```

---

## GST Accounting Entries

### Basic Ledger Accounts

| Account | Type | Purpose |
|---------|------|---------|
| Input CGST | Asset | CGST paid on purchases |
| Input SGST | Asset | SGST paid on purchases |
| Input IGST | Asset | IGST paid on purchases |
| Output CGST | Liability | CGST collected on sales |
| Output SGST | Liability | SGST collected on sales |
| Output IGST | Liability | IGST collected on sales |
| GST Payable | Liability | Net GST to pay |
| Electronic Cash Ledger | Asset | Cash deposited for GST |

### Purchase Entry (Intra-State)

**Purchased goods worth ₹50,000 + 18% GST from local supplier**

```
Purchases A/c                  Dr.    50,000
Input CGST A/c                Dr.     4,500
Input SGST A/c                Dr.     4,500
    To Supplier A/c                        59,000
(Being goods purchased locally with GST)
```

### Purchase Entry (Inter-State)

**Purchased goods worth ₹50,000 + 18% IGST from out-of-state supplier**

```
Purchases A/c                  Dr.    50,000
Input IGST A/c                Dr.     9,000
    To Supplier A/c                        59,000
(Being goods purchased interstate with IGST)
```

### Sales Entry (Intra-State)

**Sold goods worth ₹80,000 + 18% GST locally**

```
Customer A/c                   Dr.    94,400
    To Sales A/c                           80,000
    To Output CGST A/c                      7,200
    To Output SGST A/c                      7,200
(Being goods sold locally with GST)
```

### Sales Entry (Inter-State)

**Sold goods worth ₹80,000 + 18% IGST to another state**

```
Customer A/c                   Dr.    94,400
    To Sales A/c                           80,000
    To Output IGST A/c                     14,400
(Being goods sold interstate with IGST)
```

---

## Input Tax Credit (ITC)

### What is ITC?

The GST paid on business purchases can be claimed as credit against GST payable on sales.

### Conditions for ITC

| Requirement | Detail |
|-------------|--------|
| Tax invoice | Must have valid invoice with GSTIN |
| Goods/services received | Actually received |
| Tax paid to government | Supplier must have paid |
| Return filed | Must file within time limit |
| Business purpose | Only for business use |

### ITC Not Allowed On

| Item | Reason |
|------|--------|
| Motor vehicles | Unless for specific business use |
| Food and beverages | Unless in same line of business |
| Membership (club, gym) | Personal nature |
| Works contract for construction | Immovable property |
| Rent-a-cab | Unless specific business |
| Personal consumption | Not business use |

### ITC Reversal Required When

| Situation | Action |
|-----------|--------|
| Invoice not paid in 180 days | Reverse ITC claimed |
| Goods destroyed, lost, gifted | Reverse ITC |
| Input used for exempt supplies | Proportionate reversal |

### ITC Set-off Order

**From July 2019**, input tax credit set-off follows this priority:

| Input Credit | First Set-off Against | Then Against |
|--------------|----------------------|--------------|
| IGST | IGST | CGST, then SGST |
| CGST | CGST | IGST only |
| SGST | SGST | IGST only |

**Note**: CGST cannot be set off against SGST and vice versa.

---

## Monthly GST Calculation

### Example: ABC Traders (Maharashtra)

**Output GST (Sales)**:
| Type | Taxable Value | CGST @ 9% | SGST @ 9% | IGST @ 18% |
|------|---------------|-----------|-----------|------------|
| Local Sales | ₹5,00,000 | ₹45,000 | ₹45,000 | - |
| Interstate Sales | ₹3,00,000 | - | - | ₹54,000 |

**Input GST (Purchases)**:
| Type | Taxable Value | CGST | SGST | IGST |
|------|---------------|------|------|------|
| Local Purchases | ₹3,00,000 | ₹27,000 | ₹27,000 | - |
| Interstate Purchases | ₹1,00,000 | - | - | ₹18,000 |

**ITC Available**:
- CGST: ₹27,000
- SGST: ₹27,000
- IGST: ₹18,000

**Set-off Calculation**:

```
IGST Liability: ₹54,000
Less: IGST ITC: ₹18,000
Balance IGST: ₹36,000
Less: CGST ITC (₹27,000 - ₹9,000 for CGST liability): ₹18,000
Net IGST Payable: ₹18,000

CGST Liability: ₹45,000
Less: Remaining CGST ITC (₹27,000 - ₹18,000): ₹9,000
Net CGST Payable: ₹36,000

SGST Liability: ₹45,000
Less: SGST ITC: ₹27,000
Net SGST Payable: ₹18,000
```

**Total GST Payable**: ₹18,000 + ₹36,000 + ₹18,000 = **₹72,000**

---

## GST Payment Entry

### Payment of GST

```
Output CGST A/c               Dr.    45,000
Output SGST A/c               Dr.    45,000
Output IGST A/c               Dr.    54,000
    To Input CGST A/c                      27,000
    To Input SGST A/c                      27,000
    To Input IGST A/c                      18,000
    To Electronic Cash Ledger A/c          72,000
(Being monthly GST liability settled)
```

### Deposit to Electronic Cash Ledger

```
Electronic Cash Ledger A/c    Dr.    72,000
    To Bank A/c                            72,000
(Being GST payment deposited)
```

---

## GST Returns

### Return Calendar

| Return | Purpose | Due Date | Who Files |
|--------|---------|----------|-----------|
| **GSTR-1** | Outward supplies | 11th of next month | All taxpayers |
| **GSTR-3B** | Summary return + payment | 20th of next month | All taxpayers |
| **GSTR-9** | Annual return | 31st December | All taxpayers |
| **GSTR-9C** | Audit reconciliation | 31st December | Turnover > ₹5 Cr |
| **GSTR-2A** | Auto-populated from GSTR-1 | Auto-generated | Reference only |
| **GSTR-2B** | ITC statement | Auto-generated | ITC claims |

### GSTR-3B Structure

| Table | Content |
|-------|---------|
| 3.1 | Outward supplies - taxable |
| 3.2 | Interstate supplies |
| 4 | Eligible ITC |
| 5 | Exempt, nil-rated, non-GST |
| 6 | Payment of tax |

### Late Fees

| Return | Late Fee per Day | Maximum |
|--------|------------------|---------|
| GSTR-3B | ₹50 (₹20 for nil) | ₹5,000 |
| GSTR-1 | ₹50 (₹20 for nil) | ₹5,000 |
| GSTR-9 | ₹200 | 0.5% of turnover |

---

## GSTR-2A vs GSTR-2B Reconciliation

### Why Reconcile?

Your ITC claim (GSTR-3B) should match what suppliers reported (GSTR-1 → GSTR-2A/2B).

### Common Differences

| Issue | Cause | Action |
|-------|-------|--------|
| Invoice missing in 2A | Supplier didn't file | Follow up with supplier |
| Amount mismatch | Data entry error | Verify and correct |
| GSTIN mismatch | Wrong GSTIN entered | Get corrected invoice |
| Period mismatch | Different accounting periods | Adjust in correct period |

### Reconciliation Entry

**ITC available but not in GSTR-2A (claimed anyway)**:

```
ITC Receivable (Suspense) A/c  Dr.    10,000
    To Input CGST A/c                       5,000
    To Input SGST A/c                       5,000
(Being ITC claimed but pending in GSTR-2A)
```

**When it appears in GSTR-2A**:

```
Input CGST A/c                Dr.     5,000
Input SGST A/c                Dr.     5,000
    To ITC Receivable (Suspense) A/c       10,000
(Being ITC now reflected in GSTR-2A)
```

---

## E-Way Bill

### When Required

| Scenario | E-Way Bill Required |
|----------|---------------------|
| Consignment value > ₹50,000 | Yes |
| Interstate movement of goods | Yes, any value (some states) |
| Job work | Yes, if inter-state |
| Within state < ₹50,000 | Generally no |

### Validity

| Distance | Validity |
|----------|----------|
| < 100 km | 1 day |
| Every 100 km | Additional 1 day |
| Over-dimensional cargo | 1 day per 20 km |

### Part A vs Part B

| Part | Details |
|------|---------|
| **Part A** | GSTIN, invoice details, HSN, value |
| **Part B** | Vehicle number, transporter ID |

---

## Special Scenarios

### Reverse Charge Mechanism (RCM)

When buyer pays GST instead of seller.

**Applicable on**:
- Purchases from unregistered dealers
- Specified services (legal, GTA, sponsorship)
- Import of services

**Entry for RCM**:

```
Purchases A/c                  Dr.    10,000
Input CGST A/c (RCM)          Dr.       900
Input SGST A/c (RCM)          Dr.       900
    To Supplier A/c                       10,000
    To Output CGST A/c (RCM)                 900
    To Output SGST A/c (RCM)                 900
(Being purchase under RCM - GST paid by buyer)
```

**Note**: RCM ITC can be claimed in the same month.

### Credit Note

**Issuing credit note for ₹5,000 + GST**:

```
Sales Return A/c               Dr.     5,000
Output CGST A/c               Dr.       450
Output SGST A/c               Dr.       450
    To Customer A/c                        5,900
(Being credit note issued for sales return)
```

### Debit Note

**Issuing debit note for price increase ₹2,000 + GST**:

```
Customer A/c                   Dr.     2,360
    To Additional Sales A/c                2,000
    To Output CGST A/c                       180
    To Output SGST A/c                       180
(Being debit note for price revision)
```

---

## GST in Financial Statements

### Balance Sheet Presentation

**Assets**:
- Input CGST Receivable
- Input SGST Receivable
- Input IGST Receivable
- Electronic Cash Ledger Balance

**Liabilities**:
- Output CGST Payable
- Output SGST Payable
- Output IGST Payable

### Disclosure Requirements

| Disclosure | Requirement |
|------------|-------------|
| Contingent liabilities | Disputed GST demands |
| Commitments | E-way bill related |
| Revenue | GST excluded from revenue |
| Notes | ITC not claimed/reversed |

---

## Common GST Accounting Mistakes

### Mistake 1: Wrong Tax Type

**Error**: Charged IGST on local sale
**Impact**: ITC mismatch, penalties
**Solution**: Always verify place of supply

### Mistake 2: ITC on Non-eligible Items

**Error**: Claimed ITC on car purchase
**Impact**: ITC reversal + interest
**Solution**: Check eligibility before claiming

### Mistake 3: Timing Mismatch

**Error**: Claimed ITC before invoice appears in GSTR-2B
**Impact**: ITC reversal
**Solution**: Reconcile before filing

### Mistake 4: Missing RCM

**Error**: Didn't account for reverse charge
**Impact**: Interest + penalties
**Solution**: Identify all RCM transactions

---

## GST Compliance Checklist

### Monthly Tasks

- [ ] Reconcile sales with GSTR-1
- [ ] Verify ITC with GSTR-2B
- [ ] File GSTR-1 by 11th
- [ ] Pay GST and file GSTR-3B by 20th
- [ ] Generate e-way bills for dispatches
- [ ] Follow up on missing supplier invoices

### Quarterly Tasks (QRMP Scheme)

- [ ] File GSTR-1 quarterly (if opted)
- [ ] Use IFF for B2B invoices monthly
- [ ] Pay GST monthly using challan

### Annual Tasks

- [ ] File GSTR-9 annual return
- [ ] Get GST audit done (if applicable)
- [ ] Reconcile with books and returns
- [ ] Review ITC reversals required

---

## Tools for GST Accounting

### Popular Software

| Software | Best For |
|----------|----------|
| Tally Prime | SMEs, comprehensive GST |
| Zoho Books | Cloud-based, integrated |
| ClearTax | Filing and compliance |
| Busy | Trading businesses |
| SAP B1 | Large enterprises |

### Government Portals

| Portal | Purpose |
|--------|---------|
| gst.gov.in | Return filing, registration |
| ewaybillgst.gov.in | E-way bill generation |
| einvoice.gst.gov.in | E-invoicing |

---

## Disclaimer

This guide is for educational purposes. GST laws are complex and frequently updated. Actual accounting treatment may vary based on specific circumstances. Always consult a qualified GST practitioner or Chartered Accountant for compliance matters.

---

## Summary

GST accounting requires understanding:

1. **Tax Types**: CGST, SGST, IGST based on place of supply
2. **Input Tax Credit**: Claim GST on purchases against sales
3. **Returns**: GSTR-1 (sales), GSTR-3B (summary), GSTR-9 (annual)
4. **Reconciliation**: Match your claims with GSTR-2A/2B
5. **Compliance**: E-way bills, timely filing, RCM

Master GST accounting = Better cash flow + No penalties + Peace of mind.

---

## Social Media Posts

**LinkedIn**:
"Before GST: VAT + Excise + Service Tax + Entry Tax + Octroi = 17+ taxes
After GST: One tax, one return (well, almost!)

Key GST accounting concepts:
• CGST + SGST = Intra-state
• IGST = Inter-state
• ITC = Your money back
• GSTR-3B = Monthly summary
• GSTR-2B reconciliation = Critical

Simplified taxation, complex compliance. That's GST in India. #GST #IndianTaxation"

**Twitter/X**:
"GST Accounting 101:

1️⃣ Charged IGST = Wrong state? Penalty
2️⃣ Missing ITC = Your money lost
3️⃣ Late GSTR-3B = ₹50/day late fee
4️⃣ No reconciliation = ITC reversal

Pro tip: Match GSTR-2B before filing every month

#GST #Accounting"

**Instagram**:
"GST Made Simple 📊

Before: 17+ taxes, multiple returns, cascading effect
After: 4 types (CGST, SGST, IGST, UTGST)

The basics:
✅ Buy something → Pay GST (Input)
✅ Sell something → Collect GST (Output)
✅ Output - Input = GST Payable
✅ File GSTR-1 & 3B monthly

Common mistakes:
❌ Wrong tax type (IGST vs SGST)
❌ Claiming blocked ITC
❌ Missing GSTR-2B reconciliation

One nation, one tax, one headache! 😅

#GSTIndia #AccountingTips"
