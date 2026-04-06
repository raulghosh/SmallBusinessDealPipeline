# DSCR Calculator — H1B Buyer Edition

## What Is DSCR?

**Debt Service Coverage Ratio (DSCR)** measures whether a business generates enough cash flow
to cover its debt payments.

```
DSCR = Net Operating Income ÷ Annual Debt Service
```

A DSCR of 1.25x means the business generates $1.25 for every $1.00 of loan payments due.
The SBA requires a minimum of 1.25x. Most lenders want 1.25x-1.35x as a safety buffer.

---

## H1B Buyer DSCR — Critical Adjustment

For a US citizen buyer who will actively manage the business, DSCR uses SDE directly because
the owner's labor IS the management.

**For an H1B buyer, this does not apply.** The H1B buyer CANNOT manage the business — a paid
GM is mandatory. That GM salary is a real, non-optional expense that must be deducted from SDE
before calculating DSCR.

### H1B-Adjusted DSCR Formula

```
Step 1: Adjusted NOI = SDE - GM Salary
Step 2: Annual Debt Service = Monthly Loan Payment × 12
Step 3: DSCR = Adjusted NOI ÷ Annual Debt Service
```

**Never skip Step 1.** A DSCR calculated from raw SDE (without GM adjustment) will be
artificially high and will not reflect the actual coverage ratio after the H1B buyer takes
ownership.

**Note on existing GM:** If the business already employs a GM whose salary is already deducted
in the stated financials (i.e., the owner is already paying a GM and SDE reflects this), then
no additional GM adjustment is needed. Confirm this explicitly with the seller before assuming.

---

## GM Salary Benchmarks by Revenue Bracket

These are the annual GM salary figures to use in DSCR calculations. They represent market-rate
compensation for a qualified operator with real authority in the relevant vertical.

| Annual Revenue | GM Salary Range | Use This in DSCR |
|---------------|----------------|-----------------|
| $250K - $500K | $45,000 - $55,000/yr | $50,000 |
| $500K - $1M | $55,000 - $70,000/yr | $62,500 |
| $1M - $2M | $70,000 - $90,000/yr | $80,000 |
| $2M - $3M | $80,000 - $100,000/yr | $90,000 |

**Laundromat adjustment:** Unattended or minimally-attended laundromats often need only a
part-time manager. For laundromats with revenue under $500K, use $40,000-$50,000 (part-time
oversight, not a full-time GM). For larger laundromats with WDF service, use full GM salary.

**Car wash adjustment:** Express car washes with revenue over $750K typically have an on-site
manager already. Confirm whether existing manager salary is already in financials before adding
another GM line item.

---

## Monthly Payment Estimation Formulas

These are the payment factors per $1 of loan amount. Multiply by loan amount to get monthly payment.

| Loan Type | Term | Rate | Monthly Factor |
|-----------|------|------|---------------|
| SBA 7(a) acquisition | 10 years | 10.5% | 0.01348 |
| SBA 7(a) with real estate | 25 years | 10.5% | 0.00955 |
| Seller note (subordinated) | 5 years | 6.0% | 0.01933 |
| Seller note (subordinated) | 7 years | 6.0% | 0.01459 |
| Conventional small business | 7 years | 12.0% | 0.01749 |

**Example:** SBA loan of $800,000 at 10-year term:
- Monthly payment = $800,000 × 0.01348 = $10,784/mo
- Annual debt service = $10,784 × 12 = $129,408

---

## DSCR Status Thresholds

| DSCR Value | Status | Meaning |
|-----------|--------|---------|
| ≥1.35x | STRONG PASS | Comfortable approval margin; lender will be confident |
| 1.25x - 1.34x | PASS | Approvable but thin — consider seller note to buffer against real-world fluctuations |
| 1.10x - 1.24x | MARGINAL | Below SBA minimum; requires seller note or price reduction to fix |
| 1.00x - 1.09x | FAIL — Close | Business barely covers debt; significant renegotiation needed |
| < 1.00x | FAIL — Hard | Business cannot cover debt at current price and structure |

