---
name: market-scanner
description: Discover new business listings from broker emails and (future) marketplace APIs. Searches Gmail for broker listings, extracts deal metadata (financials, operations, broker contact), filters against buyer profile (no franchises, $250K-$3M revenue, target verticals/geography), and ingests qualified deals as DISCOVERED state. Auto-trigger on "scan", "find deals", "search listings".
---

# Market Scanner Skill

## Purpose
Automate deal discovery by monitoring broker emails and extracting structured deal metadata. Filter against buyer profile and ingest qualified deals into the pipeline as `DISCOVERED` state. Maintain an audit trail of discovery sources and filter parameters.

## MVP Scope (Phase 1)
- **Primary source:** Gmail broker emails (Phase 1 MVP)
- **Future sources:** BizBuySell API, BizQuest web scraping (Phase 2+)
- **Trigger modes:** Scheduled monthly scan + manual "find deals" command
- **Ingestion:** Auto-ingest to deals.json OR manual review mode
- **Output:** New deals added to DISCOVERED state, audit log updated

---

## 4-Phase Discovery Flow

### Phase 1: DISCOVERY (Gmail Monitoring)
Search Gmail for broker emails containing business listings.

**Search strategy:**
1. Query Gmail for emails matching keyword patterns (see `search_filters.md`)
2. Filter by sender: identify broker firm domains (@firm.com patterns)
3. Extract from recent emails: broker name/email/firm, subject, listing link (if present)

**Output:** Candidate deals from past 30 days of broker emails

**Questions to answer:**
- Which brokers have sent new opportunities?
- What verticals are in the emails (janitorial, car wash, laundromat)?
- Are there links to CIMs or listing pages?

### Phase 2: ENRICHMENT (Deal Metadata Extraction)
For each candidate deal, extract structured metadata.

**Extraction sources (in order of preference):**
1. Email body text (parsed for key-value patterns)
2. CIM attachment (PDF) — if link present, fetch and parse
3. Broker's listing URL (if web scraping enabled in future)

**Fields to extract (see `extraction_schema.md`):**

| Category | Fields |
|----------|--------|
| **Basic** | Business name, city, state, vertical (janitorial/car wash/laundromat) |
| **Financial** | Revenue (TTM), EBITDA/SDE, asking price, implied SDE multiple |
| **Operational** | Staffing breakdown (W-2, 1099, part-time), equipment age, lease term, customer concentration |
| **Broker** | Broker name, email, firm, phone number |
| **Source** | Email message ID (Gmail URL), discovery date, listing URL (if present) |

**Parsing examples:**
- Email: "Janitorial service, $450K revenue, $130K SDE, $380K asking" → Extract as structured record
- CIM attachment: Parse PDF for financial statements, operational metrics
- Broker listing page: Fetch and parse HTML for key metrics

**Quality checks (see `data_quality_checks.md`):**
- Revenue plausible for vertical? (Check against benchmarks)
- Asking price reasonable? (Flag if <$100K or >$3.5M)
- Margins suspicious? (SDE >40% of revenue = red flag)
- Data complete? (All essential fields present?)

### Phase 3: FILTERING (Buyer Profile Match)
Apply **absolute disqualifiers** — any trigger = deal is OUT.

| Disqualifier | Check | Reason |
|--------------|-------|--------|
| **Franchise** | Business name/broker pitch contains "franchise" or "FDD" | Auto-exclude |
| **Revenue out of range** | Revenue <$250K OR >$3M | Outside target |
| **Wrong vertical** | Not janitorial, car wash, or laundromat | Out of scope |
| **Wrong geography** | State not in {GA, FL, AL, TN, NC, SC} | Out of scope |
| **Wrong business model** | Service-only, no physical location, or SaaS | Can't own passively |
| **Incomplete data** | Missing: business name, revenue, asking price, broker contact | Can't evaluate |

**Special case — Duplicates:**
- If deal with same name + city already exists in deals.json → mark as duplicate, don't re-ingest

### Phase 4: INGESTION (Write to Pipeline)
For each deal passing Phase 3, create entry in `data/deals.json`.

**Deal ID format:** `DISCOVERY-{YYYYMMDD}-{BROKER-INITIALS}`
Example: `DISCOVERY-20260405-SB` (discovered April 5, 2026, broker "Sarah Brown")

