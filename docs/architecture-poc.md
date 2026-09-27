# SPARK POC Architecture — "Buildable in a Day"

> Goal: a working demo your director can see today. Email in → Kudos Card out.
> Every corner cut here is labeled. The production path is noted but not built yet.

---

## The 30-second answer

```
spark@company.com      (shared mailbox — IT creates this)
       ↓
Power Automate         (monitors the mailbox, orchestrates everything)
       ↓
Azure OpenAI           (reads the email, extracts who/what/value as JSON)
       ↓
SharePoint List        (one row per kudos — this is the database)
       ↓
Outlook                (sends the Kudos Card + manager notification)
```

Five pieces. All M365-native. No servers, no deployments, no custom code for POC.

---

## Inbound — how an email becomes a kudos

### Step 1: The shared mailbox
IT creates `spark@company.com` (or whatever you agree on).
It's just a mailbox. No special setup. Takes 30 minutes.

### Step 2: Power Automate trigger
A flow with one trigger: **"When a new email arrives in a shared mailbox"**
- Fires within seconds of receipt (or up to 5 min on standard plans — fine for POC)
- Captures: From, To, CC, Subject, Body (plain text + HTML), Received time
- This is a native Power Automate connector. Zero code.

### Step 3: Azure OpenAI extraction
Power Automate calls Azure OpenAI with a prompt like:

```
You are processing a recognition email. Extract the following as JSON:
- recognized_person: full name of who is being recognized
- recognizer: full name of who sent the recognition
- source_type: one of [customer, manager, peer, leader, partner]
- summary: one warm sentence describing what they did (max 25 words)
- core_value: one of [Put People First, Customer-driven, We Value Different Perspectives]
- confidence: high / medium / low

Email:
---
{email body here}
---

Return only valid JSON. If you cannot determine a field, set it to null.
```

Output is a JSON blob. Power Automate parses it.

**POC shortcut:** If Azure OpenAI isn't provisioned yet —
use **AI Builder** (included in many M365 plans) or even skip AI entirely
and hardcode the extraction for a single demo email. The flow still works;
you're just proving the plumbing, not the intelligence.

### Step 4: Entra ID lookup
Power Automate calls the **Office 365 Users** connector:
- `Get user profile (V2)` → look up recipient by email → get their manager
- No permissions setup needed if you're running as a licensed user
- Returns: display name, job title, department, manager email

**POC shortcut:** Hardcode recipient + manager email. Skip the lookup.
Prove the AI extraction first, then add the lookup in hour 2.

### Step 5: SharePoint List — the database
Create a list called `SPARK_Kudos` with these columns:

| Column | Type | Notes |
|---|---|---|
| Title | Single line | Auto-set to "Kudos from [Recognizer]" |
| Received_Date | Date/Time | Timestamp of email |
| Recognizer_Name | Single line | From AI extraction |
| Recognizer_Email | Single line | From email headers |
| Recipient_Name | Single line | From AI extraction |
| Recipient_Email | Single line | From Entra ID lookup |
| Recipient_Manager | Single line | From Entra ID lookup |
| Team | Single line | From Entra ID lookup (department field) |
| Summary_Quote | Multi-line | The AI-generated one-sentence summary |
| Core_Value | Choice | Put People First / Customer-driven / We Value Different Perspectives |
| Source_Type | Choice | customer / manager / peer / leader / partner |
| Is_Private | Yes/No | Default: No |
| Manager_PlusOne | Yes/No | Default: No (toggled by manager reply) |
| Confidence | Choice | high / medium / low |

Power Automate creates one row per kudos. 15 minutes to set up the list.

---

## Outbound — how SPARK sends emails

### The Kudos Card (to recipient)
Power Automate → **Send an email (V2)** connector → Outlook.

HTML is inline in the Power Automate action. No external hosting.
Outlook renders it. Works on desktop, mobile, web.

```
From:    spark@company.com
To:      recipient@company.com
Subject: 🌟 Someone recognized you today
Body:    [HTML Kudos Card — see design doc]
```