---

## Reverse DSCR Calculation — Maximum Financeable Price

When DSCR fails, calculate the maximum price at which the deal becomes financeable. This
gives the buyer a data-backed number to use in renegotiation.

### Formula (SBA Primary, 10% equity injection)

```
Target DSCR = 1.25 (minimum)
Adjusted NOI = SDE - GM Salary

Required Annual Debt Service = Adjusted NOI / 1.25
Required Monthly Payment = Required Annual Debt Service / 12
Max Loan Amount = Required Monthly Payment / 0.01348
Max Purchase Price = Max Loan Amount / 0.90 (since loan = 90% of price)
```

### Formula (Hybrid, 10% equity, 15% seller carry)

```
Target DSCR = 1.25
Adjusted NOI = SDE - GM Salary

Note: Seller note adds debt service too. Approach iteratively:
  Assume seller note = 15% of price, at 6% / 5yr factor 0.01933
  Seller note monthly = (price × 0.15) × 0.01933
  SBA loan = price × 0.75
  SBA monthly = (price × 0.75) × 0.01348

  Total monthly DS = (price × 0.75 × 0.01348) + (price × 0.15 × 0.01933)
                   = price × (0.010110 + 0.002900)
                   = price × 0.013010

  Total annual DS = price × 0.013010 × 12 = price × 0.15612

  Max price: Adjusted NOI / 1.25 / 0.15612 = Max Price
```

---

## Worked Example 1 — Clean DSCR Pass (Laundromat)

**Deal:** Unattended laundromat, asking $650,000, stated SDE $175,000, annual revenue ~$210,000

**Step 1: GM Salary Adjustment**
- Revenue bracket: $250K-$500K → use $50,000/yr (part-time manager for unattended laundromat)
- Adjusted NOI: $175,000 - $50,000 = **$125,000**

**Step 2: Loan Calculation (SBA Primary, 10% equity)**
- Equity injection: $650,000 × 10% = $65,000
- SBA loan: $650,000 × 90% = $585,000
- Monthly payment: $585,000 × 0.01348 = $7,886/mo
- Annual debt service: $7,886 × 12 = **$94,632**

**Step 3: DSCR**
```
DSCR = $125,000 / $94,632 = 1.32x → PASS (≥1.25x)
```

**Step 4: Cash-on-Cash Return**
- Net annual cash flow: $125,000 - $94,632 = $30,368
- Equity deployed: $65,000
- Cash-on-cash: $30,368 / $65,000 = **46.7% — EXCELLENT**

**Step 5: SBA Eligibility Assessment**
- DSCR ≥ 1.25x: YES
- Business type (laundromat): SBA-eligible
- Assets: equipment and leasehold support collateral
- Assessment: **LIKELY**

**Recommendation:** SBA 7(a) Primary recommended. Pursue Live Oak Bank as specialist lender.
H1B flags: GM must be identified; ensure operating agreement reflects passive investor role.

---

## Worked Example 2 — Marginal DSCR, Seller Note Required (Janitorial)

**Deal:** Commercial janitorial company, asking $1,200,000, stated SDE $250,000, revenue ~$900,000

**Step 1: GM Salary Adjustment**
- Revenue bracket: $500K-$1M → use $62,500/yr
- Adjusted NOI: $250,000 - $62,500 = **$187,500**

**Step 2: Attempt SBA Primary (90% loan)**
- SBA loan: $1,200,000 × 90% = $1,080,000
- Monthly payment: $1,080,000 × 0.01348 = $14,558/mo
- Annual debt service: $14,558 × 12 = **$174,696**

**Step 3: DSCR at SBA Primary**
```
DSCR = $187,500 / $174,696 = 1.07x → FAIL (below 1.25x)
```

**Step 4: Fix with Hybrid Structure (seller note 20%)**
- SBA loan: $1,200,000 × 70% = $840,000
- SBA monthly: $840,000 × 0.01348 = $11,323/mo → $135,876/yr
- Seller note: $1,200,000 × 20% = $240,000 at 6%, 5yr
- Seller note monthly: $240,000 × 0.01933 = $4,639/mo → $55,668/yr
- Total annual debt service: $135,876 + $55,668 = **$191,544**