**JSON structure** (added to deals array in deals.json):
```json
{
  "id": "DISCOVERY-20260405-SB",
  "name": "Decatur Laundromat",
  "business_type": "laundromat",
  "state": "DISCOVERED",
  "state_history": [
    {
      "date": "2026-04-05T14:30:00Z",
      "from_state": null,
      "to_state": "DISCOVERED",
      "note": "Discovered via Gmail from broker Sarah Brown (sarahb@brightbrokerage.com)"
    }
  ],
  "broker": {
    "name": "Sarah Brown",
    "email": "sarahb@brightbrokerage.com",
    "firm": "Bright Business Brokerage",
    "phone": "+1-770-555-1234"
  },
  "metadata": {
    "source": "gmail",
    "email_message_id": "Gmail message ID",
    "listing_url": "https://example.com/listing",
    "discovered_date": "2026-04-05"
  },
  "financials": {
    "revenue_ttm": 350000,
    "sde_estimate": 105000,
    "asking_price": 385000,
    "sde_multiple": 3.67,
    "notes": "From CIM: revenue verified via P&L, SDE adjusted for owner salary"
  },
  "operationals": {
    "location": "Decatur, GA",
    "staffing": "0 FT, 1 PT (attendant 20 hrs/week)",
    "equipment": "Most replaced within last 5 years per broker",
    "lease_term": "4 years remaining, 1 renewal option (5 years)",
    "customer_concentration": "Coin-operated, no customer concentration risk"
  },
  "deal_evaluator_memo": null,
  "financing": {},
  "communications": []
}
```

**Also update `data/scan_log`:**
```json
{
  "date": "2026-04-05T14:30:00Z",
  "verticals_searched": ["janitorial", "car wash", "laundromat"],
  "geography": ["GA", "FL", "AL", "TN", "NC", "SC"],
  "source": "gmail",
  "parameters": {
    "days_lookback": 30,
    "exclude_franchises": true,
    "revenue_min": 250000,
    "revenue_max": 3000000
  },
  "results": {
    "candidates_found": 8,
    "after_filtering": 3,
    "filtered_out": 5,
    "disqualification_reasons": {
      "franchise": 1,
      "revenue_out_of_range": 2,
      "wrong_vertical": 1,
      "incomplete_data": 1
    }
  }
}
```

---

## Reference Files

### 1. `search_filters.md`
Gmail search queries and patterns for finding broker emails with listings.

**Content:**
- Email search queries per vertical (janitorial, car wash, laundromat)
- Keywords to match: "available", "opportunity", "listing", "for sale", "exit", "acquisition"
- Keywords to exclude: "franchise opportunity", "MLM", "affiliate"
- Broker domain patterns (common broker firm domains)
- Time ranges (search last 30 days by default)

### 2. `extraction_schema.md`
Rules for parsing deal information from email bodies and CIM PDFs.

