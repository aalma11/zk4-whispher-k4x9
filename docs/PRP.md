# Product Requirements Prompt (PRP)
# S.P.A.R.K. — Spreading Positivity and Appreciation through Recognition and Kudos

> Paste this into Claude Code, Codex, GitHub Copilot, or your preferred AI coding
> assistant to generate an implementation plan, architecture, and working software.

---

## One-Sentence Description

An email-first AI recognition platform that automatically converts thank-you emails
into structured kudos cards, stores them in a central hub, and uses smart nudges to
create a viral recognition flywheel — built entirely within M365 and Google Cloud tools
the team already has, requiring zero new accounts or IT approvals.

---

## Target Audience

**Department:** HR Technology / related department (~30–40 people)
**Company:** Large enterprise (Albertsons scale)

| User type | Role in SPARK | Pain point solved |
|---|---|---|
| **Individual contributors (ICs)** | Send and receive kudos | Recognition doesn't happen because it's too much effort |
| **Managers** | Receive notifications, use equity bump | Team members go unrecognized; no visibility into morale gaps |
| **Director** | Views Recognition Hub for big picture | Has to dig through emails to understand team recognition health |

---

## The Core Insight

> **"The email is the channel. The hub is the database."**

People already write thank-you emails. SPARK asks for one extra action: add `SPARK:`
to the subject line. That's the entire user behavior change. Everything else is
automated.

---

## User Journey (Full Flow)

### Everyday recognition
1. Employee writes a thank-you email — adds `SPARK:` to subject line
2. Outlook rule moves it to **SPARK Inbox** folder automatically
3. Power Automate detects the new email (watches the folder)
4. Gemini (Vertex AI) extracts: recipient, sender, source type, what happened, core value, sentiment
5. SharePoint List receives one new row — the permanent record
6. **Recipient gets a Kudos Card email** — warm, well-designed HTML — this is the product moment
7. Manager receives a notification email with a one-click `mailto:` **👏 Add my thanks** button

### Monthly rhythm
8. First working day of month: AI writes the **SPARK Spotlight** newsletter (3–5 stories,
   customer voices, emerging theme, everyone recognized) → sent to full department DL
9. Spotlight contains a **"View the Recognition Hub →"** link — the only navigation to the hub

### Mid-month bump (IC)
10. Around the 15th: anyone who hasn't sent a kudos this month gets the **IC Bump**
    - If they were recognized recently by someone: leads with the reciprocity hook
      (*"Sophia recognized you last month — maybe return the appreciation?"*)
    - Fallback: memory-sparking prompts (*"Who made your work easier this month?"*)
    - Each suggestion has a pre-filled `mailto:` template — edit two words, hit send

### End-of-month bump (Manager)
11. ~5 business days before month-end: manager gets the **Equity Bump** (private)
    - Names team members with zero kudos that month
    - Provides AI-generated memory-sparking prompts specific to that person's role
    - One-click `mailto:` recognize button

---

## Core Features

### MVP — Phase 1 (POC, buildable in one day)
| # | Feature | Description |
|---|---|---|
| 1 | **Email capture** | Outlook rule routes `SPARK:` emails to a monitored folder |
| 2 | **AI extraction** | Gemini extracts structured JSON: who, what, value, source |
| 3 | **Kudos Card email** | HTML email delivered to recipient's inbox — the magic moment |
| 4 | **Manager notification** | Notification with optional `mailto:` amplify button |
| 5 | **SharePoint database** | One row per kudos in SPARK Recognition list |
| 6 | **Roster lookup** | SPARK Roster list maps emails → team, manager, manager email |

### MVP — Phase 2 (Full MVP, ~1–2 weeks after POC)
| # | Feature | Description |
|---|---|---|
| 7 | **Recognition Hub** | SharePoint page: stats, filterable feed, team leaderboard |
| 8 | **Monthly Spotlight** | AI-written newsletter; scheduled Power Automate flow |
| 9 | **IC Bump** | Mid-month nudge with reciprocity hook + memory-sparking suggestions |
| 10 | **Manager Equity Bump** | End-of-month private nudge for managers with unrecognized team members |

