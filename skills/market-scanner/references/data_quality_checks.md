# Data Quality Checks & Red Flag Warnings

After a deal passes all 6 disqualifiers, apply these quality checks. These are **warnings** (not disqualifiers) — they flag concerns but don't auto-reject.

---

## Validity Checks (Extract-Phase Validation)

### Check 1: Plausibility of Revenue
**Question:** Does the revenue align with what's reasonable for this vertical?

**Benchmarks (approximate):**

| Vertical | Small | Medium | Large | Notes |
|----------|-------|--------|-------|-------|
| Janitorial | $250K-$500K | $500K-$1.5M | $1.5M-$3M | Revenue per crew: ~$60-80K/yr |
| Car wash | $400K-$1M | $1M-$2M | $2M-$3M | Revenue per bay: ~$150-200K/yr |
| Laundromat | $250K-$600K | $600K-$1.2M | $1.2M-$3M | Revenue per machine: ~$12-15K/yr |

**Check formula:**
- Janitorial: Estimate # of teams. (Revenue / $70K per team) = # teams. Is this plausible?
- Car wash: (Revenue / $175K per bay) = # bays. Does it match the description?
- Laundromat: (Revenue / $14K per machine) = # machines. Does it match described equipment count?

**Example:**
```
Deal: Car wash, $1.85M revenue
Implied bays: $1.85M / $175K = 10.6 bays
Check broker email for bay count
If email says "12-bay tunnel": ✓ plausible
If email says "4-bay express": ⚠ suspicious high revenue
```

**Action if implausible:**
- FLAG: "Revenue seems high/low for described operation"
- Ask broker for breakdown (revenue by service type, hourly rates, average transaction)

---

### Check 2: SDE Margin Plausibility
**Question:** Is the SDE % of revenue realistic for the vertical?

**Expected SDE margins (% of revenue):**

| Vertical | Typical | Red Flag High | Red Flag Low |
|----------|---------|---------------|---|
| Janitorial | 25-35% | >40% | <15% |
| Car wash | 20-30% | >40% | <10% |
| Laundromat | 25-40% | >45% | <15% |

**Calculation:** SDE Margin % = (SDE / Revenue) × 100

**Example:**
```
Deal: Janitorial, $420K revenue, $130K SDE
Margin: $130K / $420K = 31%
Check: 25-35% range ✓ Normal

Deal: Laundromat, $350K revenue, $160K SDE
Margin: $160K / $350K = 46%
Check: >45% = RED FLAG — suspiciously high
Action: FLAG: "SDE margin unusually high, verify with broker"
```

**Why margins might be inflated:**
- Seller added back personal expenses (car, phone, vacation) that won't continue
- Owner working excessive unpaid hours (burnout signal)
- Temporary cost reduction (one-time effect)
- Unsustainable labor cuts

**Action if flagged:**
- ASK: "Can you detail the add-backs to arrive at SDE?"
- Look for sustainability (is this repeatable profit?)

---

### Check 3: SDE Multiple Plausibility
**Question:** Is the asking price reasonable relative to earnings?

**Expected SDE multiples (healthy range):**

| Vertical | Normal | Overpriced | Underpriced |
|----------|--------|-----------|---|
| Janitorial | 2.5x-3.5x | >4.0x | <2.0x |
| Car wash | 3.0x-4.5x | >5.0x | <2.5x |
| Laundromat | 2.5x-4.0x | >4.5x | <2.0x |

**Calculation:** SDE Multiple = Asking Price / SDE

**Example:**
```
Deal: Car wash, $1.85M revenue, $420K SDE, $1.85M asking
Multiple: $1.85M / $420K = 4.4x
Check: 3.0-4.5x range ✓ Normal

Deal: Laundromat, $350K revenue, $105K SDE, $520K asking
Multiple: $520K / $105K = 4.95x
Check: >4.5x = OVERPRICED
Action: FLAG: "Asking price appears high relative to earnings"
```

