# Gmail Search Filters for Broker Listings

How to find broker emails containing business opportunities matching your buyer profile.

---

## Gmail Search Query Template

```
from:{broker_domain} subject:({keywords}) {vertical_filters} {exclude_keywords}
```

---

## By Vertical

### Janitorial / Commercial Cleaning
```
from:@bizbuysell.com OR from:@brokers.com subject:(janitorial OR cleaning OR contract) -franchise
from:@masource.org subject:(janitorial) -SaaS
```

**Broker domains to watch:** Bright Business Brokerage, VR Business Brokers, IBBA members

**Email subject keywords:**
- "janitorial opportunity"
- "cleaning service for sale"
- "contract cleaning available"
- "service-based cleaning business"

**Exclude keywords:**
- "franchise"
- "startup package"
- "business opportunity kit"

### Car Wash
```
from:{broker_domain} subject:(car wash OR carwash OR express wash OR tunnel) -franchise
subject:(automotive cleaning OR wash) -residential
```

**Email subject keywords:**
- "car wash for sale"
- "express wash available"
- "tunnel wash opportunity"
- "automated wash business"
- "self-serve wash"

**Exclude keywords:**
- "franchise"
- "affiliate"
- "detailing" (detailing is different from car wash)
- "dealership" (not a standalone car wash)

### Laundromat
```
from:{broker_domain} subject:(laundromat OR laundry OR coin laundry) -franchise
subject:(self-service laundry OR wash-and-fold) -pickup
```

**Email subject keywords:**
- "laundromat for sale"
- "coin laundry available"
- "self-service laundry"
- "laundromat business"

**Exclude keywords:**
- "franchise"
- "pickup and delivery" (WDF/P&D is more labor-intensive, poor for H1B absentee)
- "commercial laundry" (large-scale industrial laundry, different economics)

---

## Geographic Filters

Target states: GA, FL, AL, TN, NC, SC

### Query by state:
```
(location:GA OR location:FL OR location:AL OR location:TN OR location:NC OR location:SC)
OR (subject:(Georgia OR Florida OR Alabama OR Tennessee OR "North Carolina" OR "South Carolina"))
```

**State abbreviations to look for in email text:**
- GA, FL, AL, TN, NC, SC

---

## Broker Domain Patterns

Common broker firm email domain patterns:

| Firm Type | Example Domains |
|-----------|-----------------|
| National brokerages | @bizbuysell.com, @bizquest.com, @vrbusinessbrokers.com |
| Regional | @brightbrokers.com, @southeastbizbrokers.com |
| Independent | @{broker-last-name}business.com, @{city}brokers.com |
| Member directories | @masource.org, @ibba.org |

**Search tip:** If you get good leads from a specific broker, add their domain:
```
from:sarahb@brightbrokers.com subject:(available OR opportunity OR sale)
```

---

## Time Ranges

### Default search (last 30 days):
```
newer_than:30d
```

### First-time scan (all-time):
```
(no time filter — get everything, but user filters manually)
```

### Weekly check:
```
newer_than:7d
```

---

## Advanced Filters

### Exclude known franchisors:
```
-subject:(franchise OR FDD OR "franchise disclosure")
-from:franchisedevelopment@ OR franchisor@
```

### Include only specific file types (attachments):
```
has:attachment filename:(pdf OR xlsx OR doc)
```

### Exclude low-value noise:
```
-subject:("business opportunity kit")
-subject:("earn from home")
-subject:("passive income")
```

### Match specific asking price ranges:
This requires parsing email body (not pure Gmail syntax), but look for:
- "$250" and "$3" in same email (implies $250K-$3M)

---

## Email Parsing Notes

**Common patterns in broker emails:**

| Element | Pattern Example |
|---------|-----------------|
| Business type | "XYZ Janitorial Service" OR "ABC Car Wash" OR "DEF Laundromat" |
| Revenue | "Annual revenue: $350,000" OR "$350K TTM" |
| SDE/EBITDA | "Owner salary add-back: $30K" OR "Adjusted EBITDA: $105K" |
| Asking price | "Asking: $385,000" OR "$385K" |
| Location | "[City], [State]" OR "[Address], [State]" |
| Broker contact | Name, email, phone usually at signature |
| CIM link | "Link: [URL]" OR "Attached: confidential memo" |

**Red flags to note:**
- Missing revenue
- Missing asking price
- No broker contact info
- Generic "passive income" language (likely franchise bait)

---

## Testing Your Filters

**After setting up filters, test by:**

1. **Search in Gmail UI:** Paste the query into Gmail search, verify results are relevant
2. **Check sender:** Are emails from real brokers or spam aggregators?
3. **Spot-check subjects:** Do deal descriptions match your criteria?
4. **Test edge cases:** Try a franchise-heavy query (should return mostly franchises) to verify negative filters work

---

## Sample Saved Searches

**Quick search for all opportunities (monthly scan):**
```
from:@bizbuysell.com OR from:@bizquest.com
subject:(janitorial OR carwash OR laundromat)
(subject:GA OR subject:FL OR subject:AL OR subject:TN OR subject:NC OR subject:SC)
-subject:franchise
newer_than:30d
```

**Specific to favorite brokers (weekly):**
```
from:sarahb@brightbrokers.com
OR from:mike.chen@southeastbrokers.com
subject:(available OR opportunity OR sale)
```

**Laundromat deep-dive (one-time comprehensive search):**
```
subject:(laundromat OR "coin laundry" OR "self-service laundry")
(GA OR FL OR AL OR TN OR NC OR SC)
-franchise
-"wash and fold"
-"pickup and delivery"
```
