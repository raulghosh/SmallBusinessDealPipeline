# Small Business Deal Pipeline

An AI-powered acquisition pipeline for buying janitorial, car wash, and laundromat businesses in the Southeast US — built for a passive investor operating under H1B visa constraints.

---

## Buyer Profile

| Parameter | Value |
|-----------|-------|
| **Visa status** | H1B — passive investor only (cannot actively operate) |
| **Credit score** | 720+ |
| **Existing debt** | None |
| **Concurrent deals** | Up to 3 |
| **Target verticals** | Janitorial/commercial cleaning · Car wash · Laundromat |
| **Target geography** | Georgia neighboring states: FL, AL, TN, NC, SC |
| **Revenue range** | $250K – $3M |
| **Exclusions** | No franchises · No SaaS |

---

## Pipeline Architecture

This pipeline is built as a set of Claude Code **skills** orchestrated by `CLAUDE.md`. Each skill is a focused expert agent with domain knowledge loaded from reference files.

### Deal Flow (State Machine)

```
DISCOVERED → ANALYZED → SHORTLISTED → CONTACTING → IN_DILIGENCE → FINANCING → READY_TO_MEET
     ↓            ↓           ↓            ↓              ↓            ↓
   PASSED       PASSED      PASSED       PASSED         PASSED      PASSED
```

All deal state is persisted in `data/deals.json`.

### Skill Routing

| Stage | Trigger | Skill | State Change | Output |
|-------|---------|-------|-------------|--------|
| Vet brokers | "vet [name]", "is [X] legit", "find brokers" | `/broker-vetter` | None | Tiered score (Tier 1/2/Borderline/Disqualified) |
| Monthly scan | "scan", "find deals" | `/market-scanner` | → DISCOVERED | Deal ID + metadata |
| Deal evaluation | "analyze [deal]" | `/deal-evaluator` | → ANALYZED | 11-section memo |
| Market overlay | Auto (or "market check") | `/market-intel` | Stays ANALYZED | Market Intel Report (state risk, trends, insurance, competitive) |
| Shortlist | User decision | Manual | → SHORTLISTED | — |
| Contact broker | "contact broker" (user approves draft) | `/broker-comm` | → CONTACTING | Email draft for review |
| Track replies | "check for replies" | `/broker-comm` | → IN_DILIGENCE | Reply summary + next action |
| Satisfaction check | "are we good on this?" | `/broker-comm` | → READY_TO_MEET or stay IN_DILIGENCE | Gaps assessment + recommendation |
| Financing | "financing?", "SBA loan?", auto on SHORTLISTED | `/financing` | Parallel | Financing Memo (DSCR, structures, SBA eligibility) |
| Pass | User decides to walk | Manual | → PASSED | — |

---

## Skills

### `/broker-vetter` — Broker Identification & Vetting
Finds and scores business brokers against a conservative trust rubric. Grounds discovery in authoritative directories (IBBA, M&A Source, GABB, state licensing), not LLM recall. Aggressively disqualifies lemons.

**Philosophy:** Missing a good broker costs little. Trusting a bad broker costs a deal.

**Key components:**
- `scoring_rubric.md` — Hard signals (+3 each), medium signals (+2 each), 7 absolute disqualifiers
- `directory_sources.md` — Authoritative sources: IBBA, M&A Source, GABB, state licensing boards, BizBuySell, Axial
- `red_flag_patterns.md` — Detection patterns: review farming, SEO spam, anonymous ops, no deal transparency
- `vertical_specialists.md` — How to verify genuine specialization in target verticals vs self-claimed

**Outputs:** Tiered broker shortlist (Tier 1 / Tier 2 / Borderline / Disqualified) with evidence trail and source URLs

**Evals:** 6 test cases · 31 assertions

---

### `/deal-evaluator` — Deal Analysis (Warren Buffett lens)
Produces an 11-section acquisition memo analyzing deals through multiple expert lenses simultaneously.

