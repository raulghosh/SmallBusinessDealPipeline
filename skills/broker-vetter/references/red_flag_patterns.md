# Red Flag Pattern Catalog

Specific detection patterns for lemon brokers. Each pattern includes what to look for, why it matters, and specific tests to run.

---

## PATTERN 1: Review Farming

### What it looks like
- All reviews are 5-star (no 4s, no 3s, no 1s)
- Review count is low (<25 total)
- Reviews cluster in time (many posted within a narrow window)
- Reviewer profiles have only 1 review each
- Identical or near-identical phrasing across reviews ("Great experience! Highly recommend!")
- Reviews praise services the broker doesn't actually provide
- Reviews reference cities/states outside the broker's operating area

### Specific tests
1. **5-star concentration test:** If 100% of reviews are 5-star AND count <25 → flag
2. **Temporal clustering test:** If >40% of reviews were posted within a 30-day window → flag
3. **Single-review reviewer test:** If >60% of reviewers have posted only 1 lifetime Google review → flag
4. **Phrasing similarity test:** If 2+ reviews contain identical 5+ word phrases → flag
5. **Geo mismatch test:** If reviews reference states the broker doesn't list as a service area → flag

### Why it matters
Review farming is a learned, deliberate behavior. A broker willing to fabricate customer voice is willing to fabricate deal representations.

---

## PATTERN 2: SEO-Heavy Generic Content

### What it looks like
- Homepage headline: "#1 Business Broker in [State]!" with no citation
- Meta description stuffed with "best", "top-rated", "trusted"
- Content mentions every possible industry without depth in any
- City pages are templated ("Buy a business in Atlanta. Buy a business in Macon. Buy a business in Savannah.") with identical body copy
- Blog posts are generic ("5 Tips for Selling Your Business") with no specific deal examples
- Heavy use of trust badges that link to nothing (or link to the badge publisher, not verification)

### Specific tests
1. **Unsubstantiated superlative test:** Does homepage use "#1", "best", "top" without citing source? → flag
2. **Template-city test:** Do city pages share >80% identical content? → flag
3. **Blog specificity test:** Out of last 5 blog posts, how many reference specific deals/clients (even anonymized)? If 0 → flag
4. **Trust badge audit:** Click every trust badge. Do they link to verification pages? If no → flag

### Why it matters
SEO-optimized generic content signals marketing investment without operational substance. Good brokers don't need to game Google; their deals and referrals bring them clients.

---

## PATTERN 3: Outdated / Broken Website

### What it looks like
- Copyright footer year is 2+ years old
- "Latest news" / blog last updated 18+ months ago
- Deal pages return 404
- "Recent Listings" page shows deals from 2+ years ago
- Mobile version broken or missing
- SSL certificate expired or invalid
- Contact form broken
- Social media links go to dead accounts

### Specific tests
1. **Footer year test:** Current year minus footer year. If >1 → concern, if >2 → disqualify
2. **Blog freshness test:** Date of most recent blog post. If >18 months → concern, if >24 months → disqualify
3. **404 sweep test:** Click 10 internal links. If >2 return 404 → disqualify
4. **SSL test:** Does https:// load without browser warning? If no → disqualify
5. **Mobile test:** Does site render on mobile without broken layout? If no → concern

### Why it matters
A broker who can't maintain their own website in 2026 cannot be trusted to manage a 6-month deal process with dozens of moving parts. Operational hygiene is observable.

---

## PATTERN 4: Anonymous Operations

### What it looks like
- "Our Team" page has no individual names or photos
- Team photos are stock images (reverse image search positive)
- No LinkedIn presence for named brokers
- Phone number is Google Voice / VOIP
- Office address is a UPS Store / mailbox service
- No physical office (WeWork address alone is not disqualifying, but combine with other signals)

### Specific tests
1. **Named-broker test:** Does site list ≥2 named humans with bios? If no → disqualify
2. **Stock photo test:** Reverse image search team photos via Google Lens. If matches stock image → disqualify
3. **LinkedIn cross-check test:** For each named broker, does LinkedIn profile exist? Does LinkedIn name/role match site? If 0/3 match → concern, if team photo doesn't match LinkedIn photo → disqualify
4. **Address test:** Google street view the office address. Is it a real office, a mailbox service, or a residence? Mailbox → disqualify
5. **Phone carrier test:** Call the number. Is it answered professionally? Does voicemail identify the firm?