**Step 5: Revised DSCR**
```
DSCR = $187,500 / $191,544 = 0.98x → FAIL (still below 1.25x)
```

**Step 6: Reverse DSCR — What Price Makes This Deal Work?**
Using hybrid formula (price × 0.15612 = annual DS):
```
Max annual DS = $187,500 / 1.25 = $150,000
Max price = $150,000 / 0.15612 = $960,976 → ~$960K
```

**Conclusion:** At the asking price of $1.2M, this janitorial deal does not pencil even with
seller carry. The maximum financeable price is approximately $960K. The buyer should either:
1. Negotiate price to ~$960K (20% below asking), OR
2. Present a compelling case that SDE is understated (and verify via tax returns), OR
3. Pass on this deal

**Cash-on-Cash at $960K**
- Equity: $96K (10%)
- SBA loan: $720K → monthly $9,706 → annual $116,472
- Seller note: $144K → monthly $2,782 → annual $33,384
- Total annual DS: $149,856
- Net cash flow: $187,500 - $149,856 = $37,644
- C-o-C: $37,644 / $96,000 = **39.2% — STRONG**

---

## Worked Example 3 — DSCR Failure on Overpriced Deal

**Deal:** Service business, asking $2,500,000, SDE $300,000, revenue ~$1.5M

**Step 1: GM Salary Adjustment**
- Revenue bracket: $1M-$2M → use $80,000/yr
- Adjusted NOI: $300,000 - $80,000 = **$220,000**

**Step 2: SBA Primary**
- SBA loan: $2,250,000
- Monthly: $2,250,000 × 0.01348 = $30,330/mo → **$363,960/yr**
- DSCR: $220,000 / $363,960 = 0.60x — HARD FAIL

**Step 3: Reverse DSCR — Max Financeable Price (SBA Primary)**
```
Max annual DS = $220,000 / 1.25 = $176,000
Max monthly DS = $176,000 / 12 = $14,667
Max loan = $14,667 / 0.01348 = $1,088,000
Max price = $1,088,000 / 0.90 = $1,209,000 → ~$1.2M
```

**Step 4: Reverse DSCR — Max Financeable Price (Hybrid)**
```
Max price = $176,000 / 0.15612 = $1,127,000 → ~$1.13M
```

**Conclusion:** The asking price of $2.5M is more than double what this deal can support.
At $300K SDE, a reasonable acquisition price would be $900K-$1.2M (3x-4x SDE). The seller
is asking 8.3x SDE — that multiple cannot be justified for a service business. **Pass unless
price can be negotiated to ≤$1.2M or SDE can be verified substantially higher.**

---

## Quick Reference Card

### SBA Payment Factors (Monthly per $1 of loan)
- 10-year, 10.5%: 0.01348
- 25-year, 10.5%: 0.00955

### GM Salary by Revenue
- $250K-$500K rev: $50K/yr
- $500K-$1M rev: $62,500/yr
- $1M-$2M rev: $80K/yr
- $2M-$3M rev: $90K/yr

### DSCR Thresholds
- ≥1.35x: STRONG PASS
- 1.25-1.34x: PASS
- 1.10-1.24x: MARGINAL
- <1.10x: FAIL

### Max Price Formula (SBA Primary)
```
Max Price = (Adjusted NOI / 1.25) / (0.01348 × 12 × 0.9)
          = (Adjusted NOI / 1.25) / 0.14560
          = Adjusted NOI × 5.49
```
This means: for every $1 of Adjusted NOI, the max SBA-financeable price is ~$5.49.

**Example:** $175K adjusted NOI → max price ≈ $175K × 5.49 = $960K

### Revenue Multiple Sanity Check
If asking price ÷ Adjusted NOI > 6x, DSCR will likely fail. Start reverse calculation immediately.
