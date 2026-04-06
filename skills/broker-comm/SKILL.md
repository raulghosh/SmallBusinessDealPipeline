---
name: broker-comm
description: >
  Manage all broker communications throughout the deal lifecycle — drafting outreach emails,
  tracking replies, sending follow-ups, and assessing diligence readiness. Triggers on "contact
  broker", "email broker", "check replies", "follow up", "draft email", "check for replies",
  "broker replied", "are we good on this deal", "ready to meet", "send follow-up", "reach out
  to broker", "has the broker responded", "nudge broker", "draft outreach". Creates DRAFTS ONLY
  — never sends any email without explicit user approval.
---

# Broker Communication Skill

## Purpose

Manage all outbound and inbound broker communication across the deal lifecycle. This skill
handles four distinct operating modes, each triggered by a different user intent. It never
sends an email autonomously — every outbound communication must be reviewed and approved by
the user before being saved as a Gmail draft.

This skill moves deals through: **SHORTLISTED → CONTACTING → IN_DILIGENCE → READY_TO_MEET**

## Critical Rules (Non-Negotiable)

1. **NEVER send emails.** This skill creates Gmail drafts only. User must explicitly approve
   before any draft is saved. Display the full draft text first and wait for approval.
2. **NEVER schedule calendar events.** You may suggest meeting time windows, but no calendar
   event may be created without explicit user instruction.
3. **NEVER reveal H1B/visa status** in any broker communication. That is handled by attorneys.
4. **NEVER reveal internal deal IDs** to brokers. Track internally only.
5. **Vet brokers before first contact.** If a broker has not been run through /broker-vetter,
   flag this and suggest vetting them before sending any outreach.
6. **Log every communication** to `data/deals.json` under the deal's `communications` array
   immediately after user approves a draft or after confirming a received message.

## Reference Files to Load (Load Before Running Any Mode)

Read these at the start of every broker-comm session:

1. `references/email_templates.md` — All email templates with subject lines, body copy, and
   personalization guidance. Required for DRAFT_INITIAL_OUTREACH, DRAFT_FOLLOW_UP, and
   SATISFACTION_CHECK modes.
2. `references/communication_protocol.md` — Timing rules, sequencing logic, tone guidelines,
   red flags in broker behavior, and the JSON schema for logging communications to deals.json.
3. `references/diligence_question_bank.md` — Bank of categorized diligence questions. Required
   for POST_CIM_QUESTIONS and SATISFACTION_CHECK modes.

Also read `data/deals.json` to load current deal states and communication histories before
acting in any mode.

## Operating Modes

### Mode 1: DRAFT_INITIAL_OUTREACH

**Triggers:** User says "contact broker for [deal]", "reach out to [deal]", "draft first email
for [deal]", "email the broker on [deal listing]"

**Pre-conditions to check:**
- Deal must be in SHORTLISTED state. If it's in an earlier state, halt and tell the user.
- Broker must have been vetted via /broker-vetter. If no vetting record exists in deals.json
  for this broker, warn the user and ask whether they'd like to vet first or proceed anyway.
- Check the deal's `communications` array — if an outbound inquiry already exists, do not
  draft a duplicate. Instead, tell the user and offer DRAFT_FOLLOW_UP mode.

**Process:**
1. Load the deal from deals.json — pull listing URL, broker name, broker email, business type,
   asking price, revenue, SDE, and any notes from the deal evaluation memo.
2. Load the Initial Inquiry template from `references/email_templates.md`.
3. Personalize the draft using specific details from the listing (business type, geography,
   specific financial stats). Generic emails get ignored by brokers.
4. Include NDA request in the initial email (always — per protocol).
5. Display the full draft to the user:
   - Subject line
   - Full body
   - Recipient (broker name + email)
   - Explain that this is a DRAFT — not yet saved to Gmail
6. Ask: "Shall I save this as a Gmail draft? (yes / edit first / cancel)"
7. On user approval: call `gmail_create_draft` to save to Gmail. Record the draft in
   deals.json communications array with `"status": "draft"`.
8. Tell the user: "Draft saved to Gmail. The deal state will move to CONTACTING once you send
   it. Let me know when you've sent it and I'll update the deal state."

**State transition:** SHORTLISTED → CONTACTING (only after user confirms the email has been
sent, not when the draft is created)

**Output format:**
```
## Draft: Initial Outreach — [Deal Name]

TO: [Broker Name] <[broker email]>
SUBJECT: [subject line]

---
[full email body]
---

Tone: [brief note on tone choices]
Personalization included: [list the deal-specific details woven in]

Ready to save as Gmail draft? (yes / edit first / cancel)
```

---

### Mode 2: CHECK_REPLIES

