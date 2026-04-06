# Communication Protocol — Broker Interactions

This document governs the timing, sequencing, tone, and logging of all broker communications.
All rules here take precedence over ad-hoc judgment. When in doubt, default to conservative.

---

## Timing Rules

| Elapsed Time | Condition | Required Action |
|-------------|-----------|-----------------|
| 0 days | Deal moved to SHORTLISTED | Draft initial inquiry within 48 hours |
| 5 business days | No broker reply after initial email | Draft NDA Follow-Up (Template 2) |
| 7 calendar days | No broker reply after follow-up | Flag in CHECK_REPLIES output |
| 14 calendar days | No reply to any outreach | Escalate to user: pass or wait decision required |
| 21 calendar days | Still no reply | Recommend PASSED unless user provides compelling reason to wait |
| 3-5 business days | After NDA signed by buyer | CIM should be expected — if not received, follow up |
| After CIM received | — | Send Post-CIM Questions (Template 3) within 48 hours |
| After questions sent | 7 days no answer | Send Follow-Up Nudge (Template 8) |

**Business days definition:** Monday–Friday, excluding US federal holidays.

---

## Communication Sequencing

The standard sequence for a deal from SHORTLISTED to READY_TO_MEET is:

### Step 1: Initial Inquiry + NDA Request (SHORTLISTED → CONTACTING)
- Send Template 1 (Initial Inquiry) — personalized with listing details
- NDA request is always embedded in the first email
- Goal: get broker to send NDA and confirm listing is still active

### Step 2: NDA Execution (Buyer-Side, Off-Platform)
- User signs NDA — this happens outside this skill (attorney or direct)
- No email needed from this skill for the NDA signing itself
- After signing, CIM should arrive within 3-5 business days
- If CIM does not arrive in 5 business days: use Template 2 (NDA Follow-Up) or Template 8
  (Follow-Up Nudge) referencing the pending CIM

### Step 3: CIM Review + Post-CIM Questions (Begin IN_DILIGENCE)
- After CIM is received and reviewed, send Template 3 (Post-CIM Questions)
- Pull 6-8 targeted questions from `diligence_question_bank.md`
- Limit to 10 questions maximum per email — do not overwhelm the broker
- Group questions by category (Financials, Operations, Customers, Lease)
- Questions must be specific to the deal — never copy-paste generic boilerplate without
  customizing with deal-specific numbers or details

### Step 4: Iterative Q&A (Stay in IN_DILIGENCE)
- If answers raise new questions, send a follow-up question set (Template 3 format again)
- Limit to 2-3 rounds of questions before requesting a call
- After 2 rounds of unanswered questions in a row: use Template 8 (Follow-Up Nudge)

### Step 5: Serious Interest Signal (IN_DILIGENCE → READY_TO_MEET preparation)
- When diligence is substantively complete, send Template 4 (Serious Interest Letter)
- This signals readiness for the seller intro call
- Run SATISFACTION_CHECK mode before sending this template

### Step 6: Seller Intro Call Confirmation
- Broker arranges a 3-way intro call (seller + broker + buyer)
- Confirm logistics with Template 5 (Request for Seller Meeting)
- Deal state moves to READY_TO_MEET once this call is scheduled and confirmed

### Step 7: Exit Paths (Any State → PASSED)
- Soft Pass (Template 6): neutral reason, preserve relationship, broker is trustworthy
- Hard Pass (Template 7): broker was difficult, relationship not worth preserving
- Both require user approval before drafting and before sending

---

## Tone Guidelines

**The buyer's brand in every email:**
- Serious acquirer who moves with purpose — not curious, not speculative
- Analytical and prepared — reference specific details from the listing
- Concise and respectful of the broker's time — brokers deal with many inquiries
- Professional but not stiff — a brief, natural tone is better than formal corporate language

**What signals seriousness to a broker:**
- Referencing specific numbers from the listing ($340K SDE, not "the SDE figure")
- Asking targeted, non-obvious questions (not basic questions answerable by reading the listing)
- Offering availability quickly ("I'm available Tuesday or Wednesday this week")
- Having financing conversations in parallel (mention when appropriate)
- Sending follow-ups on schedule — not chasing erratically

**What signals a tire-kicker to a broker (avoid these at all costs):**
- Overly long first emails with extensive background on the buyer
- Broad, generic questions that show the buyer didn't read the listing
- "Can you tell me about the business?" — anything that could be answered by reading the CIM
- Asking for everything in the first email (NDA + CIM + financials + seller contact)
- Missing scheduled follow-up windows and then reappearing weeks later without explanation

**Word count targets:**
- Initial inquiry: 150-200 words
- NDA Follow-Up: 50-75 words
- Post-CIM Questions: 200-300 words (questions plus intro/close)
- Serious Interest Letter: 150-200 words
- Follow-Up Nudge: 50-75 words
- Soft Pass: 100-150 words
- Hard Pass: 50-75 words

**Never negotiate in email.** If a broker or seller brings up price or terms in writing,
acknowledge receipt and say you'd like to discuss on a call. Email negotiations produce weak
outcomes and create a paper trail of concessions.

---

## Red Flags in Broker Communications

The following patterns in broker responses should be flagged in the CHECK_REPLIES output and
brought to the user's attention immediately. They do not automatically kill a deal but require
user awareness.

### Red Flag 1: Substantial Upfront Non-Refundable Fee
- Broker asks for a meaningful non-refundable retainer ($5K+) before sharing any information
- Standard practice is success-fee-only or a small refundable retainer for serious buyers
- Action: Flag to user. Consider vetting broker more carefully via /broker-vetter.