### Why it matters
Anonymity protects identity, which protects a churn-and-burn broker from accountability. Legitimate brokers publicize their team because reputation IS the business.

---

## PATTERN 5: No Deal Transparency

### What it looks like
- Zero case studies on website
- "Testimonials" are all anonymized ("J.S., business owner")
- No references provided even when asked
- Won't name any past clients even with permission
- "Deals Closed" counter on homepage but no detail behind it
- Dollar volume claims without verification

### Specific tests
1. **Case study count test:** How many case studies with specific business names, verticals, and timeframes exist? If 0 → disqualify
2. **Testimonial attribution test:** Are testimonials attributed to real names + companies or anonymized? 100% anonymized → concern
3. **Reference test:** Ask for 3 client references. Do they provide? If refuse → disqualify
4. **Claim verification test:** If broker claims "$50M closed in 2023", can you find evidence of even one $5M+ deal? If no → concern

### Why it matters
You cannot verify track record without transparency. A broker proud of their track record shares it. A broker hiding it is hiding something.

---

## PATTERN 6: Predatory or Opaque Fee Structures

### What it looks like
- Refuses to disclose fee structure without engagement letter
- Upfront retainer >$5K with vague deliverables
- Success fee >12% on deals <$2M (industry norm: 8-12%)
- Non-refundable "marketing fees"
- Tail provisions extending 24+ months post-engagement
- Same firm represents both buyer and seller on same deal (undisclosed)
- "Exclusive" engagement required for basic intake conversation

### Specific tests
1. **Fee disclosure test:** Ask fee structure over email before engagement. Do they answer? If refuse → concern
2. **Retainer test:** Is upfront retainer required? If >$5K → concern, if non-refundable → disqualify
3. **Success fee test:** For $250K-$3M range, is success fee 8-12%? If >12% → concern, if >15% → disqualify
4. **Tail provision test:** How long does the tail last post-engagement? If >18 months → concern
5. **Dual agency test:** Do they represent buyers AND sellers on the same deals? If yes + undisclosed → disqualify

### Why it matters
Fee structure reveals incentive alignment. Opaque or predatory fees mean the broker extracts value from friction, not deal completion.

---

## PATTERN 7: Fake Awards and Credentials

### What it looks like
- "Award" from an org that only exists to sell awards
- Award from aggregator sites ("Top 10 Best" lists behind a paywall)
- Certifications from unaccredited "institutes"
- Self-published rankings
- Awards with no year cited (evergreen meaninglessness)

### Specific tests
1. **Award provenance test:** Click through to the awarding org. Is it a legitimate industry body? If .com with rankings in URL → flag
2. **Aggregator test:** If award source is "BestOf[City].com" or similar → discount
3. **Evergreen award test:** Does the award cite a specific year? If no → flag
4. **Pay-to-play test:** Does the awarding org charge to be considered / listed? If yes → disqualify as positive signal

### Why it matters
Real awards come from peer organizations. Fake awards come from pay-to-play aggregators.

---

## PATTERN 8: High Turnover / Churn Signals

### What it looks like
- Team listings change frequently (LinkedIn shows different brokers month-to-month)
- High-profile team member departures without announcements
- "Our team is growing!" language masking churn
- Glassdoor reviews mention high turnover or commission disputes
- Former broker reviews complain about deal/commission disputes

### Specific tests
1. **Team stability test:** Compare current team to archived Wayback Machine snapshot from 12 months ago. What's the retention rate?
2. **Glassdoor test:** Does firm have Glassdoor reviews? What's the pattern on turnover mentions?
3. **Former employee check:** LinkedIn search for past employees. What do they say in their summaries?

### Why it matters
Broker firms with high turnover either mistreat brokers (operational red flag) or churn through brokers trying to close quickly (incentive red flag). Either way, your deal gets deprioritized.

---

## SUMMARY: Decision Matrix

| Pattern | Single Instance | Multiple Indicators |
|---------|-----------------|---------------------|
| Review farming | Concern | Disqualify |
| SEO spam language | Concern | Disqualify |
| Outdated website | Concern if <18mo, DQ if >24mo | Disqualify |
| Anonymous ops | Concern (some indicators) | Disqualify |
| No deal transparency | Automatic Disqualify | Disqualify |
| Predatory fees | Concern | Disqualify |
| Fake awards | Discount that signal | No positive credit |
| High turnover | Concern | Disqualify |

**Rule of thumb:** Any TWO concerns = disqualify. Any ONE disqualifier = disqualify immediately.