### What is explicitly NOT in scope (MVP)
- Points/scoring system (makes gratitude transactional)
- Social wall with likes/comments (nobody visits portals)
- Forms or Power Apps submission (email only)
- Individual leaderboard (demotivating in small teams at launch)
- "Ask Recognition" AI chat (v2)
- Power BI dashboard (Hub covers it; build when a specific question needs it)

---

## Technical Architecture

### Platform
**100% M365 + Google Cloud.** No new tools, no new vendor approvals.

### Stack

| Layer | Tool | Why |
|---|---|---|
| **Email intake** | Outlook + SPARK Inbox folder + Outlook rule | Zero IT approval; uses existing account |
| **Automation engine** | Power Automate | Already licensed (M365); native connectors for everything |
| **AI extraction** | Gemini 2.0 Flash via Vertex AI | Enterprise account already active; $0 marginal cost at POC scale |
| **Database** | SharePoint Lists | Native PA connector; POC DB = production DB; no migration |
| **Hub** | SharePoint Modern page | Built on top of same list; no separate hosting |
| **Outbound email** | Office 365 Outlook connector (PA) | Already in tenant |
| **Scheduled flows** | Power Automate scheduled cloud flows | Bumps, Spotlight |

### Power Automate flow (8 actions)

```
[Trigger]     "When new email arrives in folder" → SPARK Inbox (Outlook connector)
     ↓
[Condition]   Skip if sender = own email (loop prevention)
     ↓
[Get items]   SharePoint → SPARK Roster list → filter by sender/recipient email
              Returns: team, manager name, manager email
     ↓
[HTTP]        POST to Vertex AI (Gemini 2.0 Flash)
              Auth: Google AI Studio API key (POC) → service account Bearer token (prod)
     ↓
[Parse JSON]  Input: candidates[0].content.parts[0].text
              Unlocks named fields as PA dynamic content
     ↓
[Condition]   needs_clarification = true?
              → Yes: send "who did you mean?" email → Terminate
              → No: continue
     ↓
[Create item] SharePoint → SPARK Recognition list → new row
     ↓
[Send email]  Kudos Card HTML → recipient (Office 365 Outlook connector)
     ↓
[Send email]  Manager notification + mailto: button → manager
```

### Gemini API call (HTTP action)

```
Method:  POST
URL:     https://generativelanguage.googleapis.com/v1beta/models/
         gemini-2.0-flash:generateContent?key=YOUR_API_KEY

Body:
{
  "systemInstruction": {
    "parts": [{ "text": "You are SPARK... extract recognition details... return JSON only." }]
  },
  "contents": [{
    "role": "user",
    "parts": [{ "text": "[assembled from PA dynamic content: sender, subject, body]" }]
  }],
  "generationConfig": {
    "responseMimeType": "application/json",
    "temperature": 0.1
  }
}
```

Response path: `candidates[0].content.parts[0].text`

---

## Data Models

### SharePoint List 1: SPARK Recognition

| Column | Type | Source |
|---|---|---|
| Title (recipient name) | Single line | Gemini → recipient_name |
| Sender | Single line | Gemini → sender_name |
| Source Type | Choice | Gemini → source_type |
| What Happened | Multiple lines | Gemini → what_happened |
| Core Value | Choice | Gemini → core_value |
| Team | Single line | Roster lookup |
| Manager Email | Single line | Roster lookup |
| Date | Date | PA trigger timestamp |
| Sentiment | Choice | Gemini → sentiment |
| Is Private | Yes/No | Default No; set if sender replies "private" |
| Manager Plus Ones | Number | Incremented when manager clicks 👏 |
| Bump Generated From | Yes/No | True if kudos was triggered by a bump |

### SharePoint List 2: SPARK Roster (manual, fill once)

| Column | Type |
|---|---|
| Name | Single line |
| Email | Single line |
| Team | Single line |
| Manager Name | Single line |
| Manager Email | Single line |
| Active | Yes/No |

### SharePoint List 3: SPARK Bump State (for bump logic)

| Column | Type |
|---|---|
| Employee Email | Single line |
| Last Kudos Sent Date | Date |
| Last Kudos Received Date | Date |
| IC Bump Sent This Month | Yes/No |
| Equity Bump Sent This Month | Yes/No |
| Opted Out | Yes/No |

---

