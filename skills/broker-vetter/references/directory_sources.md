# Broker Directory Sources (Authoritative)

The Discovery phase MUST pull candidates from these sources. Do not invent broker names from memory.

---

## TIER 1 SOURCES (Start Here)

### 1. IBBA (International Business Brokers Association)
- **URL:** https://www.ibba.org
- **Directory:** https://www.ibba.org/find-a-business-broker/
- **What to extract:** Name, firm, city, state, CBI (Certified Business Intermediary) status
- **Why authoritative:** Paid membership ($395/yr minimum), code of ethics, continuing education requirements
- **Filters to apply:** State = GA/FL/AL/TN/NC/SC
- **Notes:** CBI certification is a meaningful signal (requires coursework + exam + ethics agreement)

### 2. M&A Source
- **URL:** https://www.masource.org
- **Directory:** https://www.masource.org/find-an-advisor
- **What to extract:** Name, firm, specialization, M&AMI (Merger & Acquisition Master Intermediary) status
- **Why authoritative:** Focus on lower-middle-market ($1M-$100M deals), higher bar than IBBA
- **Filters to apply:** Deal size fit ($250K-$3M is at lower end of their focus)
- **Notes:** M&AMI is the senior credential

### 3. GABB (Georgia Association of Business Brokers)
- **URL:** https://www.gabb.org
- **Directory:** https://www.gabb.org/find-a-broker/
- **What to extract:** Name, firm, city, years of membership
- **Why authoritative:** State-specific, active events, peer-nominated awards (Multi-Million Dollar Club)
- **Filters to apply:** GA-focused, but some GABB members work across FL/AL/TN/NC/SC
- **Notes:** Check for award history on a separate GABB awards page

### 4. State Licensing Boards

Business broker licensing requirements vary by state. For each target state:

#### Georgia
- **Status:** NO separate business broker license required
- **Workaround:** Check Georgia Real Estate Commission (https://grec.state.ga.us) — most GA business brokers hold real estate licenses
- **Search:** https://grec.state.ga.us/PublicRosters

#### Florida
- **Status:** NO separate business broker license (but if they sell real estate as part of the deal, need FL real estate license)
- **Check:** https://www.myfloridalicense.com/LicenseDetail.asp

#### Alabama
- **Status:** No separate business broker license
- **Check:** Alabama Real Estate Commission https://arec.alabama.gov

#### Tennessee
- **Status:** No separate business broker license
- **Check:** Tennessee Real Estate Commission https://www.tn.gov/commerce/regboards/trec.html

#### North Carolina
- **Status:** No separate business broker license
- **Check:** NC Real Estate Commission https://www.ncrec.gov

#### South Carolina
- **Status:** No separate business broker license (but may need real estate license for deals with real estate)
- **Check:** SC Real Estate Commission https://llr.sc.gov/re/

**General approach:** Since most target states don't require a dedicated broker license, use real estate license records to verify years active + disciplinary actions.

---

## TIER 2 SOURCES (Supplementary)

### 5. BizBuySell "Top Brokers"
- **URL:** https://www.bizbuysell.com/business-brokers
- **Filters:** State + specialty (if applicable)
- **What to extract:** Name, firm, active listings count, closed listings (if displayed), profile link
- **Why useful:** Platform-validated activity (they actually list deals)
- **Caution:** BizBuySell listing does NOT validate quality, only activity. Cross-reference with Tier 1.

### 6. BizQuest Broker Directory
- **URL:** https://www.bizquest.com/business-brokers
- **What to extract:** Name, firm, state, listings count
- **Why useful:** Secondary platform, sometimes has brokers not on BizBuySell
- **Caution:** Less rigorous verification than BizBuySell

### 7. Axial (Deal Flow Platform)
- **URL:** https://www.axial.net
- **Access:** May require registration; primarily serves sponsors/PE but has broker visibility
- **What to extract:** Broker league tables, closed deal counts by quarter
- **Why useful:** Transparent deal flow visibility — the gold standard for verifying "closed deals" claims
- **Caution:** Skews to larger deals ($5M+); may not cover $250K-$3M range well

---

## TIER 3 SOURCES (Cross-Reference Only)

### 8. Google Business Profiles
- **Purpose:** Verify office address, phone, photos, review patterns
- **What to check:** Physical office exists, photos match firm site, review recency
- **Caution:** Do NOT score based on Google reviews alone — easy to game

### 9. LinkedIn Company Pages
- **Purpose:** Verify team size, tenure, employee count
- **What to check:** Employee count matches "Our Team" page, individual brokers have LinkedIn presence
- **Caution:** LinkedIn is self-reported; verify cross-platform

### 10. Better Business Bureau (BBB)
- **URL:** https://www.bbb.org
- **What to check:** Rating, complaint history, years in business, accreditation
- **Caution:** BBB ratings can be gamed via payment for accreditation. Use as one signal, not the signal.

### 11. Trade Journal Archives
- **Sources:** International Carwash Association, Coin Laundry Association, ISSA (janitorial)
- **Purpose:** Verify vertical specialization claims
- **What to check:** Broker quoted or published in trade journals? Attended their conferences?

---

## DATA COLLECTION CHECKLIST (Per Candidate)

For each broker pulled from directories, record:

```
Name:
Firm:
City, State:
Source of discovery: [IBBA/M&A Source/GABB/BizBuySell/etc.]
Source URL:
CBI certification: Y/N + URL
M&AMI certification: Y/N + URL
Years active: [from state registry + URL]
Website URL:
BizBuySell profile URL (if any):
LinkedIn firm page URL:
Google Business profile URL:
BBB profile URL:
```

This is the "candidate pool." It goes into Phase 2 (enrichment).

---

## ANTI-PATTERNS (Do NOT Use)

These sources are NOT authoritative:
- ❌ "Top 10 Business Brokers in Georgia" blog posts on generic sites
- ❌ Reddit threads recommending brokers (anecdotal, low verification)
- ❌ Yelp "Best Of" lists (pay-to-play in many categories)
- ❌ Facebook group recommendations (unverifiable, prone to affiliate bias)
- ❌ Broker-aggregator sites without membership vetting
- ❌ Any site whose URL contains "rankings" or "top-rated" in the domain name

**Rationale:** These sources often charge for placement or upvote based on advertising relationships, not deal quality.