**Triggers:** User says "check for replies", "any broker responses?", "has [broker] replied?",
"check inbox for deal updates", "broker replied?"

**Process:**
1. Load deals.json — collect all deals in CONTACTING or IN_DILIGENCE state.
2. For each such deal, extract: broker name, broker email, last outbound email date,
   gmail_thread_id (if recorded).
3. Search Gmail for replies:
   - Use `gmail_search_messages` with queries like `from:[broker_email]` or
     `subject:[subject keywords]` for each deal.
   - If a thread_id is known, use `gmail_read_thread` to get the full thread.
4. Determine reply status for each deal:
   - **REPLIED**: Broker has sent at least one message since the last outbound.
   - **SILENT**: No reply found.
5. Calculate days since last outbound message for each deal.
6. Flag any deal silent for >7 days (nudge threshold) and >14 days (escalation threshold).
7. If a reply is found:
   - Summarize the reply content (subject, date, 1-2 sentence summary of what broker said).
   - Identify what action is needed: NDA to sign, CIM received, questions to answer, etc.
   - If broker sent CIM or NDA, note this explicitly.
   - Suggest next mode (e.g., "CIM received — ready to run DRAFT_FOLLOW_UP with questions?")
8. Update deals.json for any newly detected inbound messages (log them to communications array
   with `"direction": "inbound"`, `"status": "received"`).

**State transition:**
- If broker replied and info exchange has clearly begun → suggest moving deal to IN_DILIGENCE
  (user must confirm).

**Output format:**
```
## Broker Reply Check — [Date]

| Deal | Broker | State | Last Contact | Days Waiting | Status | Notes |
|------|--------|-------|-------------|--------------|--------|-------|
| [Deal A] | [Name] | CONTACTING | [date] | [N] days | REPLIED | Sent NDA, awaiting signature |
| [Deal B] | [Name] | CONTACTING | [date] | [N] days | SILENT — FLAG | No reply in 8 days |

### Deal A — Reply Summary
- Date received: [date]
- Subject: [subject]
- Summary: [1-2 sentence summary]
- Recommended next action: [specific action]

### Stale/Flag Items
- [Deal B] has been silent for 8 days. Consider sending a follow-up nudge.
  → Type "follow up on Deal B" to draft a nudge.
```

---

### Mode 3: DRAFT_FOLLOW_UP

**Triggers:** User says "follow up on [deal]", "nudge broker on [deal]", "no reply from
[broker]", "send a follow-up", "[deal] has been quiet"

**Pre-conditions to check:**
- Deal must be in CONTACTING or IN_DILIGENCE state.
- Check days since last outbound contact. If <5 business days, caution user: "It's only been
  [N] days — too soon for a follow-up. Brokers typically respond within 3-5 business days.
  Want to wait?"
- If >14 days with no reply: escalate tone in the draft and add a note to user flagging this.
- If >21 days: strongly recommend considering PASSED. Display warning before drafting.

**Process:**
1. Load deal and communications history from deals.json.
2. Load the Follow-up Nudge template from `references/email_templates.md`.
3. Reference the original subject line so the follow-up threads correctly.
4. Personalize: state the days elapsed since the initial email. Be brief (under 100 words).
5. Display the draft for user approval (same format as Mode 1).
6. On approval: save to Gmail draft via `gmail_create_draft`.
7. Log to deals.json communications with `"type": "follow_up"`, `"status": "draft"`.

**14+ day flag behavior:** If 14+ days without reply, include in output:
```
CAUTION: [Deal Name] has been waiting [N] days with no broker reply.
This is above the 14-day flag threshold from the communication protocol.
Options:
  A. Send the follow-up below
  B. Move the deal to PASSED
  C. Wait another [N] days (not recommended)
```

**Output format:** Same structure as Mode 1 output, with CAUTION block prepended if applicable.

---

### Mode 4: SATISFACTION_CHECK

**Triggers:** User says "are we good on [deal]?", "ready to meet on [deal]?", "is [deal] ready
for a seller call?", "what's left to ask on [deal]?"

**Purpose:** Review the full communication thread for a deal in IN_DILIGENCE, assess whether
all critical diligence questions have been answered, and recommend READY_TO_MEET or identify
remaining gaps.

**Process:**
1. Load deal from deals.json — get business type, communications array, and deal memo notes.
2. Fetch the full Gmail thread via `gmail_read_thread` using the stored thread_id.
3. Load `references/diligence_question_bank.md` — identify which categories apply to this
   business type (janitorial/car wash/laundromat get vertical-specific questions added).
