# Deal Metadata Extraction Schema

Rules for parsing deal information from broker emails and CIM documents.

---

## Field Definitions & Extraction Rules

### BASIC INFORMATION

#### Business Name
**Where to find:** Email subject, email body first paragraph, CIM cover page

**Extraction rules:**
- Exact business name as listed (don't shorten or expand)
- If name includes franchise brand, note it (for disqualification check)
- If name generic ("ABC Service", "XYZ LLC"), extract trade name if mentioned

**Example:**
```
Email subject: "ABC Janitorial Services - Atlanta - $350K Revenue"
Extracted: name = "ABC Janitorial Services"
```

#### Location (City, State)
**Where to find:** Email subject, body (address line), CIM operations section

**Extraction rules:**
- City: proper case
- State: 2-letter abbreviation
- Don't include street address in this field (store separately if CIM mentions it)

**Example:**
```
Email: "Opportunity in Decatur, GA"
Extracted: location = "Decatur, GA"
```

**Validation:**
- Must be in target states (GA, FL, AL, TN, NC, SC) — if not, FILTER OUT

#### Vertical
**Where to find:** Email subject, body keywords

**Mapping (janitorial):**
- "janitorial" → janitorial
- "commercial cleaning" → janitorial
- "office cleaning" → janitorial
- "contract cleaning" → janitorial
- **Exclude:** "residential cleaning", "maid service", "dry cleaning"

**Mapping (car wash):**
- "car wash" / "carwash" → car wash
- "express wash" / "tunnel wash" → car wash
- "automated wash" → car wash
- "self-serve wash" / "self-service" → car wash
- **Exclude:** "detailing", "hand wash", "dealership wash bay"

**Mapping (laundromat):**
- "laundromat" → laundromat
- "coin laundry" → laundromat
- "self-service laundry" → laundromat
- "wash & fold" → EXCLUDE (labor-intensive, not suitable)
- "industrial laundry" → EXCLUDE (different business model)

**If vertical unclear:**
- Read full email body to determine business model
- If still ambiguous, extract as "unknown" and flag for manual review

**Validation:**
- Must be one of {janitorial, car wash, laundromat} — if not, FILTER OUT

---

### FINANCIAL INFORMATION

#### Revenue (TTM / Last 12 Months)
**Where to find:** Email body ("Annual revenue", "$XXX revenue"), CIM financials

**Extraction rules:**
- Look for exact number (e.g., "$350,000" or "350K")
- Parse "K" as thousands (350K = $350,000)
- Accept "annual", "TTM", "trailing twelve months", "FY 2025"
- If only partial-year data, ask broker for TTM or annualize conservatively

**Common phrasings:**
- "Annual revenue: $350,000"
- "TTM revenue: $350K"
- "2025 YTD revenue $280K (run rate ~$350K)"
- "Approximate annual gross: $350K"

**Validation:**
- Must be numeric, >$0
- Must be in range $250K–$3M — if not, FILTER OUT

**Example:**
```
Email: "This service generates approximately $350K in annual revenue"
Extracted: revenue_ttm = 350000
```

#### SDE / EBITDA (Seller's Discretionary Earnings)
**Where to find:** Email body, CIM financials section

**Extraction rules:**
- SDE = Seller's Discretionary Earnings = EBITDA + owner salary + owner discretionary expenses
- Look for exact number or estimate from broker
- If not provided, estimate: SDE ≈ Revenue × typical margin
  - Janitorial: ~25-35% margin → SDE ≈ Revenue × 0.30
  - Car wash: ~20-30% margin → SDE ≈ Revenue × 0.25
  - Laundromat: ~25-40% margin → SDE ≈ Revenue × 0.30

**Common phrasings:**
- "Owner add-back (salary): $30K"
- "EBITDA: $105K"
- "Seller discretionary earnings: $105K"
- "Adjusted cash flow: $105K"

**Example:**
```
Email: "Revenue $350K, owner salary $75K, normalized SDE ~$105K"
Extracted: sde_estimate = 105000
```

**Fallback rule:** If SDE not stated and revenue is given, estimate at 30% of revenue (conservative for all 3 verticals)

#### Asking Price
**Where to find:** Email subject (sometimes), email body, CIM executive summary

**Extraction rules:**
- Exact asking price as stated by broker
- Parse "K" as thousands
- If range given (e.g., "$380K–$400K"), take midpoint

**Common phrasings:**
- "Asking: $385,000"
- "List price: $385K"
- "Business asking: $385K"

**Validation:**
- Must be numeric, >$0
- If asking price >$3.5M or <$100K, flag as unusual but don't filter out yet (downstream validation)

**Example:**
```
Email: "Asking price: $385,000"
Extracted: asking_price = 385000
```

#### SDE Multiple (Calculated)
**Formula:** SDE Multiple = Asking Price / SDE

**Example:**
```
Asking price: $385,000
SDE: $105,000
SDE Multiple = 385,000 / 105,000 = 3.67x
```

**Reference benchmarks (see valuation_benchmarks.md):**
- Janitorial: 2.5x–3.5x typical
- Car wash (express): 3.0x–4.5x typical
- Laundromat (unattended): 2.5x–4.0x typical

**Flag if:**
- Multiple >5.0x (overpriced, needs explanation)
- Multiple <1.5x (underpriced, possible issues)

---

### OPERATIONAL INFORMATION

#### Staffing
**Where to find:** CIM operations section, email body

**Extraction rules:**
- Count of full-time (FT) employees
- Count of part-time (PT) employees
- Break out by role if possible (e.g., "1 FT manager, 2 PT attendants")
- Note if owner currently works there

**Example:**
```
"Staffed by 1 full-time manager and 3 part-time drivers"
Extracted: staffing = "1 FT manager, 3 PT drivers"
```

**H1B relevance:** Note if business requires active operation. Unattended laundromats (0 FT on-site) score higher for H1B viability than fully-staffed operations.

#### Equipment Age
**Where to find:** CIM operations section, broker email

**Extraction rules:**
- Age of primary equipment (tunnel for car wash, machines for laundromat)
- "Most replaced within last X years" → ask for specifics
- Note equipment lifespan concern (e.g., tunnel installed 2010 = 16 years old = capex cliff)

**Example:**
```
Email: "Equipment: Tunnel installed 2019, good condition"
Extracted: equipment_age = "Tunnel: 2019 (7 years old)"
```

**Benchmarks:**
- Car wash tunnel lifespan: 10-15 years (major rebuild needed after)
- Laundromat equipment lifespan: 8-12 years (depends on brand)
- Janitorial: N/A (low equipment dependency)

#### Lease Term
**Where to find:** CIM real estate section, broker email

**Extraction rules:**
- Years remaining on base lease
- Renewal option terms (how many years, at what terms)
- Total potential occupancy (base + renewals)

**Example:**
```
Email: "4-year lease remaining, 1 renewal option (5 years)"
Extracted: lease_term = "4 years base + 5-year renewal option (9 years total)"
```

**Critical for laundromats:** Lease term must extend beyond equipment payback period (typically 5-7 years). If lease <5 years and asking price justifies 6-year payback, FLAG as risky.

#### Customer Concentration
**Where to find:** CIM customer section, broker narrative

**Extraction rules:**
- % of revenue from largest customer
- If none stated, note as "N/A" or "not disclosed"
- Coin laundromats: no customer concentration (coin-operated = anonymous)
- Car washes: check for fleet contracts (single customer = concentration risk)
- Janitorial: often 3-5 major contracts

**Example:**
```
Email: "3 major contracts: ABC Corp 40%, DEF Inc 25%, GHI Ltd 20%"
Extracted: customer_concentration = "Top 3 = 85% of revenue, largest = 40%"
```

**H1B relevance:** Higher concentration = less passive (need broker to manage relationships)

---

## Extraction from Email Body (Unstructured Text)

**Pattern matching approach:**

| Pattern | Regex-like | Example |
|---------|----------|---------|
| Revenue | `\$[\d,]+K?` or `[\d,]+ revenue` | "$350,000" or "350K revenue" |
| SDE/EBITDA | `(SDE\|EBITDA\|add.back)\s*[\d,]+` | "EBITDA $105K" |
| Asking price | `(asking\|list.*price)\s*[\d,]+` | "asking $385,000" |
| Location | `[A-Z][a-z]+,\s*[A-Z]{2}` | "Decatur, GA" |
| Vertical | `(janitorial\|car wash\|laundromat)` | "car wash" |
| Staffing | `(FT\|full.time\|employee)` | "1 FT manager" |
| Equipment | `(installed\|year\|equipment)` | "Tunnel installed 2019" |
| Lease | `(lease\|year.*remain)` | "4 years remaining" |

---

## Extraction from CIM PDF

**If CIM is attached or linked:**

1. **Fetch the PDF** (WebFetch tool)
2. **Extract text** (pdf_tools: read_pdf_content)
3. **Parse sections** (look for Financial Statements, Operations, Real Estate)
4. **Key pages typically:**
   - Cover page: business name, location, asking price
   - Executive summary: revenue, SDE, key metrics
   - Financials: detailed P&L
   - Operations: staffing, equipment, customer list
   - Real estate: lease terms (if applicable)

**Example CIM structure for laundromat:**
```
Page 1: Cover
- Seller: Sarah Brown
- Business: Decatur Laundromat, Decatur, GA
- Asking: $385,000

Page 2: Executive Summary
- Annual Revenue: $350,000
- Owner Salary: $45,000
- Adjusted EBITDA: $105,000

Page 4-5: Financial Statements
- P&L for past 3 years
- Breakdown by revenue stream (coin vs card)

Page 7: Operations
- Equipment: 25 machines, ages vary (most 2019-2023)
- Staffing: 1 PT attendant (20 hrs/week)
- Hours: 24/7 operation

Page 9: Real Estate
- Lease: 4-year remaining term
- Renewal: 1 optional 5-year renewal
- Base rent: $3,500/month
```

---

## Data Quality Flags

**After extraction, check for:**

| Check | Flag if | Action |
|-------|---------|--------|
| Revenue missing | No revenue stated | FILTER OUT |
| SDE missing | No SDE, can't estimate | FLAG: manual review |
| Asking price missing | No price stated | FILTER OUT |
| Vertical unclear | Multiple interpretations | FLAG: manual review |
| Location missing | No city/state | FILTER OUT |
| Broker contact missing | No broker name/email | FILTER OUT |
| SDE multiple >5x | Overpriced signal | FLAG: overpriced warning |
| Equipment >15yr old (car wash) | Capex cliff concern | FLAG: capex warning |
| Lease <5yr (laundromat) | Payback risk | FLAG: lease warning |
| Customer concentration >50% | Passive operation risk | FLAG: concentration warning |

---

## Example Extraction (Complete)

**Input Email:**
```
From: sarahb@brightbrokers.com
Subject: Decatur Laundromat - $350K Revenue - GA

Hi [Buyer],

I have an excellent laundromat opportunity in Decatur, GA.

Business: Decatur Laundromat (27 machines, fully attended)
Location: Decatur, GA
Annual Revenue: $350,000 (TTM)
Owner Salary: $45,000
Adjusted Earnings: $105,000
Asking Price: $385,000

The equipment is in great shape - most machines replaced 2019-2023.
Lease is strong: 4 years remaining with one 5-year renewal option.

Currently run by owner + 1 PT attendant (20 hrs/week).

Interested in learning more? I can send the CIM.

Sarah Brown
Bright Business Brokerage
sarah@brightbrokers.com | 770-555-1234
```

**Extracted JSON:**
```json
{
  "name": "Decatur Laundromat",
  "business_type": "laundromat",
  "location": "Decatur, GA",
  "financials": {
    "revenue_ttm": 350000,
    "sde_estimate": 105000,
    "asking_price": 385000,
    "sde_multiple": 3.67
  },
  "operationals": {
    "staffing": "1 owner + 1 PT attendant (20 hrs/week)",
    "equipment": "27 machines, most replaced 2019-2023",
    "lease_term": "4 years remaining, 1 renewal option (5 years)"
  },
  "broker": {
    "name": "Sarah Brown",
    "email": "sarahb@brightbrokers.com",
    "firm": "Bright Business Brokerage",
    "phone": "770-555-1234"
  }
}
```

This deal passes all filters and is ready for DISCOVERED ingestion.
