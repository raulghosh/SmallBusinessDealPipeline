# Value Investor Lens — Small Business Acquisition Analysis

Adapted from Warren Buffett's investment philosophy for private acquisitions
in janitorial/commercial cleaning, car wash, and laundromat verticals.
Target: $250K-$3M revenue businesses in the Southeastern US.

---

## MOAT ANALYSIS FRAMEWORK

A "moat" in a local service business is fundamentally different from a moat in public
equities. There are no patents, no global brands, no technological lock-in. The moats
are smaller, more fragile, and more dependent on the owner's behavior — which is precisely
why they must be evaluated with care before acquisition.

### Moat Type 1: Switching Costs

The cost (financial, operational, or psychological) a customer incurs by changing providers.

| Vertical | Switching Cost Strength | Explanation |
|---|---|---|
| **Janitorial / Commercial Cleaning** | **STRONG** | Written contracts with 30-90 day termination clauses. Onboarding a new cleaner requires key/alarm access setup, security clearance, quality calibration. Facilities managers hate switching — it creates risk for them personally. |
| **Car Wash** | **WEAK** | Monthly memberships create mild friction (cancellation + re-signup elsewhere) but the service is commoditized. Customers will drive an extra 2 minutes for a better wash or lower price. Unlimited wash memberships improve this to MODERATE. |
| **Laundromat** | **WEAK** | Almost zero switching cost. Customer loyalty is purely proximity-based. If a closer or cleaner facility opens, customers leave without hesitation. Loyalty card programs provide marginal retention. |

### Moat Type 2: Local Network Effects

Reputation, word-of-mouth, and embedded relationships that compound over time.

| Vertical | Network Effect Strength | Explanation |
|---|---|---|
| **Janitorial / Commercial Cleaning** | **STRONG** | Property managers talk to each other. One strong reference in a commercial park or office complex opens 3-5 more doors. B2B referrals in local markets are the primary growth engine. 10+ years of operation in one market creates a referral flywheel that a new entrant cannot replicate quickly. |
| **Car Wash** | **MODERATE** | Google/Yelp reviews matter. A well-reviewed wash with 500+ ratings has a visibility advantage that takes years to match. But the network effect is shallow — it helps acquire customers, not retain them. |
| **Laundromat** | **WEAK** | Laundromats are discovered by proximity, not referral. Reviews matter only to avoid the worst options. No meaningful network effect. |

### Moat Type 3: Regulatory Barriers

Permits, licenses, environmental compliance, and zoning that make entry harder.

| Vertical | Regulatory Barrier Strength | Explanation |
|---|---|---|
| **Janitorial / Commercial Cleaning** | **WEAK** | Low barriers. General business license, basic insurance. Some niches (biohazard, medical facility cleaning) require specialized certifications — if the target business holds these, that is a narrow moat. |
| **Car Wash** | **MODERATE** | Environmental permits for water discharge, chemical handling. Zoning approval for new car wash construction is increasingly difficult in suburban SE US markets. An existing, permitted site has genuine scarcity value. |
| **Laundromat** | **MODERATE** | Commercial water/sewer permits, ADA compliance, zoning for laundry use. In dense areas, zoning boards resist new laundromat permits. An existing permitted location is an asset — but only if the lease is long-term and transferable. |

### Moat Type 4: Scale Advantages

Cost efficiencies from equipment purchasing, route density, or multi-location operations.

| Vertical | Scale Advantage Strength | Explanation |
|---|---|---|
| **Janitorial / Commercial Cleaning** | **MODERATE** | Route density reduces drive time per account. Bulk supply purchasing at 5+ crews provides 10-15% supply cost savings. A 15-crew operation has meaningful cost advantages over a 3-crew startup. Multi-location cleaning contracts (e.g., cleaning all 8 branches of a regional bank) are powerful. |
| **Car Wash** | **WEAK** | Single-location businesses in this revenue range have no meaningful scale advantage. Chemical purchasing volume is too small to negotiate meaningfully. Scale only matters at 3+ locations. |
| **Laundromat** | **NONE** | Single-location laundromats have no scale advantage. Equipment is purchased per-machine at standard dealer pricing. No route density, no bulk purchasing leverage at this size. |

### Moat Summary Matrix

| Moat Type | Janitorial | Car Wash | Laundromat |
|---|---|---|---|
| Switching Costs | STRONG | WEAK | WEAK |
| Network Effects | STRONG | MODERATE | WEAK |
| Regulatory Barriers | WEAK | MODERATE | MODERATE |
| Scale Advantages | MODERATE | WEAK | NONE |
| **Overall Moat** | **STRONG** | **MODERATE** | **WEAK** |

**Implication:** Janitorial businesses have the strongest natural moat in this target
set. Car washes have decent positional moats (site + permits) but weak customer moats.
Laundromats have almost no moat — value is in the location/lease and equipment, not the
business itself. Price laundromat acquisitions accordingly (lower multiple).

---

## INTRINSIC VALUE CALCULATION

### Method 1: Owner's Earnings (Buffett's Preferred Approach)