## AI Integration Details

### Model
**Gemini 2.0 Flash** (Vertex AI) — fast, cheap, accurate for extraction tasks.
Cost: ~$0.00005 per email processed. 100 kudos/month ≈ essentially free.

### AI tasks
| Task | Trigger | Prompt type |
|---|---|---|
| **Email extraction** | Every SPARK: email | Structured JSON extraction |
| **Spotlight writing** | Monthly scheduled flow | Narrative generation from kudos data |
| **Bump suggestions** | IC/Manager bump flows | Memory-sparking question generation |

### Extraction JSON schema (Gemini output)
```json
{
  "recipient_name": "string or null",
  "sender_name": "string",
  "source_type": "customer | peer | manager | leader | partner",
  "what_happened": "string (max 40 words, third person, warm tone)",
  "core_value": "Customer-driven | Put People First | We value different perspectives",
  "sentiment": "positive | negative | neutral",
  "is_recognition": "boolean",
  "needs_clarification": "boolean"
}
```

### Suggestion engine (bumps — no external data)
SPARK uses only what it knows:
- Entra ID / Roster list: team members, roles, names
- Its own kudos history: who recognized whom, who has zero kudos this month
- Generates question-style prompts ("Who made your work easier this month?"), not data statements
- Leads with reciprocity hook if available ("Sophia recognized you last month…")

---

## Recognition Hub (SharePoint Page)

**Audience:** Director (monthly big picture), Managers (team drill-down)
**Access:** Anyone on the department DL — M365 login = access

Three sections:
1. **This month at a glance** — stat tiles: total kudos, # recognized, # givers, customer kudos
2. **Recognition feed** — filterable by team, value, month. One card per kudos.
3. **Team leaderboard** — team-level only (no individual ranking at MVP)

Hub is linked from the Spotlight email — no URL to remember, no separate bookmark.

---

## Authentication

**None required beyond existing M365 login.**
- Power Automate flows run under the user's M365 account
- SharePoint access controlled by department DL membership
- Gemini API key stored as Power Automate environment variable (not in flow body)
- No user-facing login screen, no session management, no new accounts

---

## Email Design