**The 👏 Manager notification (to manager)**
Sent immediately after the Kudos Card.

```
From:    spark@company.com
To:      manager@company.com
Subject: [Name] just received a kudos — add your thanks?
Body:    Short summary + one button
```

The button for POC is a **mailto: link**:
```html
<a href="mailto:spark@company.com?subject=+1 [K-{ID}]&body=Adding my thanks for {Name}">
  👏 Add my thanks
</a>
```
When the manager clicks it, their mail client opens a pre-filled email.
They hit Send. That's it.

A second Power Automate flow monitors spark@ for emails with subject starting `+1 [K-`
and updates the `Manager_PlusOne` field in the SharePoint row.

**Production upgrade:** Outlook Actionable Messages (Adaptive Cards) — real inline buttons
in email, no mail client opens. Requires M365 admin to register a provider. Save for v1.

---

## The two flows — what you build

### Flow 1: Inbound processing (the main one)
```
Trigger:   New email in spark@ mailbox
Action 1:  Call Azure OpenAI → parse JSON
Action 2:  Look up recipient in Office 365 Users
Action 3:  Look up recipient's manager
Action 4:  Create item in SharePoint List
Action 5:  Send Kudos Card email to recipient
Action 6:  Send manager notification email
```
Build time: 2–3 hours including testing.

### Flow 2: Manager +1 handler
```
Trigger:   New email in spark@ mailbox with subject starting "+1 [K-"
Action 1:  Parse kudos ID from subject
Action 2:  Get item from SharePoint by ID
Action 3:  Update Manager_PlusOne = true
```
Build time: 30 minutes.

---

## What you need before you start

**From IT (request these first — they're the long pole):**
- [ ] Shared mailbox `spark@company.com` created and accessible
- [ ] Permission to create a Power Automate flow using that mailbox
- [ ] Azure OpenAI endpoint + API key (or confirm AI Builder is available)

**You do yourself (takes minutes):**
- [ ] Create the `SPARK_Kudos` SharePoint List with columns above
- [ ] Power Automate account (included in M365 — log in at make.powerautomate.com)

---

## POC vs Production — what you're cutting

| Feature | POC (day one) | Production (v1) |
|---|---|---|
| Mailbox monitoring | Power Automate standard trigger | Same, but with error handling |
| AI extraction | Single prompt, basic JSON | Multi-step with confidence fallback + human review queue |
| Directory lookup | Hardcoded or basic lookup | Full Entra ID lookup, handles multiple recipients |
| Manager +1 | mailto: link → reply parsing | Outlook Adaptive Card (inline button) |
| Low-confidence handling | Creates row, sends card anyway | Sends one-question email back to sender |
| Multiple recipients | Not handled — demo with single name | Resolved by AI + regex |
| Error handling | None | Failed flows alert a monitoring mailbox |
| Hub | Not built | SharePoint Modern page |
| Bumps | Not built | Two scheduled flows |
| Newsletter | Not built | Scheduled flow + OpenAI narrative |

---

## The demo script (for your director)

1. Open your email client. Write a short thank-you note to a real colleague.
2. CC `spark@company.com`.
3. Hit Send.
4. Wait ~30 seconds.
5. The colleague gets a Kudos Card in their inbox.
6. Show the SharePoint List — one new row, all fields populated by AI.
7. Show the manager notification email.
8. Click the 👏 mailto: button — manager's email opens pre-filled.
9. Hit Send. Show the SharePoint row updating `Manager_PlusOne = true`.

That's the POC. Nine steps, all live, all real. No mocks.

---

## Estimated build time

| Task | Time |
|---|---|
| SharePoint List setup | 20 min |
| Flow 1: inbound processing | 2–3 hrs |
| Flow 2: manager +1 | 30 min |
| HTML Kudos Card email template | 1 hr |
| End-to-end testing + fixes | 1 hr |
| **Total** | **~5–6 hrs** |

Prereqs (IT tasks) are the only wildcard. If the shared mailbox and Azure OpenAI
are ready before you start, this is a single focused day.