Buffett defines owner's earnings as: net income + depreciation + amortization - average
annual maintenance capital expenditure. For small businesses, adapt this as follows:

```
Owner's Earnings = SDE
                   - True Replacement Cost of Owner's Labor
                   - Annual Maintenance CapEx
                   - Working Capital Increase Required
```

**True Replacement Cost of Owner's Labor:**
This is the market salary for a GM who could fully replace the owner's operational
involvement. Not a token figure — the real number.

| Vertical | Typical GM Replacement Cost (SE US) |
|---|---|
| Janitorial ($500K-$3M revenue) | $55,000 - $75,000 |
| Car Wash ($250K-$1.5M revenue) | $45,000 - $60,000 |
| Laundromat ($250K-$800K revenue) | $35,000 - $50,000 (can be part-time manager) |

**Annual Maintenance CapEx:**
The recurring capital expenditure required to maintain current revenue — not growth capex.

| Vertical | Maintenance CapEx as % of Revenue |
|---|---|
| Janitorial | 2-4% (vehicles, equipment replacement) |
| Car Wash | 8-15% (tunnel equipment, conveyor, chemicals systems) |
| Laundromat | 5-10% (machine replacement on 8-12 year cycle) |

**Worked Example — Janitorial Business:**

```
Listing Price:                   $600,000
Reported SDE:                    $200,000
Revenue:                         $900,000

Owner's Earnings Calculation:
  SDE                            $200,000
  - GM Replacement Salary        ($65,000)
  - Maintenance CapEx (3%)       ($27,000)
  - Working Capital Reserve      ($5,000)
  = Owner's Earnings             $103,000
```

The listing asks $600K for $103K in true owner's earnings. That is a 5.8x multiple on
real cash flow — far less attractive than the 3.0x SDE multiple the broker advertises.

### Method 2: Conservative DCF

Use a higher discount rate than public markets to reflect the illiquidity, concentration
risk, and operational fragility of a small business.

**Discount Rate Selection:**
| Deal Quality | Discount Rate | Justification |
|---|---|---|
| Strong moat, diversified revenue, contracts | 15% | Low end of small business risk premium |
| Average small business | 18% | Standard rate for owner-operated small biz |
| Weak moat, concentrated, owner-dependent | 20-25% | Reflects genuine risk of cash flow disruption |

**Conservative DCF Formula:**

```
Intrinsic Value = SUM(Year 1-5 Owner's Earnings / (1 + r)^n) + Terminal Value

Terminal Value = Year 5 Owner's Earnings / r    (assumes zero growth — conservative)
Terminal Value is then discounted back: TV / (1 + r)^5

Total Intrinsic Value = PV of Years 1-5 + PV of Terminal Value
```

**Worked Example (continuing from above):**

```
Owner's Earnings Year 1:    $103,000
Growth Assumption:          0% (conservative — flat cash flow)
Discount Rate:              18% (average small business)

Year 1 PV:  $103,000 / 1.18^1  =  $87,288
Year 2 PV:  $103,000 / 1.18^2  =  $73,973
Year 3 PV:  $103,000 / 1.18^3  =  $62,689
Year 4 PV:  $103,000 / 1.18^4  =  $53,126
Year 5 PV:  $103,000 / 1.18^5  =  $45,022

Sum of PV (Years 1-5):             $322,098

Terminal Value:  $103,000 / 0.18          = $572,222
PV of Terminal:  $572,222 / 1.18^5        = $250,049

Total Intrinsic Value:                      $572,147
```

**Verdict:** Intrinsic value of ~$572K vs. asking price of $600K.
Margin of Safety = ($572K - $600K) / $572K = **-4.9%** (negative — the deal is slightly overpriced).

If you could negotiate to $460K, the margin of safety would be:
($572K - $460K) / $572K = **19.6%** — approaching "good deal" territory.

### The Margin of Safety Decision Rule

| Margin of Safety | Verdict | Action |
|---|---|---|
| 20%+ | **Excellent deal** | Move fast. Submit LOI within 48 hours. |
| 10-20% | **Good deal** | Proceed, but negotiate harder on terms (seller note, transition). |
| 0-10% | **Fair deal** | Only proceed if strategic value exists (adjacent to existing operations, fills a gap). |
| Negative | **Pass** | Walk away unless you can negotiate price down significantly. |

---

## THE 20-YEAR TEST

Buffett asks: "Would I be comfortable owning this business for 20 years?" For a small
acquisition, the practical version is: "Will this business model still generate cash in
20 years, and is the cash flow predictable enough to service 10 years of debt?"

### Durability Assessment

| Question | Janitorial | Car Wash | Laundromat |
|---|---|---|---|
| Will demand exist in 20 years? | **Yes.** Buildings will always need cleaning. Essential, non-discretionary service. | **Probably.** Cars will exist and get dirty. EV transition is irrelevant — cars still need washing. | **Depends.** In-unit washers are expanding in new construction. Demand is stable in lower-income and dense urban areas but declining in suburban new-build markets. |
| Is the service resistant to technology disruption? | **Yes.** Robotic cleaning is decades away from replacing janitorial work in complex commercial spaces. | **Mostly.** Automation is already embedded in the model (tunnel washes). No further disruption on the horizon. | **Yes.** Washers and dryers are mature technology. Smart machines are incremental, not disruptive. |
| Is the revenue recurring? | **Yes.** Monthly contracts. Best-in-class have 12-24 month written agreements. | **Partially.** Memberships are recurring. Single washes are transactional. A business with 60%+ membership revenue has strong recurring base. | **No.** Pure transactional. No contracts, no memberships (some newer models have wash-and-fold subscriptions — evaluate these as a premium). |