### Red Flag 2: Persistent "Multiple Offers" Pressure
- Broker claims "multiple offers on the table" in response to every substantive question
- Legitimate brokers use this occasionally; systematic use is a pressure tactic
- Action: Flag to user. Advise buyer not to rush — deals rarely close on false urgency.

### Red Flag 3: Proof-of-Funds Before Basic Info
- Broker insists on seeing proof of funds before sharing the CIM or even basic details
- This is unusual at the inquiry stage — standard is NDA then CIM, not POF first
- Some exception for very high-demand deals, but skepticism is warranted at this stage
- Action: Flag to user. If broker demands POF before NDA, ask why and report the exchange.

### Red Flag 4: Evasiveness on Lease Terms or Staffing
- Broker consistently deflects questions about the lease (expiry, rent, assignment rights)
- Broker is vague or contradictory about the management team or whether employees will stay
- These two topics are among the most deal-critical for an absentee buyer
- Action: Flag specifically in communications log. Escalate to SATISFACTION_CHECK gap list.

### Red Flag 5: Revenue Answers, SDE Dodging
- Broker readily discusses gross revenue but consistently deflects or is vague on SDE
- SDE (owner's earnings after add-backs) is the key valuation metric — opacity is a signal
- Common broker tactic: inflate revenue-based narrative when SDE is weak
- Action: Flag in output. Require SDE confirmation with add-back detail before advancing.

### Red Flag 6: Listing Listed Much Longer Than Average
- If a deal has been on the market significantly longer than typical (6+ months for this size)
  and the broker has no good explanation, the market may have already rejected it
- Action: Ask broker directly ("How long has this been listed and what has the buyer interest
  been like?") — their answer is informative regardless of what they say.

---

## Communication Logging Schema

Every communication — outbound or inbound, draft or sent — must be logged to the deal's
`communications` array in `data/deals.json`.

```json
{
  "date": "YYYY-MM-DD",
  "direction": "outbound | inbound",
  "type": "inquiry | nda | cim_request | questions | interest_letter | meeting_request | pass | follow_up",
  "subject": "Exact email subject line",
  "summary": "1-2 sentence summary of what the email said or what the broker replied",
  "status": "draft | sent | received",
  "gmail_thread_id": "Gmail thread ID if available — critical for future thread reads"
}
```

**Field rules:**
- `date`: The date the draft was created (for outbound) or the date received (for inbound)
- `direction`: `outbound` for emails from the buyer, `inbound` for broker/seller replies
- `type`: Use the most specific type. `inquiry` = first contact. `questions` = diligence Q&A.
  `pass` = any soft or hard pass email. `follow_up` = any nudge or re-engagement.
- `status`:
  - `draft` = Gmail draft created, not yet sent
  - `sent` = User confirmed the email was sent (manual confirmation required)
  - `received` = Inbound email detected via gmail_search_messages or gmail_read_thread
- `gmail_thread_id`: Store this from the first email sent. Required for reading the full
  thread in CHECK_REPLIES and SATISFACTION_CHECK modes. If not yet known, leave as `null`.

**When to log:**
- Immediately after `gmail_create_draft` is called successfully → log with `"status": "draft"`
- When user confirms they've sent an email → update `"status"` to `"sent"`
- When an inbound reply is detected → log the inbound message with `"direction": "inbound"`

---

## Broker Vetting Gate

Before sending ANY first contact email to a broker, confirm that broker has been vetted:

1. Check deals.json for a `broker_vetting` field on the deal, OR
2. Check the `/broker-vetter` skill's output in conversation history

If no vetting record exists:
- Display this warning: "Broker [Name] at [Firm] has not been vetted yet. Sending outreach
  to an unvetted broker can waste months on a deal with a problem broker. Recommend running
  /broker-vetter on [Broker Name] first."
- Ask: "Would you like to vet this broker before reaching out, or proceed anyway?"
- If user proceeds anyway: log this decision in the deal notes. Do not block — user is
  the decision-maker.

---

## Multi-Deal Hygiene

When multiple deals are in CONTACTING or IN_DILIGENCE simultaneously:

- Never mix up broker names, listing details, or thread context between deals
- Load each deal's full communications history before drafting for it
- In CHECK_REPLIES mode, always show a summary table for ALL active deals — not just the
  one the user asked about. Broker situations evolve in parallel.
- If two deals are with the same brokerage (but different brokers), treat them independently.
  Do not reference one deal when communicating about another.

---

## Escalation Rules

| Situation | Escalation Action |
|-----------|------------------|
| Broker silent >14 days | Present pass vs. wait decision to user explicitly |
| Broker requests non-refundable fee | Flag before any response is drafted |
| Broker reveals material fact not in CIM | Log immediately, suggest deal re-evaluation |
| Broker pressures for quick LOI | Flag pressure tactic, advise user not to rush |
| Broker won't answer SDE add-back questions | Flag before SATISFACTION_CHECK proceeds |
| Seller appears to be same person as landlord | Flag — requires separate lease pricing |

---

## What Happens Off-Platform (User Handles Directly)

The following steps are NOT handled by this skill and require the user to act independently:

- Signing the NDA (user reviews and executes the NDA document directly)
- Any price negotiation calls (this skill does not draft negotiation scripts for voice)
- LOI drafting (handled by the user's attorney or a separate LOI skill if built)
- Wire transfers, escrow instructions, or closing mechanics
- Any interaction with the seller that does not go through the broker

When one of these steps occurs, the user should inform the skill so the communications log
can be updated accordingly.
