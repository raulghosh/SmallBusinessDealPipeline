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

| Stage | Trigger | Skill | State Change |
|-------|---------|-------|-------------|
| Vet brokers | "find brokers" / "vet broker" / "is [X] legit" | `/broker-vetter` | None (pre-pipeline) |
| Monthly scan | "scan" / "find deals" | `/market-scan` | → DISCOVERED |
| Deal evaluation | User selects deals to analyze | `/deal-evaluator` | → ANALYZED |
| Market overlay | Auto after evaluation | `/market-intel` | Stays ANALYZED |
| Shortlist | User reviews memos | Manual decision | → SHORTLISTED |
| Contact broker | User approves | `/broker-comm` | → CONTACTING |
| Track replies | "check for replies" | `/broker-comm` | → IN_DILIGENCE |
| Financing | Any SHORTLISTED+ deal | `/financing` | Parallel track |
| Pass | User decides to walk | Manual update | → PASSED |

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

### `/market-scanner` — Deal Discovery *(skeleton — in development)*
Will search BizBuySell, BizQuest, and Gmail broker emails for new listings matching buyer filters. Auto-ingests to `data/deals.json` as `DISCOVERED`.

---

### `/broker-comm` — Broker Communication *(skeleton — in development)*
Drafts outreach emails, tracks replies, extracts facts from broker responses, flags answers that dodge diligence questions. Gmail integration via MCP.

---

### `/market-intel` — Market Intelligence *(skeleton — in development)*
Enriches analyzed deals with regional economic context, industry macro trends, and competitive landscape. Auto-runs after `/deal-evaluator`.

---

### `/financing` — Financing Advisory *(skeleton — in development)*
Evaluates SBA 7(a) eligibility, seller financing viability, and loan preparation. Runs in parallel — never blocks deal progression.

---

## Repository Structure

```
SmallBusinessDealPipeline/
├── CLAUDE.md                          # Pipeline orchestrator (session start, skill routing, state machine)
├── data/
│   ├── deals.json                     # Pipeline state (all deals, history, memos, financing)
│   └── memos/                         # Saved deal evaluation memos
├── skills/
│   ├── broker-vetter/                 # Broker identification and vetting
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── scoring_rubric.md
│   │   │   ├── directory_sources.md
│   │   │   ├── red_flag_patterns.md
│   │   │   └── vertical_specialists.md
│   │   └── evals/evals.json
│   ├── deal-evaluator/                # 11-section deal analysis
│   │   ├── SKILL.md
│   │   ├── references/                # 8 reference files
│   │   └── evals/evals.json
│   ├── market-scanner/                # (In development)
│   ├── broker-communicator/           # (In development)
│   ├── market-intelligence/           # (In development)
│   └── financing-advisor/             # (In development)
└── .gitignore
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