### What Could Kill the Business

| Threat | Janitorial | Car Wash | Laundromat |
|---|---|---|---|
| **Key client loss** | HIGH risk if top client is >20% of revenue. A single contract loss can eliminate profitability. | LOW risk. Revenue is distributed across hundreds of individual customers. | LOW risk. Same as car wash — distributed customer base. |
| **Lease loss** | LOW risk (most cleaning businesses don't need a prime location — office/warehouse is fine). | **CRITICAL.** The business IS the location. Lease expiration without renewal = total loss. | **CRITICAL.** Same as car wash. The lease is the single most important asset. |
| **Regulatory change** | LOW. Cleaning is lightly regulated. | MODERATE. Water use regulations tightening in some SE US municipalities. | LOW. Minimal regulatory exposure. |
| **Demographic shift** | LOW. Commercial office/retail space drives demand. SE US markets are growing. | LOW-MODERATE. Population growth in SE US supports demand. | MODERATE. Laundromat demand correlates with renter density and lower income demographics. Gentrification can destroy the customer base. |
| **Owner departure** | HIGH if owner holds client relationships. LOW if contracts are institutional. | LOW. Customers don't know the owner. | LOW. Customers don't interact with ownership. |

### Debt Service Test

Can the business service a 10-year SBA loan at current cash flow with a 25% cushion?

```
Debt Service Coverage Ratio (DSCR) = Owner's Earnings / Annual Debt Service

Minimum acceptable DSCR:
  Conservative:   1.50x  (Owner's Earnings covers debt payments 1.5 times)
  Acceptable:     1.25x  (tight — any revenue dip causes stress)
  Dangerous:      < 1.25x (do not proceed)
```

**Example:**
- SBA loan: $480K at 10.5% over 10 years = ~$76,800/year in debt service
- Owner's Earnings (from above): $103,000
- DSCR = $103,000 / $76,800 = **1.34x** (acceptable but tight — negotiate price down or seek better terms)

---

## MARGIN OF SAFETY — COMPREHENSIVE CHECKLIST

Beyond the numerical margin of safety from the DCF, evaluate qualitative downside
protection factors. Each of these can erode or destroy the calculated margin of safety.

### Revenue Concentration Risk
| Concentration Level | Risk Rating | Impact on Margin of Safety |
|---|---|---|
| No client > 10% of revenue | LOW | No adjustment needed |
| Top client is 10-20% of revenue | MODERATE | Reduce margin of safety by 5 percentage points |
| Top client is 20-30% of revenue | HIGH | Reduce margin of safety by 10 percentage points |
| Top client is > 30% of revenue | CRITICAL | Reduce margin of safety by 15+ points. Consider a walkaway. |

### Key Person Dependency
| Dependency Level | Risk Rating | Impact on Margin of Safety |
|---|---|---|
| Business has a GM and documented SOPs | LOW | No adjustment |
| Business has a strong #2 but no formal GM | MODERATE | Reduce by 5 points |
| Owner manages all client relationships personally | HIGH | Reduce by 10 points |
| Owner IS the business — sales, ops, delivery | CRITICAL | Reduce by 15+ points. This is not really a business — it is a job being sold as a business. |

### Equipment Obsolescence
| Equipment Age vs. Useful Life | Risk Rating | Impact |
|---|---|---|
| Equipment is < 50% through useful life | LOW | No adjustment |
| Equipment is 50-75% through useful life | MODERATE | Reduce by 3-5 points (capex coming) |
| Equipment is > 75% through useful life | HIGH | Reduce by 8-10 points (imminent replacement) |
| Car wash tunnel or laundromat machines near end-of-life | CRITICAL | Price the replacement cost into your offer as a direct deduction |

### Lease Expiration (Car Wash and Laundromat ONLY)
| Lease Remaining | Risk Rating | Impact |
|---|---|---|
| 10+ years remaining with options | LOW | No adjustment |
| 5-10 years remaining | MODERATE | Reduce by 3-5 points |
| 2-5 years remaining | HIGH | Reduce by 10 points. Negotiate lease renewal BEFORE closing. |
| < 2 years remaining | CRITICAL | Do not close without a signed lease extension. Period. |

### The Final Test

After all adjustments, if the adjusted margin of safety is still positive, the deal is
worth pursuing. If it has gone negative after qualitative adjustments, the price must come
down or the deal should be abandoned.

**Buffett's rule, adapted:** You do not need to swing at every pitch. There is no penalty
for letting a mediocre deal pass. The penalty comes from overpaying for a business that
looked good on a spreadsheet but was fragile in reality.
