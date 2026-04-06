---
name: market-intel
description: >
  Add macro, regulatory, insurance, and competitive context to an evaluated deal.
  Auto-triggered by the orchestrator after deal-evaluator completes for any deal.
  User can also invoke manually. Triggers on "market check", "macro risk",
  "market overlay", "check market", "market intel", "market analysis",
  "check the market on this deal", "what's the market like for this deal".
  Does NOT re-evaluate the deal itself — it is an additive overlay only.
  Stays the deal in ANALYZED state (no state change).
---

# Market Intel Skill

## Purpose

Add macro context — regulatory environment, industry trends, insurance cost benchmarks,
and competitive density signals — to a deal that has already been evaluated by the
deal-evaluator. This skill answers the question: "Even if the deal financials pencil out,
is the market itself working for us or against us?"

This skill AUGMENTS the deal memo. It does NOT change the deal evaluator's verdict. The
Market Intel Verdict (GO / CAUTION / AVOID) is advisory input for the orchestrator and user
when deciding whether to advance a deal to SHORTLISTED.

---

## Trigger

**Auto-invoked** by the orchestrator immediately after `/deal-evaluator` completes for any
deal. No user action required in the normal flow.

**Manual triggers:**
- "market check on [deal name]"
- "macro risk on [deal name]"
- "market overlay"
- "check market"
- "market intel"
- "what's the market like for this deal?"
- "run market intel on [deal ID]"

**Deal state requirement:** Deal must be in ANALYZED state (i.e., deal-evaluator has run).
If the deal is DISCOVERED but not yet ANALYZED, surface a warning and ask if the user wants
to run deal-evaluator first.

---

## What This Skill Does NOT Do

- Does NOT re-analyze the deal financials or SDE.
- Does NOT revisit the deal evaluator's verdict.
- Does NOT change the deal's pipeline state (stays ANALYZED).
- Does NOT send any communications or contact any brokers.
- Does NOT guarantee SBA eligibility or immigration status compatibility — always defer to
  qualified attorneys for visa and legal questions.

---

## Reference Files to Read (in order)

Read these at the START of every market intel request:

1. `references/state_risk_profiles.md` — State-by-state regulatory, wage, and insurance
   environment for GA, FL, AL, TN, NC, SC.
2. `references/industry_trends.md` — Macro trends, consolidation dynamics, and risk factors
   for janitorial, car wash, and laundromat verticals.
3. `references/insurance_benchmarks.md` — Insurance cost benchmarks by vertical. Use to
   validate whether the deal's stated insurance costs are in range.
4. `references/competitive_signals.md` — Framework for assessing competitive density and
   identifying moats for each vertical.

---

## Input

The skill requires either:
- A **Deal ID** (e.g., `DISCOVERY-20260405-SB`) — reads directly from `data/deals.json`
- A **Deal name** — searches `data/deals.json` for the closest matching active deal

If neither uniquely resolves to a single deal, ask the user to clarify.

---

## 4-Phase Flow

### Phase 1: LOAD

1. Read `data/deals.json` and locate the target deal.
2. Extract: `business_type` (janitorial / car wash / laundromat), `state` (location state),
   and the existing `deal_evaluator_memo` (if present).
3. If the deal has already had `market_intel` run (a `market_intel` object exists on the
   record), surface the prior run date and ask the user if they want to re-run.
4. Identify the relevant metro area from the deal location (e.g., Atlanta for GA,
   Charlotte for NC, Nashville for TN, Jacksonville for FL, Birmingham for AL, Raleigh for NC).
5. Note any regulatory flags already raised in the deal evaluator memo — these provide
   context for Phase 2.

**Output of Phase 1:** Deal vertical, deal state (geography), deal metro, and awareness of
any flags already in the evaluator memo.

---

### Phase 2: RESEARCH

Pull macro context across four dimensions. For each dimension, reference the appropriate
file from the references folder. Apply only the section relevant to this deal's vertical
and geography — do not dump generic content.