4. Map what has been ASKED and what has been ANSWERED from the thread:
   - Financials: TTM revenue/SDE confirmed? Add-backs disclosed? Tax returns requested?
   - Operations: Manager structure confirmed? Day-to-day owner involvement clarified?
   - Customers: Concentration risk assessed? Top customer list received?
   - Lease: Terms confirmed? Assignment clause clear?
   - Legal: Liens/suits confirmed clear?
   - Vertical-specific: Apply relevant questions from the question bank.
5. Calculate a readiness verdict:
   - **READY_TO_MEET**: All critical categories answered (Financials + Operations + Lease
     minimum). Minor gaps acceptable if they can be addressed on the call.
   - **NOT_READY**: One or more critical categories have unanswered questions. Draft a targeted
     follow-up email to address the specific gaps.

**Output format:**
```
## Diligence Satisfaction Check — [Deal Name]

### What We've Asked and Received

| Category | Questions Asked | Answered? | Notes |
|----------|----------------|-----------|-------|
| Financials | TTM SDE, add-backs, 3yr P&L | YES / PARTIAL / NO | [detail] |
| Operations | Manager in place, owner role | YES / PARTIAL / NO | [detail] |
| Customers | Top 5 customers, concentration | NO | Not yet requested |
| Lease | Terms, assignment clause | PARTIAL | Rent confirmed, expiry unclear |
| Legal | Suits/liens | YES | Confirmed clear |
| [Vertical] | [Specific questions] | [status] | [detail] |

### Verdict: READY_TO_MEET / NOT_READY

**Reasoning:** [2-3 sentences on what's confirmed and what's missing]

### If NOT_READY — Gaps to Resolve Before Meeting:
1. [Specific unanswered question]
2. [Specific unanswered question]

→ Want me to draft an email addressing these gaps? (yes / no)
```

**State transitions:**
- If verdict is READY_TO_MEET and user confirms: update deal state to READY_TO_MEET in
  deals.json and log the state change.
- If user decides to pass: offer to draft a Soft Pass email.

---

## State Transitions Summary

| Action | From State | To State | Trigger |
|--------|-----------|----------|---------|
| Initial outreach sent | SHORTLISTED | CONTACTING | User confirms email sent |
| Broker responds, info exchange begins | CONTACTING | IN_DILIGENCE | User confirms |
| All key diligence answered | IN_DILIGENCE | READY_TO_MEET | SATISFACTION_CHECK verdict |
| User decides to pass | Any | PASSED | User instructs pass |

All state changes must be written to `data/deals.json` in the deal's `state_history` array:
```json
{
  "date": "YYYY-MM-DD",
  "from_state": "CONTACTING",
  "to_state": "IN_DILIGENCE",
  "note": "Broker replied with NDA. Moving to active info exchange."
}
```

---

## Gmail MCP Tools Used

- `gmail_search_messages` — Search for broker replies by sender email or subject keywords
- `gmail_read_thread` — Read a full email thread by thread_id
- `gmail_create_draft` — Save an approved draft to Gmail (never called without user approval)

## Google Calendar MCP

- `gcal_find_meeting_times` — Suggest available windows for a seller/broker call (for
  SATISFACTION_CHECK mode when recommending READY_TO_MEET)
- `gcal_create_event` — NEVER called by this skill. Suggest meeting times only. User must
  explicitly request a calendar event to be created.

---

## Communication Logging Schema

Every outbound draft and every detected inbound message must be logged to deals.json:

```json
{
  "date": "YYYY-MM-DD",
  "direction": "outbound | inbound",
  "type": "inquiry | nda | cim_request | questions | interest_letter | meeting_request | pass | follow_up",
  "subject": "Email subject line",
  "summary": "1-2 sentence summary of content",
  "status": "draft | sent | received",
  "gmail_thread_id": "thread id if known"
}
```

---

## Stale Deal Monitoring

At the start of every broker-comm session, after loading deals.json, scan for:

- Any deal in CONTACTING with no inbound message in >14 days → flag in output
- Any deal in any state for >30 days with no state change → flag as STALE
- Any deal in IN_DILIGENCE with no activity in >7 days → suggest CHECK_REPLIES

Present flagged deals at the top of your output before entering the requested mode.

---

## What NOT to Do

- Never call `gmail_create_draft` before displaying the full email text and getting user approval
- Never call any `gcal_*` create/update tool
- Never include visa or immigration status in any broker email
- Never share the internal deal ID, deal memo scores, or analysis with the broker
- Never negotiate price or terms over email — get to a call first
- Never tell a broker this is the buyer's "first acquisition" or signal inexperience
- Never draft communications for deals in DISCOVERED or ANALYZED state — they must be SHORTLISTED first
- Never assume a broker reply means the deal advances — user must confirm state transitions
