# Buyer Filter Rules — Disqualification Criteria

Conservative, absolute filters applied to every discovered deal. Any match = FILTERED OUT.

---

## The 6 Absolute Disqualifiers

### Disqualifier 1: Franchise Detected
**Rule:** If business is franchised or franchise-like, FILTER OUT immediately.

**Detection keywords (any match = disqualify):**
- "franchise" (any form: franchise, franchisee, franchisor, franchising)
- "FDD" (Franchise Disclosure Document)
- "franchise agreement"
- "brand rights"
- "licensed brand"
- "business format franchise"
- "turnkey franchise"
- Popular franchise brands: Subway, Anytime Fitness, JAN-PRO, Molly Maid, etc.

**Why:** Franchises violate buyer profile (no solo acquisition), require upfront fees, lock you into brand, limit margins, and typically don't permit passive operation.

**Example:**
```
Email subject: "Jan-Pro Franchise Opportunity"
Check: "Jan-Pro" is a well-known cleaning franchise
Result: FILTER OUT
```

**Exception:** None. Disqualify all franchise-flagged opportunities.

---

### Disqualifier 2: Revenue Out of Range
**Rule:** If annual revenue <$250K OR >$3M, FILTER OUT.

**Thresholds:**
- **Floor:** $250,000 (minimum) — below this, cash flow doesn't support financing + passive owner distribution
- **Ceiling:** $3,000,000 (maximum) — above this, scale/complexity rises, likely requires active management