#### 2a. Regulatory Environment
From `references/state_risk_profiles.md`:
- Extract the risk profile for the deal's state.
- Note: minimum wage trajectory and any scheduled increases (impacts labor cost for
  janitorial businesses with hourly workers).
- Note: vertical-specific regulations (car wash water recycling, chemical disposal,
  laundromat energy compliance, janitorial bonding/licensing requirements).
- Note: employment law environment (workers comp rates, at-will status, non-compete
  enforceability).
- Flag any pending legislation that could affect the vertical. If unknown, state "no
  pending legislation identified at time of analysis — recommend buyer's counsel verify."

#### 2b. Industry Trends
From `references/industry_trends.md`:
- Extract the trend section for this deal's vertical.
- Determine: is the vertical GROWING, STABLE, or DECLINING at the macro level?
- Check for consolidation risk: is this vertical being rolled up by PE-backed platforms?
  If yes, flag it explicitly — consolidation is a direct threat to independent operators.
- Identify 2-3 relevant tailwinds and 2-3 relevant headwinds for this vertical.

#### 2c. Insurance Cost Benchmarks
From `references/insurance_benchmarks.md`:
- Extract insurance benchmarks for this deal's vertical.
- Compare the deal's stated insurance costs (if available in the evaluator memo or the
  deal's financials) against benchmarks.
- If the deal's stated insurance is significantly below benchmarks, flag as a red flag —
  either coverage is dangerously thin or costs are being understated in the financials.
- Estimate realistic insurance cost as a percentage of revenue for this vertical.
- Note any state-specific insurance risks (e.g., hurricane property risk in FL, workers
  comp environment in specific states).

#### 2d. Competitive Density
From `references/competitive_signals.md`:
- Apply the competitive density framework for this deal's vertical and metro.
- Determine: THIN / MODERATE / SATURATED.
- Flag any moat signals present in the deal (from the evaluator memo or listing).
- Note consolidation indicators (e.g., national chains active in this metro, PE roll-up
  activity in the vertical).

---

### Phase 3: SYNTHESIZE

Produce the Market Intel Report (format specified below). Synthesize the four research
dimensions into:

- **Overall Market Risk:** LOW / MEDIUM / HIGH
  - LOW: Favorable regulatory environment + growing or stable industry + below-benchmark
    insurance costs + thin or moderate competitive density
  - MEDIUM: Any one dimension is unfavorable; otherwise manageable
  - HIGH: Two or more dimensions are unfavorable, or one dimension is severely unfavorable
    (e.g., market is SATURATED + vertical is DECLINING)

- **Market Intel Verdict:** GO / CAUTION / AVOID
  - GO: Overall market risk is LOW or MEDIUM with no severe headwinds. Market supports
    the deal proceeding.
  - CAUTION: Overall market risk is MEDIUM with one or more notable headwinds, or one
    dimension is HIGH (e.g., competitive density SATURATED). Buyer should account for
    market risk in valuation and due diligence.
  - AVOID: Overall market risk is HIGH, or multiple dimensions are severely unfavorable.
    Market fundamentals argue against this deal independent of financials.

**Key rule:** The Market Intel Verdict is INDEPENDENT of the deal evaluator's verdict.
A deal can receive "PURSUE WITH CONDITIONS" from deal-evaluator AND "CAUTION" from
market-intel — both assessments stand. The orchestrator and user synthesize them.

---

### Phase 4: APPEND

1. Write the `market_intel` object to the deal record in `data/deals.json`.
2. **Do NOT overwrite any other fields** — use a targeted write to append only the
   `market_intel` key on the matching deal object.
3. Also append the Market Intel Report block to the `deal_evaluator_memo` field if it
   exists, formatted as a new section titled `## MARKET INTEL OVERLAY`.
4. Log the run date as `date_run` in the market_intel object.

---

## Output Format

Produce the following structured Market Intel Report. Use headers exactly as shown.

---