**Why multiples might be inflated:**
- Seller hopes for buyer foolishness
- Recent improvements not yet reflected in earnings
- Unusual buyer demand (e.g., large corporate buyer willing to overpay)
- Real estate bundled in (but you shouldn't include RE in business multiple)

**Why multiples might be depressed:**
- Seller motivated to exit (burnout, health, partnership dispute)
- Recent revenue decline (trend matters)
- Hidden operational issue

**Action if flagged:**
- For overpriced: Negotiate harder, or move on
- For underpriced: Ask about issues, verify trend, but opportunity!

---

### Check 4: Missing Operational Detail
**Question:** Do we have enough operational info to evaluate absentee viability?

**Required fields per vertical:**

**Janitorial:**
- ✓ # of crews or monthly contract count
- ✓ % of revenue from top 3 clients
- ✓ Equipment needs (minimal for janitorial)

**Car wash:**
- ✓ # of bays / tunnel length
- ✓ Equipment age and condition
- ✓ Staffing model (automated vs. hand wash attendants)
- ✓ Membership model (if applicable)

**Laundromat:**
- ✓ # of machines and types
- ✓ Equipment age
- ✓ Lease term remaining
- ✓ Coin vs card/app payment split
- ✓ Staffing (attended vs unattended)

**Action if missing:**
- FLAG: "Missing key operational detail, ask broker for: [field]"
- Don't disqualify, but defer deal-evaluator until you have the info

---

### Check 5: Customer Concentration Risk
**Question:** Is the business too reliant on a few customers?

**Concentration thresholds:**

| Risk Level | Definition | Action |
|-----------|-----------|--------|
| **Low** | Top customer <25% revenue | ✓ Proceed |
| **Medium** | Top customer 25-50% | ⚠ FLAG: Verify customer retention |
| **High** | Top customer >50% | 🚩 MAJOR RED FLAG: Customer concentration risk |

**Exceptions (less risky):**
- Coin laundromats: No customer concentration (anonymous coin revenue)
- Car washes: Less risky if membership base is large and sticky

**Example:**
```
Deal: Janitorial, $420K revenue
Top 3 clients: ABC Corp $150K (36%), DEF Inc $100K (24%), GHI $80K (19%)
Concentration: Top customer = 36%
Check: 25-50% medium concentration
Action: FLAG: "Customer concentration medium, verify contract terms"
Question: What's the contract renewal risk with ABC Corp?
```

**Action if flagged:**
- ASK: Contract terms for top customers (auto-renewal? termination notice?)
- Assess buyer's ability to maintain relationships (H1B = can't manage directly)
- Evaluate impact if top customer leaves

---

### Check 6: Trend Analysis (If Available)
**Question:** Is revenue growing, stable, or declining?

**Trend types:**
- **Growth** (YoY ↑): Positive, business is improving
- **Stable** (YoY ≈): Neutral, business is steady-state
- **Decline** (YoY ↓): Negative, needs investigation

**If broker provides 3-year history:**
```
Year 1: $350K
Year 2: $380K
Year 3: $420K
Trend: +8.6% CAGR — Growing ✓
```

**If declining:**
```
Year 1: $450K
Year 2: $420K
Year 3: $380K
Trend: -8.4% CAGR — Declining ⚠ FLAG
Action: ASK: Why declining? Is it temporary or structural?
```

**Common causes of decline:**
- Owner losing interest (burnout) → often reversible
- Market downturn → macro issue
- Key customer loss → need to understand replacement pipeline
- Competitive pressure → assess barriers to entry

**Action if flagged:**
- If growth: Good sign, justifies asking price
- If stable: Normal, no concern
- If declining: Ask broker why, assess if reversible

---

## Duplicate Detection

**Question:** Is this deal already in our deals.json?

**Check logic:**
1. Extract business name + city from new deal
2. Search deals.json for matching name + city (case-insensitive)
3. If found: MARK as duplicate, do NOT ingest

**Example:**
```
New deal: "Decatur Laundromat, Decatur, GA"
Check deals.json:
  - Found: Deal ID DISCOVERY-20260402-RJ "Decatur Laundromat, Decatur, GA"
Result: DUPLICATE — Don't re-ingest, note in log
```

**Why duplicates occur:**
- Same broker sending same listing via email multiple times
- Different brokers listing the same deal
- Broker updating listing with new broker contacts

**Action:**
- Log duplicate in scan_log
- If broker changed, update deal's broker_contacts list (don't overwrite, append)

---

## Flag Summary for User

**After all checks, generate a flag report:**

```
Scan Complete: 3 deals ingested
⚠  Warnings (review before deal-evaluator):

1. Decatur Laundromat
   - SDE margin unusually high (46% vs 25-40% typical)
   - Ask broker to detail add-backs

2. Jacksonville Car Wash
   - Revenue appears high for 4-bay facility ($1.85M / 4 bays = $462K/bay)
   - Verify membership model and service mix

3. Nashville Janitorial
   - Top customer = 40% of revenue, contract term unclear
   - Critical to understand renewal risk for H1B passive operation

All 3 passed buyer filters and are in DISCOVERED state.
Proceed to /deal-evaluator when ready.
```

---

## Warning Flags (Don't Auto-Disqualify)

| Flag | Severity | Action |
|------|----------|--------|
| Revenue margin high | ⚠ Medium | Ask broker for detailed P&L |
| SDE multiple >4.5x | ⚠ Medium | Negotiate, or pass |
| SDE multiple <2.0x | ⚠ Low | Good value, investigate why underpriced |
| Customer concentration 25-50% | ⚠ Medium | Ask about contract terms |
| Equipment age at lifecycle end | ⚠ Medium | Plan for capex investment |
| Lease <5 years (laundromat) | ⚠ High | Risky for equipment payback |
| Missing operational detail | ⚠ Medium | Request from broker, defer eval |
| Revenue declining YoY | ⚠ Medium | Ask why, assess reversibility |
| Duplicate deal | ℹ️ Info | Log in scan_log, don't re-ingest |

**Philosophy:** Warnings inform but don't block. A deal with warnings can still be excellent if the underlying risk is understood and mitigated.
