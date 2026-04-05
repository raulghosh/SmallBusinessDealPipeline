---
name: broker-vetter
description: Identify and score trustworthy business brokers for janitorial, car wash, and laundromat acquisitions in the Southeast US (GA, FL, AL, TN, NC, SC). Triggers on "find brokers", "vet broker", "broker list", "who should I work with", "is this broker legit", "top brokers in [state/vertical]". Produces a ranked list with evidence-backed scores and disqualifies lemons aggressively.
---

# Broker Vetter Skill

## Purpose
Produce a **conservative, evidence-grounded** ranked list of business brokers the buyer can trust. The goal is NOT to maximize the list size — it's to **eliminate lemons** first, then rank the survivors.

Core principle: **Missing a good broker costs little. Trusting a bad broker costs a deal.**

## The 4-Phase Flow

This skill does NOT ask an LLM to "name top brokers from memory." It grounds every candidate in verifiable external data, then scores.

### Phase 1: DISCOVERY (Scrape authoritative directories)
Do NOT generate broker names from memory. Pull from the sources in `references/directory_sources.md`:
- IBBA member directory
- M&A Source directory
- GABB directory (Georgia Association of Business Brokers)
- State licensing boards (GA, FL, AL, TN, NC, SC)
- BizBuySell "Top Brokers" by state
- Axial (if accessible)

**Output of Phase 1:** Candidate pool of 50-100 brokers with name, firm, state, source-of-discovery.

### Phase 2: ENRICHMENT (Gather evidence per broker)
For each candidate, collect:
- **Website snapshot**: Last updated, design quality, broken links, deal transparency pages
- **Reviews**: Google + BBB + Trustpilot + Yelp → count, average, recency spread, language patterns
- **Team**: Named brokers (LinkedIn profiles verifiable?), stock photos, tenure
- **Deal history**: Public case studies (specific!) vs generic testimonials
- **Licensing**: State record verification, years active
- **Specialization**: Do they mention janitorial/car wash/laundromat specifically?
- **Memberships**: IBBA, M&A Source, GABB (verify on those orgs' sites, not just broker claim)

Use WebSearch + WebFetch to gather this. Record source URLs for every claim.

### Phase 3: SCORING (Apply rubric to enriched data)
Apply `references/scoring_rubric.md`. One structured pass.

**Hard disqualifiers are applied FIRST.** Any negative signal → broker is OUT. No point-balancing.

Remaining brokers get scored 0-20 on positive signals. Only scores ≥12 make the shortlist.

### Phase 4: VARIANCE CHECK (Only for borderline cases)
For brokers scoring 10-13 (ambiguous zone):
- Re-score with fresh reasoning
- If the two scores disagree by >2 points, flag for human review
- If they agree, trust the score

Do NOT run variance check on clear winners (14+) or clear rejects (<10). Wasteful.

## Reference Files to Read (in order)

Read these at the START of every broker-vetting request:

1. `references/scoring_rubric.md` — The formal scoring system + disqualifier rules
2. `references/directory_sources.md` — Where to look for candidates (the grounded data)
3. `references/red_flag_patterns.md` — How to spot review farming, SEO spam, fake-ops brokers
4. `references/vertical_specialists.md` — Known specialists in target verticals + what to look for

## Inputs
The user provides:
- **Target state(s)** (e.g., "Georgia + neighboring states")
- **Target vertical(s)** (e.g., "janitorial" or "all three")
- **(Optional) Specific broker to evaluate** (single-broker mode)

## Output Format

### For list mode (find brokers):

```markdown
# Broker Shortlist — [State], [Vertical]

**Candidates evaluated:** [N]
**Disqualified:** [N] (red flags triggered)
**Shortlisted:** [N] (score ≥12)
**Borderline (flagged for your review):** [N]

---

## TIER 1: Trusted (Score 16-20)
### 1. [Broker Name] — [Firm], [State] · Score: X/20
**Evidence:**
- IBBA member (verified: [URL])
- 15 years active, licensed [state] (verified: [URL])
- 8 public case studies with specifics (URL)
- Named team of 4 with LinkedIn profiles
- Google reviews: 47 reviews, 4.7 avg, spread across 3 years
- **Specialization match:** Closed 3 laundromat deals per case studies

**Why you'd work with them:** [1-2 sentence summary]

---

## TIER 2: Qualified (Score 12-15)
[Same format]

---

## BORDERLINE (Your judgment needed)
### [Broker] · Score: 10-13, disagreement in variance check
**Strengths:** [list]
**Concerns:** [list]
**Recommendation:** Quick 15-min intro call before committing

---

## DISQUALIFIED (For your awareness)
Grouped by red flag triggered:

**Website broken / not updated (N brokers):**
- [Broker A] — website last updated 2021
- [Broker B] — multiple 404s on deal pages

**Review farming pattern (N brokers):**
- [Broker C] — 12 reviews all 5-star, 11 posted same week

**No named brokers / stock photos (N brokers):**
- [Broker D]

**"#1 broker" language without proof (N brokers):**
- [Broker E]

**No deal transparency (N brokers):**
- [Broker F]
```

### For single-broker mode (is this broker legit?):

```markdown
# Broker Evaluation: [Name]

**Verdict:** PROCEED / BORDERLINE / AVOID

**Score:** X/20 (breakdown below)

## Hard Signals
- [ ] IBBA / M&A Source / GABB member (verified)
- [ ] Named brokers w/ verifiable track record
- [ ] Public closed-deal case studies
- [ ] >10 years active (licensing verified)

## Medium Signals
- [ ] BizBuySell verified presence
- [ ] Awards from legitimate orgs
- [ ] Specialization matches target vertical

## Red Flags (Disqualifiers)
- [ ] Website broken or outdated
- [ ] Suspicious review patterns
- [ ] No named brokers
- [ ] "#1 broker" language without proof
- [ ] No deal transparency

## Evidence Trail
[Bullet list of every verified claim with source URL]

## Recommendation
[1 paragraph]
```

## Key Rules

1. **Never invent brokers.** If you can't find a verifiable URL for a broker, they don't exist for this list.
2. **Every positive signal needs a URL.** Claims without citations don't count.
3. **Disqualifiers are absolute.** A broker with 4 positives and 1 disqualifier is OUT, not borderline.
4. **Be conservative.** Under-include rather than over-include.
5. **Cite sources in output.** The user must be able to verify your reasoning.
6. **No bandwagoning.** Popularity on Reddit / forums is not evidence. Closed deals + memberships are.

## When NOT to Use This Skill
- User is asking about a specific DEAL, not a broker → use deal-evaluator
- User wants to draft a broker email → use broker-communicator
- User is asking about market trends → use market-intelligence