**Validation:**
- Look for TTM (trailing twelve months) or annual revenue
- If only partial-year data, annualize conservatively (don't over-estimate)
- If range given (e.g., "$300K-$350K"), use midpoint
- If revenue "estimated" or "potential", ask broker for actuals before filtering decision

**Examples:**
```
Email: "Small residential cleaning service, ~$200K annual"
Check: $200K < $250K floor
Result: FILTER OUT

Email: "Commercial janitorial with multiple contracts, $5.2M annual"
Check: $5.2M > $3M ceiling
Result: FILTER OUT

Email: "Laundromat averaging $350K annually"
Check: $350K within $250K-$3M range
Result: PASS filter (proceed to next checks)
```

---

### Disqualifier 3: Wrong Vertical
**Rule:** If business type is NOT janitorial, car wash, or laundromat, FILTER OUT.

**Allowed verticals:**
- Janitorial / commercial cleaning
- Car wash (express, tunnel, self-serve, full-service)
- Laundromat (self-service, attended, wash-dry-fold)

**Disallowed verticals:**
- Franchises (already covered above, but reinforces exclusion)
- SaaS or digital services
- Retail (non-location dependent)
- Restaurants / food service
- Consulting / professional services
- E-commerce businesses
- Dry cleaning (not laundromat)
- Residential cleaning (not commercial janitorial)
- Auto detailing (not car wash)

**Detection logic:**
1. Look for exact vertical keywords in email subject/body
2. If vertical unclear, read full context to determine business model
3. If still ambiguous, ask broker to clarify before filtering decision

**Examples:**
```
Email: "Commercial office cleaning contract business"
Check: Matches "janitorial" vertical
Result: PASS filter

Email: "Detailing and car wash franchise"
Check: Contains "franchise" → disqualify under Disqualifier 1
Also contains "detailing" (not pure car wash) → possible ambiguity
Result: FILTER OUT (franchise clause)

Email: "SaaS subscription service for laundromat management"
Check: SaaS = disallowed vertical
Result: FILTER OUT (wrong vertical)
```

---

### Disqualifier 4: Wrong Geography
**Rule:** If location is NOT in target states, FILTER OUT.

**Target states:**
- Georgia (GA)
- Florida (FL)
- Alabama (AL)
- Tennessee (TN)
- North Carolina (NC)
- South Carolina (SC)

**Rationale:**
- H1B visa holder in Georgia region
- Want to manage deals personally (in-state = better local knowledge)
- State regulatory environment similar across SE cluster

**Detection:**
- Look for city, state in email body
- Parse state abbreviation (GA, FL, etc.)
- If state not listed, ask broker to confirm

**Examples:**
```
Email: "Janitorial service in Jacksonville, FL"
Check: FL in target states
Result: PASS filter

Email: "Laundromat in Phoenix, AZ"
Check: AZ NOT in target states
Result: FILTER OUT (wrong geography)

Email: "Car wash in Nashville"
Check: Nashville = TN, TN in target states
Result: PASS filter

Email: "Cleaning service (location TBD)"
Check: No state mentioned
Result: ASK broker to confirm location before filtering decision
```

---

### Disqualifier 5: Wrong Business Model (Not Passive-Eligible)
**Rule:** If business requires active daily operation from owner, FILTER OUT.

**Passive-eligible models:**
- Unattended laundromat (24/7 coin/card operation, no staff on-site)
- Attended laundromat with separate manager + staff (owner not needed on-site)
- Car wash (automated or staffed, owner not critical to daily operation)
- Janitorial with crews (staff handle client relationships, owner manages back-office)

**Not passive-eligible:**
- Service business requiring owner's specific expertise (handyman, contractor)
- Business with personal client relationships owner must manage
- Sole proprietor operation (owner = the service)
- Pick-up/delivery business (laundromat WDF, delivery services)

**H1B Visa Constraint:**
- H1B visa = cannot "actively work" or "manage day-to-day operations"
- Must be truly passive (invest capital, receive distributions)
- Can't be on-site managing staff or clients

**Detection keywords to exclude:**
- "requires owner involvement"
- "owner currently on-site X hours/week"
- "hands-on management needed"
- "pickup and delivery" (WDF)
- "owner operates" (vs "owner invests")

**Examples:**
```
Email: "Janitorial with 8 crews, owner manages routes and billing"
Check: Owner in back-office, crews handle client contact
Result: PASS filter (passive-eligible)

Email: "One-man handyman service"
Check: "One-man" = sole proprietor = not passive
Result: FILTER OUT (not passive-eligible)

Email: "Unattended 24-hour laundromat, card-based, monthly collection"
Check: No staff on-site, automated operation = fully passive
Result: PASS filter

Email: "Laundromat with wash-dry-fold service, driver does pickups"
Check: "Pickup and delivery" = labor-intensive, requires active management
Result: FILTER OUT (not passive-eligible)
```

---

### Disqualifier 6: Incomplete Deal Information
**Rule:** If critical fields are missing, FILTER OUT.

**Critical fields (all required):**
- Business name
- Revenue (TTM)
- Asking price
- Broker contact (name and email at minimum)

**Why:** Can't evaluate deal without these. Incomplete data = harder for due diligence.

**Detection:**
- After extraction, check if revenue is "not disclosed" or "estimated" (is estimate reliable?)
- Check if asking price missing (broker won't share until NDA?)
- Check if broker contact anonymous (red flag)

**Fallback logic:**
- If name/revenue/price missing, FILTER OUT
- If broker contact missing, FILTER OUT (how will you reach them?)
- If vertical/location ambiguous but other fields complete, FLAG for manual review (don't auto-filter)

**Examples:**
```
Email: "Great janitorial opportunity in Atlanta"
[No revenue, no asking price, no broker name]
Check: Missing revenue, price, broker contact
Result: FILTER OUT (insufficient data)

Email: "Laundromat, Decatur GA, $350K revenue, $385K asking, contact: info@brokers.com"
Check: Name="Decatur Laundromat" (inferred), revenue=$350K, price=$385K, contact=info@brokers.com
Result: PASS filter (all critical fields present)

Email: "We have a car wash available"
[No location, no financials, no contact]
Check: Missing location, revenue, price, contact info
Result: FILTER OUT (insufficient data)
```

---

## Filtering Logic (Sequential Check)

```
For each discovered deal:

1. Check Disqualifier 1 (Franchise)?
   → YES: FILTER OUT, stop
   → NO: Continue

2. Check Disqualifier 2 (Revenue range)?
   → OUT OF RANGE: FILTER OUT, stop
   → IN RANGE: Continue

3. Check Disqualifier 3 (Vertical)?
   → WRONG VERTICAL: FILTER OUT, stop
   → CORRECT VERTICAL: Continue

4. Check Disqualifier 4 (Geography)?
   → WRONG STATE: FILTER OUT, stop
   → CORRECT STATE: Continue

5. Check Disqualifier 5 (Passive-eligible)?
   → NOT PASSIVE: FILTER OUT, stop
   → PASSIVE ELIGIBLE: Continue

6. Check Disqualifier 6 (Data complete)?
   → CRITICAL DATA MISSING: FILTER OUT, stop
   → DATA COMPLETE: Continue

RESULT: Deal passes all filters → INGEST as DISCOVERED
```

---

## Log Disqualification Reason

When a deal is filtered out, record the reason in scan_log:

**Disqualification reasons enum:**
- `franchise` — Franchise detected
- `revenue_below_floor` — Revenue <$250K
- `revenue_above_ceiling` — Revenue >$3M
- `wrong_vertical` — Not janitorial/car wash/laundromat
- `wrong_geography` — Not in GA/FL/AL/TN/NC/SC
- `not_passive_eligible` — Requires active owner operation
- `incomplete_data` — Missing critical fields

**Example scan_log entry:**
```json
{
  "date": "2026-04-05T14:30:00Z",
  "results": {
    "candidates_found": 8,
    "after_filtering": 3,
    "filtered_out": 5,
    "disqualification_reasons": {
      "franchise": 1,
      "revenue_above_ceiling": 1,
      "wrong_vertical": 1,
      "incomplete_data": 2
    }
  }
}
```

---

## No Exceptions

These 6 filters are **absolute**. Do not allow exceptions or judgement calls.

- A "borderline" franchise deal is still a franchise → OUT
- A business at $3.05M revenue is still over ceiling → OUT
- A business that "could be passive" but requires active management → OUT

**Philosophy:** Conservative filtering prevents wasting time on deals that don't fit your profile. Better to miss 1 good deal than pursue 10 bad ones.