### [DEAL NAME] — Market Intel Overlay
*Date: [today's date] | Run by: Market Intel Agent | Deal vertical: [vertical] | State: [state]*

---

#### MARKET INTEL SUMMARY

| Dimension | Rating | Notes |
|---|---|---|
| Overall Market Risk | LOW / MEDIUM / HIGH | [one-line rationale] |
| Regulatory Environment | LOW / MEDIUM / HIGH risk | [one key driver] |
| Industry Trend | GROWING / STABLE / DECLINING | [one key evidence point] |
| Insurance Outlook | [cost range as % revenue] | [any flags] |
| Competitive Density | THIN / MODERATE / SATURATED | [one-line characterization] |

**Market Intel Verdict: [GO / CAUTION / AVOID]**
*[One-sentence rationale for the verdict.]*

---

#### 1. REGULATORY ENVIRONMENT — [STATE]

**Risk Rating: LOW / MEDIUM / HIGH**

- **Business-friendliness:** [State's overall regulatory posture]
- **Minimum wage:** [Current rate and trajectory — especially important for janitorial]
- **Vertical-specific regulations:** [What applies to this business type in this state]
- **Employment law:** [At-will, workers comp rates, non-compete enforceability]
- **Insurance environment:** [State-specific context]
- **Pending legislation:** [Any known bills or initiatives affecting this vertical; or
  "None identified — recommend buyer's counsel verify before close."]

---

#### 2. INDUSTRY TREND — [VERTICAL]

**Trend: GROWING / STABLE / DECLINING**

[2-3 sentences on the macro trend for this vertical, specific to why it is trending this
direction. Reference the national picture and, if applicable, how the Southeast specifically
differs.]

**Consolidation / Roll-up Risk: YES / NO / WATCH**
[Is this vertical being rolled up by PE or national chains? If yes: how does it affect an
independent operator in this geography? If NO: is the window likely to close?]

**Tailwinds:**
- [Tailwind 1 — specific to vertical]
- [Tailwind 2]
- [Tailwind 3]

**Headwinds:**
- [Headwind 1 — specific to vertical]
- [Headwind 2]
- [Headwind 3]

---

#### 3. INSURANCE OUTLOOK

**Estimated annual insurance cost: [X-Y% of revenue]**

| Coverage Type | Benchmark Range | Deal's Stated Cost | Assessment |
|---|---|---|---|
| General Liability | $[low]-$[high]/yr | $[stated] or UNKNOWN | IN-RANGE / LOW / HIGH |
| Workers Compensation | [X-Y]% of payroll | [stated] or UNKNOWN | IN-RANGE / FLAG |
| [Other relevant coverage] | $[range] | [stated] or UNKNOWN | IN-RANGE / FLAG |

**Coverage Risks:**
- [Key coverage risk 1 for this vertical]
- [Key coverage risk 2]

**State-specific insurance notes:**
[Anything specific to this state that affects premiums or coverage availability — e.g.,
hurricane coverage in FL, specific workers comp rate environment.]

**Flag — Stated Insurance Below Benchmarks:** [YES / NO]
[If YES: "The deal's stated insurance cost of $X represents Y% of revenue, which is below
the [Z-Z%] benchmark range. This suggests coverage may be inadequate or costs are being
understated. Requires verification during diligence."]

---

#### 4. COMPETITIVE DENSITY

**Density: THIN / MODERATE / SATURATED**

[2-3 sentences describing the competitive landscape in this metro for this vertical. How
many competitors are present? Are national chains active? Is PE roll-up activity underway?]

**Moat Signals in This Deal:**
- [Any moat identified from the deal memo — e.g., long-term anchor contracts, premium
  location, established route ownership. If none: "No durable moat signals identified
  in this deal."]

**Competitive Risk:**
[What's the realistic threat from existing or new entrants? What would it take for a
competitor to take significant market share from this business?]

---

#### 5. MACRO TAILWINDS

- [Tailwind 1]
- [Tailwind 2]
- [Tailwind 3]

---

#### 6. MACRO HEADWINDS

- [Headwind 1]
- [Headwind 2]
- [Headwind 3]

---

#### 7. MARKET INTEL VERDICT

**[GO / CAUTION / AVOID]**

[2-4 sentences synthesizing all four dimensions. Explain the verdict in plain language.
Connect the verdict to specific deal facts where possible. Note what would change the
verdict — e.g., "If the buyer can verify long-term contracts covering 70%+ of revenue,
this CAUTION could shift to GO."]

**How this overlays with the deal evaluator's verdict:**
[One sentence on how the market intel verdict interacts with the evaluator's recommendation.
Example: "Deal evaluator recommended PURSUE WITH CONDITIONS. Market intel rates this CAUTION
due to competitive density. Together: proceed with margin-of-safety pricing and prioritize
contract verification in diligence."]

---

## JSON Schema — market_intel Object

When appending to `data/deals.json`, write exactly this structure:

```json
{
  "market_intel": {
    "date_run": "YYYY-MM-DD",
    "overall_market_risk": "LOW|MEDIUM|HIGH",
    "regulatory_risk": "LOW|MEDIUM|HIGH",
    "regulatory_notes": "...",
    "industry_trend": "GROWING|STABLE|DECLINING",
    "industry_trend_notes": "...",
    "insurance_outlook": {
      "annual_cost_estimate_pct_revenue": "X-Y%",
      "coverage_risks": ["...", "..."],
      "notes": "..."
    },
    "competitive_density": "THIN|MODERATE|SATURATED",
    "competitive_notes": "...",
    "tailwinds": ["...", "...", "..."],
    "headwinds": ["...", "...", "..."],
    "verdict": "GO|CAUTION|AVOID",
    "verdict_rationale": "..."
  }
}
```

**Write rules:**
- Use a targeted update — find the deal by ID, add the `market_intel` key only.
- Do not null out or overwrite: `deal_evaluator_memo`, `financials`, `operationals`,
  `broker`, `state_history`, or any other existing key.
- If a `market_intel` key already exists, overwrite it (re-run is intentional).
- Set `date_run` to today's date (ISO 8601 format: YYYY-MM-DD).

---

## Key Rules

1. **Augment, don't override.** This skill adds a second opinion — it does not change the
   deal evaluator's verdict or scoring. Never say "the deal evaluator was wrong."

2. **Verdict is advisory.** Market Intel Verdict (GO / CAUTION / AVOID) is one input to
   the orchestrator and user decision. It does not automatically advance or kill a deal.

3. **Flag pending legislation.** If any state has pending legislation that could affect
   the vertical (minimum wage increases, environmental regulation tightening, new bonding
   requirements), flag it. If nothing is identified: state that explicitly and recommend
   buyer's counsel verify.

4. **Always flag consolidation.** If the vertical is experiencing PE-backed roll-up
   activity, name it. Consolidation compresses margins and threatens route ownership for
   independent operators. It is never a minor observation.

5. **Insurance below benchmarks is a red flag.** If the deal's stated insurance costs are
   materially below the benchmark range for the vertical, flag it as a potential financial
   misrepresentation. This can mask lower-than-reported insurance coverage or inflated SDE.

6. **State-specific context matters.** Florida is not Tennessee. Apply state-specific
   regulatory and insurance context — don't apply generic national commentary.

7. **Be specific about metro areas.** "Georgia" is not specific enough. Atlanta is
   different from Savannah. Charlotte and Raleigh are different NC markets. Apply
   metro-level context from `references/state_risk_profiles.md` where available.

8. **Never make legal or visa promises.** Regulatory analysis is informational. Always
   note that the buyer should consult immigration counsel and a business attorney
   before relying on any regulatory assessment.

---

## Workflow Integration

```
[deal-evaluator completes]
         ↓
[market-intel auto-triggers]
  Phase 1: Load deal from deals.json
  Phase 2: Research regulatory + trends + insurance + competition
  Phase 3: Synthesize → Market Intel Report
  Phase 4: Append market_intel object to deals.json
         ↓
[Orchestrator presents combined memo to user]
  - Deal Evaluator verdict: PURSUE WITH CONDITIONS
  - Market Intel verdict: CAUTION
  - Together: user decides whether to SHORTLIST
```

The orchestrator presents both verdicts side by side. The user synthesizes them into a
final decision. Neither verdict alone is sufficient to advance or kill a deal — the user
holds the final approval gate.