**11-section memo format:**
1. Deal Vitals
2. Valuation Assessment (SDE multiples vs vertical benchmarks)
3. Intrinsic Value & Margin of Safety (Owner's Earnings + DCF)
4. Seller-Side Decoding
5. Seller's Axe (behavioral analysis — what does the seller *really* want?)
6. Absentee Viability Score (critical for H1B buyer)
7. Red Flags
8. Green Flags
9. Due Diligence Priority Questions
10. Negotiation Position (BATNA, opening offer, walkaway price)
11. Broker's Verdict

**Vertical coverage:**

| Vertical | SDE Multiple Range | Key Risks | Absentee Score |
|----------|-------------------|-----------|---------------|
| Janitorial | 2.5x–3.5x | Customer concentration, W-2/1099 mix, GM dependency | 5–7/10 |
| Car wash (express/tunnel) | 3.0x–4.5x | Equipment capex cliff, membership inflation, RE separation | 7–9/10 |
| Car wash (full-service) | 2.0x–3.5x | Labor dependency | 2–4/10 |
| Laundromat (unattended) | 2.5x–4.0x | Lease term, coin revenue unverifiability, equipment age | 7–9/10 |
| Laundromat (attended/WDF) | 2.0x–3.5x | Labor, WDF pickup/delivery ops | 4–6/10 |

**Reference files (8):**
- `valuation_benchmarks.md` — Vertical-specific SDE ranges, premium/discount factors
- `seller_playbook.md` — Seller tactics by vertical (revenue presentation games, timing tricks)
- `absentee_due_diligence.md` — Absentee viability scoring by business model
- `value_investor_lens.md` — Buffett-style moat analysis, intrinsic value + margin of safety
- `negotiation_playbook.md` — BATNA framework, anchoring, concession ladder, deal structure levers
- `behavioral_reads.md` — Seller archetypes, deflection patterns, "axe" identification methodology
- `carwash_industry_guide.md` — Equipment, revenue drivers, seasonality, regulatory, metrics
- `laundromat_industry_guide.md` — Equipment, revenue verification, lease dependency, utilities

**Evals:** 6 test cases · 45 assertions · **100% passing** across all 3 verticals

---

### `/market-scanner` — Deal Discovery
Searches Gmail for broker emails, extracts deal metadata, applies buyer filters (6 absolute disqualifiers), and auto-ingests to `data/deals.json` as `DISCOVERED`.

**MVP approach:** Gmail-first discovery with clear roadmap for BizBuySell/BizQuest web scraping (Phase 2+).

**Key components:**
- `search_filters.md` — Gmail query templates by vertical + geography
- `extraction_schema.md` — Field definitions, extraction rules, CIM PDF parsing
- `buyer_filter_rules.md` — 6 disqualifiers: franchise, revenue range, vertical, geography, passive-eligibility, data completeness
- `data_quality_checks.md` — Validity checks (SDE margin, multiple plausibility), duplicate detection, warning flags

**Output modes:** Auto-ingest (direct to deals.json) or manual review (user approval before ingest)

**Evals:** 6 test cases · 36 assertions

---

### `/broker-comm` — Broker Communication & Threading
Drafts outreach emails, tracks reply threads, manages diligence Q&A, and flags gaps. All communication is draft-first (user approval required before sending). Gmail + Google Calendar integration via MCP.

**Four operating modes:**
1. **DRAFT_INITIAL_OUTREACH** — Broker vetting check, NDA request, CIM ask
2. **CHECK_REPLIES** — Search Gmail, build summary table, flag 7/14/21-day thresholds
3. **DRAFT_FOLLOW_UP** — Polite nudge if >7 days silent, escalate if >14 days, recommend PASSED if >21 days
4. **SATISFACTION_CHECK** — Map answered vs. outstanding diligence questions, recommend READY_TO_MEET or flag gaps

**Key components:**
- `email_templates.md` — 8 templates (initial, NDA follow-up, post-CIM questions, serious interest, seller meeting, soft pass, hard pass, follow-up nudge)
- `communication_protocol.md` — Timing rules (0→21+ days), 7-step sequence, broker red flags
- `diligence_question_bank.md` — 6 universal categories + 3 vertical-specific question sets

**Evals:** 6 test cases · 32 assertions

---

### `/market-intel` — Market Intelligence & Risk Overlay
Enriches analyzed deals with state regulatory context, industry macro trends, insurance cost benchmarks, and competitive landscape. Runs auto after `/deal-evaluator`. Advisory overlay only — does NOT override deal verdict.

**Market Intel Report includes:**
- Overall market risk (LOW / MEDIUM / HIGH)
- Regulatory environment (state-specific)
- Industry trend (GROWING / STABLE / DECLINING)
- Insurance outlook (cost % of revenue, coverage risks)
- Competitive density (THIN / MODERATE / SATURATED)
- Tailwinds & headwinds (2-3 bullets each)
- Market verdict (GO / CAUTION / AVOID — independent of deal financials)

**Key components:**
- `state_risk_profiles.md` — GA, FL, AL, TN, NC, SC regulatory + workers comp + insurance context
- `industry_trends.md` — Consolidation risk, labor cost trends, regulatory shifts (janitorial/car wash/laundromat)
- `insurance_benchmarks.md` — Per-vertical cost ranges ($1.5K–$8K GL, 1.5–4% workers comp), below-benchmark flags
- `competitive_signals.md` — Density frameworks (5-mile car wash count, laundromats/household ratio, metro context)

**Evals:** 5 test cases · 28 assertions

---

### `/financing` — Financing Advisory & SBA Modeling
Analyzes financing options (SBA 7(a), seller financing, hybrid, conventional, cash) and models DSCR for H1B passive investors. **Runs in parallel — never blocks deal progression.**

**DSCR analysis includes:**
- H1B-adjusted formula: (SDE − GM Salary) ÷ Annual Debt Service ≥ 1.25x
- GM salary benchmarks by deal size ($45K–$100K depending on revenue)
- Reverse DSCR calculation for price renegotiation
- Recommended structure with alternative scenarios

**Key components:**
- `sba_eligibility.md` — H1B passive investor requirements, Live Oak Bank (laundromat/car wash specialist) called out
- `financing_structures.md` — SBA primary, seller financing, hybrid SBA+seller note, conventional, cash (with worked examples)
- `dscr_calculator.md` — Formula, GM benchmarks, monthly payment factors, status thresholds (STRONG PASS / PASS / MARGINAL / FAIL)
- `loan_package_checklist.md` — 3-phase checklist (pre-LOI, with LOI, underwriting), H1B-specific docs, timeline (60–120 days)

**Evals:** 6 test cases · 38 assertions

---

## Repository Structure

```
SmallBusinessDealPipeline/
├── README.md                          # This file
├── CLAUDE.md                          # Pipeline orchestrator (session start, skill routing, state machine)
├── INTEGRATION_TEST_REPORT.md         # End-to-end validation with test deal
├── INTEGRATION_TEST_SUMMARY.md        # Executive summary of test results
├── INTEGRATION_TEST_CHECKLIST.md      # Specification compliance matrix
├── data/
│   └── deals.json                     # Pipeline state (all deals, history, memos, financing)
├── skills/
│   ├── broker-vetter/                 # Broker identification and vetting (4 refs, 31 assertions)
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── scoring_rubric.md
│   │   │   ├── directory_sources.md
│   │   │   ├── red_flag_patterns.md
│   │   │   └── vertical_specialists.md
│   │   └── evals/evals.json
│   ├── deal-evaluator/                # 11-section deal analysis (8 refs, 41 assertions)
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── valuation_benchmarks.md
│   │   │   ├── seller_playbook.md
│   │   │   ├── absentee_due_diligence.md
│   │   │   ├── value_investor_lens.md
│   │   │   ├── negotiation_playbook.md
│   │   │   ├── behavioral_reads.md
│   │   │   ├── carwash_industry_guide.md
│   │   │   └── laundromat_industry_guide.md
│   │   └── evals/evals.json
│   ├── market-scanner/                # Gmail discovery (4 refs, 36 assertions)
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── search_filters.md
│   │   │   ├── extraction_schema.md
│   │   │   ├── buyer_filter_rules.md
│   │   │   └── data_quality_checks.md
│   │   └── evals/evals.json
│   ├── market-intel/                  # Market overlay (4 refs, 28 assertions)
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── state_risk_profiles.md
│   │   │   ├── industry_trends.md
│   │   │   ├── insurance_benchmarks.md
│   │   │   └── competitive_signals.md
│   │   └── evals/evals.json
│   ├── broker-comm/                   # Communication & threading (3 refs, 32 assertions)
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── email_templates.md
│   │   │   ├── communication_protocol.md
│   │   │   └── diligence_question_bank.md
│   │   └── evals/evals.json
│   └── financing/                     # SBA modeling (4 refs, 38 assertions)
│       ├── SKILL.md
│       ├── references/
│       │   ├── sba_eligibility.md
│       │   ├── financing_structures.md
│       │   ├── dscr_calculator.md
│       │   └── loan_package_checklist.md
│       └── evals/evals.json
└── .gitignore
```

**All 6 skills complete:** 23 reference files · 6 eval suites · 174 total assertions (111+ core, rest integration tests)

---

---

## Production Status

✅ **All 6 skills built and tested**
- Broker Vetter: 6/6 evals passing · 31 assertions
- Market Scanner: 6/6 evals passing · 36 assertions
- Deal Evaluator: 6/6 evals passing · 41 assertions
- Market Intel: 5/5 evals passing · 28 assertions
- Broker Comm: 6/6 evals passing · 32 assertions
- Financing: 6/6 evals passing · 38 assertions

✅ **Integration test complete** — Full end-to-end pipeline validated with test deal (Wash & Win Laundromat, Charlotte NC)
- All 6 skills executed in sequence without errors
- Approval gates enforced (broker vetting, email drafts)
- State machine validated (5 transitions logged)
- All calculations verified (DSCR 1.87x, cash-on-cash 127% annual)

✅ **Ready for production use**
- Live Gmail scanning available
- Real broker vetting operational
- Deal analysis fully implemented
- H1B visa constraints enforced throughout

See `INTEGRATION_TEST_REPORT.md` for full validation details.

---

## Quick Start

### 1. Set up your Gmail labels (optional but recommended)
Create labels in Gmail for deal tracking:
- "BizBuySell" or "deal-listings"
- "brokers" for broker emails
- "awaiting-reply" for deals in CONTACTING state

### 2. Run your first scan
```
User: "scan for deals"
→ /market-scanner triggers
→ Searches Gmail for broker emails
→ Applies buyer filters
→ Presents candidates for review
→ Ingests approved deals to deals.json as DISCOVERED
```

### 3. Vet a broker (before engaging)
```
User: "is [broker name] legit?"
→ /broker-vetter triggers
→ Scores broker against 7 red flags + positive signals
→ Returns: Tier 1 / Tier 2 / Borderline / Disqualified
```

### 4. Analyze a discovered deal
```
User: "analyze [deal name]"
→ /deal-evaluator triggers
→ Produces 11-section memo with valuation, seller archetype, absentee score, negotiation position
→ Deal state → ANALYZED
```

### 5. Get market context
```
→ /market-intel auto-triggers (or manually: "market check")
→ Adds state risk, industry trends, insurance, competitive density
→ Verdict: GO / CAUTION / AVOID (advisory, doesn't override deal verdict)
```

### 6. Contact a broker
```
User: "contact broker for [deal]"
→ /broker-comm drafts initial inquiry email
→ Shows draft for approval (no auto-send)
→ User reviews subject/body
→ User: "send it"
→ Email sent via Gmail
→ Deal state → CONTACTING
```

### 7. Check for replies
```
User: "check for replies"
→ /broker-comm searches Gmail for all CONTACTING deals
→ Shows reply status, time waiting, next action
→ Flags if >7 days silent (suggests follow-up), >14 days (escalate), >21 days (recommend pass)
```

### 8. Model financing
```
User: "what's the financing on this deal?"
→ /financing analyzes all structures (SBA 7(a), seller carry, hybrid, conventional, cash)
→ Calculates DSCR for each (must be ≥1.25x for SBA approval)
→ Recommends primary structure + alternatives
→ Live Oak Bank called out as specialist lender for laundromats/car wash
→ H1B flags noted (GM salary needed, passive investor requirements)
```

### Example: Day 1 workflow
```
09:00 User: "scan for deals"
      → 3 deals found, 2 pass filters, 1 filtered (franchise)
      → 2 deals ingested as DISCOVERED

09:15 User: "analyze wash and win laundromat"
      → 11-section memo: 3.67x multiple (FAIR), Genuine Retiree seller, excellent absentee fit
      → Verdict: PURSUE WITH CONDITIONS
      → State → ANALYZED

09:30 Automatic: /market-intel adds context
      → Charlotte NC = LOW risk, laundromat market STABLE, competitive density MODERATE
      → Verdict: GO

09:45 User: "is Jim Patterson legit?"
      → /broker-vetter: TIER 2, score 16/20, IBBA member, 2 laundromat case studies, proceed

10:00 User: "contact broker"
      → /broker-comm drafts email requesting CIM + NDA
      → User approves draft
      → Email sent
      → State → CONTACTING

10:05 User: "financing on this deal"
      → /financing: SBA 7(a) 90%, DSCR 1.87x, equity $38.5K, cash-on-cash 127%
      → Verdict: STRONG PASS, SBA-eligible

11:00 Awaiting broker reply...
```

---

## Session Start

Every session begins with CLAUDE.md auto-reading `data/deals.json` and presenting:

```
ACTIVE DEALS DASHBOARD
──────────────────────────────────────────────
Deal A  │ Laundromat, Decatur GA  │ ANALYZED   │ 3 days  │ → Shortlist?
Deal B  │ Car Wash, Kennesaw GA   │ CONTACTING │ 18 days │ ⚠ No reply — follow up
──────────────────────────────────────────────
What do you want to focus on today?
```

Flags stale deals (>30 days in any state) and deals awaiting follow-up (>14 days in CONTACTING without reply).

---

## Key Design Decisions

**1. Conservative by default**
Every skill is tuned to under-include rather than over-include. Better to miss a deal than pursue a bad one.

**2. H1B visa constraints embedded throughout**
Every deal evaluation memo includes an absentee viability score. Any deal requiring active operation flags an immigration attorney consultation.

**3. Evidence-grounded, not recall-based**
Broker discovery pulls from authoritative directories, not LLM memory. Every positive claim requires a verifiable URL.

**4. Disqualifiers are absolute**
One red flag on a broker → out, regardless of positive signals. One franchise in a deal → auto-rejected.

**5. Seller psychology is core, not optional**
The "Seller's Axe" section in every deal memo decodes the seller's real motivation — retirement vs burnout vs capex cliff vs "something wrong." Behavioral analysis informs negotiation position.

**6. Financing runs in parallel**
The financing track never blocks deal progression. You don't wait for financing approval before advancing a deal.

---

## Approval Gates

These actions **require explicit user confirmation** before executing:

- Sending any email (broker-communicator creates drafts only)
- Moving a deal to PASSED
- Scheduling any meeting
- Changing market scan filters
- Any outbound communication

---

## Disclaimer

This pipeline provides analysis and decision support. It does **not** provide legal advice. Consult qualified immigration and business attorneys before completing any acquisition. SBA eligibility assessments are analytical, not guarantees.