### Kudos Card (recipient)
- From: SPARK (sender's M365 account for POC)
- Subject: `✦ You were recognized, [Name]!`
- HTML with inline CSS (works in Gmail, Outlook, Apple Mail)
- Dark header gradient, quote block, meta row (recognized by + core value chip)
- Footer: link to Recognition Hub

### Manager notification
- Short plain-text + HTML
- `mailto:` button opens pre-filled email to recipient: one click + send
- Subject: `SPARK: [Recipient] was recognized`

### Monthly Spotlight
- AI-written narrative: 3–5 standout stories, customer voices, emerging theme
- Full list of everyone recognized that month
- CTA: "View the Recognition Hub →"
- Sent via scheduled Power Automate flow to department DL

### IC Bump
- Warm, optional-feeling tone — never guilt-tripping
- One email per person per month max
- Reciprocity hook leads if available; memory-sparking prompts as fallback
- Pre-filled `mailto:` button per suggestion

### Manager Equity Bump
- Private — sent only to the manager, never to the team
- Names 1–2 unrecognized team members max
- AI-generated memory-sparking prompts per person
- Pre-filled `mailto:` button

---

## Bump Logic (Scheduled Flows)

### IC Bump — runs ~15th of each month
```
Get all employees from Roster list
For each employee:
  → Has sent any kudos this month? → Skip
  → Has IC Bump already sent this month? → Skip
  → Has opted out? → Skip
  → Check: did anyone recognize this employee in last 30 days
            but hasn't been recognized back? → reciprocity hook
  → Generate 2-3 memory-sparking prompts (Gemini call)
  → Send bump email
  → Mark bump_sent = true in Bump State list
```

### Manager Equity Bump — runs ~5 business days before month end
```
Get all managers from Roster list
For each manager:
  → Get their direct reports (Roster list filter)
  → Find reports with zero kudos this month
  → Skip if none
  → Skip if only 1–2 person team
  → Take top 1–2 unrecognized (by longest gap since last recognition)
  → Generate memory-sparking prompts per person (Gemini call)
  → Send private bump email to manager
  → Mark equity_bump_sent = true
```

---

## Security Considerations

- SPARK reads only emails explicitly routed to the SPARK Inbox folder
- Employee emails processed by Gemini via Google Cloud enterprise (covered by Google's DPA)
- No employee data sent to third-party services outside M365/Google Cloud
- Manager Equity Bump content never exposed to team members
- Reply `private` → kudos stored but excluded from Hub and Spotlight
- Reply `remove me` → employee opted out of all bumps and Spotlight
- SharePoint lists scoped to department — not company-wide
- Gemini API key stored as Power Automate environment variable, not in flow body

---

## Design Vibe

- **Tone:** Warm, human, Apple-inspired. Not corporate-cold. Not gamified.
- **Email aesthetic:** Clean dark header, generous whitespace, one clear action per email
- **Hub aesthetic:** Dashboard-style but human — stat tiles, card feed, not a wall of numbers
- **Copy voice:** Direct, specific, warm. Never generic. The AI always uses names.
- **Inspirations:** Apple product emails (clean), LinkedIn recommendations (feel), Bonusly (concept but not execution)

---

## Development Milestones

### Day 1 — POC
- [ ] Create SPARK Inbox folder in Outlook
- [ ] Create Outlook rule: `SPARK:` subject → SPARK Inbox folder
- [ ] Create SPARK Recognition list (SharePoint)
- [ ] Create SPARK Roster list (SharePoint) — fill manually (~30 min)
- [ ] Build Power Automate flow (8 actions)
- [ ] Get Gemini API key (Google AI Studio)
- [ ] Wire HTTP action → Gemini
- [ ] Wire Parse JSON → SharePoint Create Item
- [ ] Wire Send Email × 2 (Kudos Card + Manager notification)
- [ ] Test end-to-end with one real email

### Week 1–2 — Full MVP
- [ ] Recognition Hub: SharePoint Modern page (stats + feed + leaderboard)
- [ ] Monthly Spotlight: scheduled PA flow + Gemini narrative generation
- [ ] IC Bump: scheduled PA flow (mid-month) + Gemini suggestion generation
- [ ] Manager Equity Bump: scheduled PA flow (end-of-month) + Gemini prompts
- [ ] SPARK Bump State list (SharePoint)
- [ ] Opted-out handling (reply `remove me`)
- [ ] Private kudos handling (reply `private`)

### v2 — Post-MVP
- [ ] Migrate to dedicated shared mailbox (`spark@`) — IT request
- [ ] Outlook Actionable Messages (true in-email buttons, replaces mailto:)
- [ ] "Ask Recognition" via email reply (natural language Q&A over the dataset)
- [ ] Individual leaderboard (after enough data to make it motivating, not discouraging)
- [ ] Power BI dashboard (when director asks questions Hub can't answer)
- [ ] Points/rewards integration (only if tied to a real budget)

---

## Success Metrics (30-day pilot)

| Metric | Target | What it measures |
|---|---|---|
| % of dept that sent ≥1 kudos in month | North star | Sending is the behavior; receiving follows |
| Weekly volume, weeks 3–4 vs week 1 | Must hold >30% | Real loop vs launch buzz |
| Bump → kudos conversion | >20% | Are suggestions landing? |
| Spotlight open rate | >60% | Is the monthly email worth opening? |
| AI card accuracy (no correction needed) | >95% | Trust in the system |
| Manager 👏 click rate | Baseline | Manager buy-in signal |

**Kill criterion:** If week-4 volume < 30% of week-1 AND bump conversion < 10%,
fix the loop before adding any features.

---

## Launch Plan

No training. No announcement deck.

The department head sends **one real thank-you email** with `SPARK:` in the subject,
thanking someone specific for something real. That email is the tutorial.
The recipient gets the first Kudos Card. The loop is live.

---

## Open Questions (for IT/stakeholder alignment)

1. Is manager acknowledgment a formal HR requirement (award program, audit trail)?
2. Do points connect to a real rewards budget?
3. Is the department cleanly defined in the directory (one DL or one manager tree)?
4. Migrate to a dedicated `spark@` shared mailbox after POC — who submits the IT ticket?
5. Outlook Actionable Messages — can we register the provider in this M365 tenant?