**Content:**
- Regex patterns for extracting revenue, SDE, asking price, count of employees
- Field mapping (what does "gross revenue" mean vs. "net revenue"?)
- Common abbreviations (SDE = Seller's Discretionary Earnings, EBITDA, TTM = trailing twelve months)
- CIM parsing rules (where financial statements are typically found in PDFs)
- Fallback logic (if exact SDE not found, estimate from financial data)

### 3. `buyer_filter_rules.md`
Formalization of the 6 absolute disqualifiers.

**Content:**
- Franchise detection: keywords, FDD indicators, brand name patterns to watch
- Revenue range: $250K floor, $3M ceiling, rounding rules
- Vertical matching: exact names to look for (janitorial vs dry cleaning, car wash vs auto detailing, laundromat vs dry cleaning)
- Geography: state abbreviations, what counts as neighboring GA
- Business model check: passive vs active operation requirements
- Data completeness: which fields are mandatory vs optional

### 4. `data_quality_checks.md`
Validation rules and red flag patterns for discovered deals.

**Content:**
- Plausibility checks (revenue for vertical, SDE as % of revenue, margins)
- Duplicate detection (same name + city = likely duplicate)
- Red flags to warn on (but not auto-reject): asking price too low for revenue, SDE >40% (might be unsustainable), zero growth rate, customer concentration >50%
- Missing data warnings (incomplete financials, no operational detail)
- Consistency checks (does asking price align with SDE multiple?)

---

## Triggers

**Auto-trigger keywords:**
- "scan"
- "find deals"
- "search for deals"
- "check listings"
- "find me new deals"
- "scan for [vertical]" (e.g., "scan for car washes")

**Scheduled triggers** (optional, via `/schedule` skill):
- Monthly reminder: "scan" every first Monday of month at 9am

---

## Ingestion Modes

### Mode 1: Auto-Ingest (Default)
Discovered deals immediately written to `data/deals.json` with state: DISCOVERED.

**Workflow:**
```
User: "scan"
  ↓
[Skill searches Gmail, extracts, filters]
  ↓
✅ 3 deals discovered and added to pipeline
⚠  2 deals filtered (franchise, out-of-range)
ℹ  Scan log updated
```

**Output:**
```
NEWLY DISCOVERED (3)
1. Laundromat, Decatur GA — $385K asking | Revenue $350K | Broker: Sarah Brown
2. Car wash, Jacksonville FL — $2.1M asking | Revenue $1.85M | Broker: Mike Chen
3. Janitorial, Nashville TN — $450K asking | Revenue $420K | Broker: Jennifer Lee

FILTERED OUT (2)
- "XYZ Franchise Network" — franchise detected
- Janitorial service in Montana — geography mismatch

Next: Run /deal-evaluator on these new deals?
```

### Mode 2: Manual Review
Present findings first, user confirms before ingestion.

**Workflow:**
```
User: "scan for laundromats and show me first"
  ↓
[Skill searches and extracts]
  ↓
Scan Results (Pending Your Approval)
1. Laundromat, Decatur GA — $385K asking | Revenue $350K
2. Laundromat, Atlanta GA — $950K asking | Revenue $875K

Approve and ingest both? (yes/no)
Or review individually? (details for each)
```

---

## Key Design Decisions

1. **Gmail-first MVP** — Most brokers email deals proactively. Minimal scope, maximum speed.

2. **Both auto & manual modes** — Auto-ingest for efficiency, manual review for cautious operator.

3. **Comprehensive extraction upfront** — Get all data (financials + operations) so downstream skills (deal-evaluator, financing-advisor) have full context. Avoid round-tripping.

4. **Absolute filters, not scoring** — Unlike broker-vetter (which scores), market-scanner uses binary disqualifiers. Conservative.

5. **Audit trail via scan_log** — Track discovery parameters, candidate counts, disqualification reasons. Full transparency.

6. **No web scraping in MVP** — Gmail polling is simpler, more reliable than scraping. Add BizBuySell API / web scraping in Phase 2.

---

## Limitations & Future Expansion

**Phase 1 (Current):**
- ✅ Gmail broker email monitoring
- ✅ Email body parsing + CIM PDF extraction
- ✅ Structured metadata formatting
- ✅ Buyer profile filtering

**Phase 2 (Future):**
- ⏳ BizBuySell API integration (if API available)
- ⏳ BizQuest web scraping
- ⏳ Cross-reference discovered brokers against broker-vetter database
- ⏳ Automated email reply tracking (acknowledge broker, confirm receipt)

---

## Workflow Integration

```
┌─────────────────────────────────────────────┐
│ User: "scan for new deals"                  │
└────────────┬────────────────────────────────┘
             ↓
   [market-scanner]
   - Search Gmail (30 days lookback)
   - Extract: business name, financials, ops, broker
   - Filter: franchise, revenue, vertical, geography
   - Ingest: write DISCOVERED deals to deals.json
             ↓
┌─────────────────────────────────────────────┐
│ Dashboard shows 3 new DISCOVERED deals      │
│ User: "analyze these" or "/deal-evaluator"  │
└────────────┬────────────────────────────────┘
             ↓
   [deal-evaluator]
   - 11-section memo per deal
   - SDE multiple vs benchmarks
   - Seller motivation analysis
   - Negotiation position
             ↓
┌─────────────────────────────────────────────┐
│ User reviews memos, shortlists best 2       │
│ User: "contact these brokers"               │
└────────────┬────────────────────────────────┘
             ↓
   [broker-communicator]
   - Draft initial diligence questions
   - Send via Gmail (with user approval)
   - Track replies
```

---

## Exit Criteria (MVP Complete)

✅ Skill can search Gmail for broker listings
✅ Skill can extract revenue, SDE, asking price, broker contact from email bodies
✅ Skill can apply the 6 absolute disqualifiers
✅ Skill can write new deals to deals.json as DISCOVERED
✅ Skill maintains scan_log for audit trail
✅ Both auto-ingest and manual-review modes work
✅ Evals pass: normal scan, edge cases (all-franchise results, empty search)
