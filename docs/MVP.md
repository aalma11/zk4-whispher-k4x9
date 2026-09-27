# SPARK MVP — "Just CC Spark"

> Status: draft v1, updated for Hub + Bump features.

## The one idea
People already write thank-you emails. SPARK asks them to do **one** extra thing: add
`spark@` to the CC line. Nothing to learn, install, open, or log into.

## Principles
1. **Zero new behavior.** No forms, no portal login, no subject-line syntax, no tagging, no categories to pick.
2. **The recipient's moment is the product.** Everything serves the second someone learns they were appreciated.
3. **Say no to almost everything.** One input (an email), and every output earns its place.
4. **The AI stays invisible.** Nobody should have to think "I'm using an AI tool." It just works.
5. **Every email SPARK sends must be worth opening.** If it's noise, people filter it, and the product is dead.
6. **Bumps are nudges, not guilt trips.** Warm, specific, easy to act on. Never shame. One per month max per person.

---

## What the MVP does (6 things)

### 1. Capture — CC or forward
- Writing a thank-you? CC `spark@`.
- Got praise from a customer or partner? Forward it to `spark@`.
- That's it. No manager approval gate before it counts.

### 2. Understand — AI, behind the curtain
From the email alone, SPARK figures out:
- **Who** was recognized (matched against the directory; handles several people in one email)
- **Who** recognized them, and the source (customer, peer, manager, leader, partner)
- **What** happened, in one warm sentence
- **Which core value** it shows (just the 3 company values — not 10 categories shown to users)

If it's unsure (e.g. "thanks team!" with no names), it asks the sender **one** question
by email, answered by clicking a button.

### 3. Deliver — the magic moment
- The **recipient** gets a short, well-designed Kudos Card email: the quote, who sent it,
  the value it shows. That one email is the reward.
- The recipient's **manager** is informed automatically, with a one-click
  **👏 Add my thanks** button. This turns the original "manager must acknowledge" gate into
  an optional amplifier.
- The **sender** gets nothing, or one quiet confirmation line at most. No spam.

### 4. Reflect — the monthly Spotlight email
On the first working day of the month, the whole department gets **SPARK Spotlight**,
written by the AI: 3–5 standout stories, customer voices, one emerging theme, the full
list of everyone recognized, and **one prominent "View the full hub →" link**.

That link is the only way stakeholders need to find the Hub. No bookmarks, no separate URL to remember.

### 5. The Hub — "I want more" for managers and directors
The Hub is not a destination. It's what the Spotlight links to.

**MVP Hub = one SharePoint page, three sections:**

| Section | What it shows |
|---|---|
| **This month** | Stats at a glance: total kudos, # recognized, # givers, customer kudos count |
| **Recognition feed** | Filterable by team, value, or month. One card per kudos: name, quote, value tags. |
| **Leaderboard** | Top recognized teams this month (not individuals — avoids awkward competition at MVP) |

That's it. No social wall, no likes, no comments at MVP. The goal is: a manager clicks the link from the Spotlight, gets their question answered in 10 seconds, and closes the tab.

**Why not individual leaderboard yet?** Team-level recognition is motivating. Individual leaderboards at early stage can demotivate the people not on them — we want everyone leaning in, not comparing. Revisit in v2 with more data.

**Access:** Scoped to the department in SharePoint. Anyone on the department DL can view. No login separate from their normal M365 account.

### 6. Bumps — the viral recognition loop
Three distinct bumps. Each is warm, specific, and easy to act on. One per person per type per month, max.

---

#### 6a. Employee bump — "Have you recognized someone lately?"
**Trigger:** It's mid-month (around the 15th) and you haven't CC'd spark@ once yet.

**What they get:** A short email with:
- A warm one-liner: *"SPARK noticed you haven't recognized anyone from your team this month yet."*
- **2–3 AI suggestions** based on their team roster and recent kudos patterns (e.g., "Does someone always answer your questions? Is someone covering for a gap right now?")
- A **pre-filled email template** they can click, edit two words, and send. The template already has `spark@` in CC. Zero blank page.

**Example:**
> Hi [Name], it's quick — here are some people on your team who might deserve a moment of recognition this month:
>
> 🔹 **Sarah Chen** — she's been answering questions for the whole team during the rollout. Is that something you've noticed?
> 🔹 **Marcus Williams** — he stepped up when the deadline moved. Maybe worth a "thank you for that"?
>
> [**✉ Recognize Sarah →**]  [**✉ Recognize Marcus →**]
>
> Clicking opens a pre-filled email. Edit, hit send. That's it.

**The suggestion engine — memory sparking, not data reporting**

SPARK has no access to ticket systems, project data, or any external signals. And that's fine.
The goal of a suggestion isn't to *tell* the sender what someone did — it's to **spark the memory
they already have.** The right prompt makes someone think "oh yeah, actually she *did* help me
with that." The AI's job is to ask the right question, not report a fact.

SPARK only uses what it knows:
- Entra ID: team roster, names, role/title (for role-appropriate prompts)
- Its own kudos history: who's been recognized this month, who recognized *you* (reciprocity), past recognition themes

**Suggestion format — questions and prompts, not statements:**
Instead of: *"Ben closed 14 support tickets"* (requires external data, may be wrong)
SPARK says: *"Is there someone who always shows up when you need them?"*
Or more personal: *"Sarah recognized you last month. Has she done anything recently worth noting?"*
Or role-targeted: *"As a lead, you probably see effort others miss. Who on your team quietly makes things better?"*

The suggestions are situational memory triggers. Generic enough to work without data.
Specific enough (name + question) to actually land.

**AI logic for suggestions:**
- Roster from Entra ID (their direct team)
- Exclude people already recognized by them this month
- Prioritize: people who recognized *them* recently (reciprocity logic, see 6b), and people in the team with zero kudos this month (equity logic)
- Use role/title from Entra ID to pick prompts that fit their vantage point (IC vs. lead vs. manager)

---

#### 6b. Reciprocity bump — "Jerry recognized you. Maybe return the love?"
**Trigger:** Someone recognized you in the last 2–4 weeks, and you haven't recognized them back yet. Runs once per pairing per month.

**What they get:** A short, warm nudge. Not a guilt trip — a prompt:

> *"Jerry gave you a shoutout 3 weeks ago for stepping up on the migration project. Maybe it's time to return the recognition?"*
>
> [**✉ Recognize Jerry →**]

**Why this is powerful:** It creates a viral loop. Every kudos sent generates a potential return kudos. Volume compounds. People who never thought to recognize someone get a concrete, personal reason to do so.

**Guardrails:**
- Max 1 reciprocity bump per person per month
- Never triggered if the other person already has multiple kudos from other people (they're not being overlooked)
- Tone is always warm and optional-feeling, never transactional ("you owe them one")

---

#### 6c. Manager equity bump — "Someone on your team hasn't been recognized yet"
**Trigger:** ~5 business days before end of month. A manager has one or more team members with zero kudos for the entire month.

**What they get:** A quiet, private email (not to the whole team — just them):

> *"As you head into the end of the month, I noticed Ben hasn't received any recognition this month. He was last recognized in July.*
>
> *Here are a few things he may have contributed that are worth a moment of appreciation:*
> - *He closed 14 support tickets in the last 3 weeks*
> - *He helped onboard two new team members*
>
> [**✉ Recognize Ben →**]*

**AI suggestion logic for managers — same memory-sparking approach:**
- Pull SPARK's own kudos history to identify who has zero kudos this month
- Look at past kudos for that person (e.g., "last recognized in July for collaboration") to suggest a theme
- Generate 2–3 question-style prompts based on their role/title, not external data:
  - *"Does Ben handle things quietly that others might not notice?"*
  - *"Has he helped someone on the team navigate something difficult recently?"*
  - *"What's something he does consistently that you appreciate?"*
- If there are multiple unrecognized members, flag only 1–2 at most per email — don't overwhelm

**Why this matters — the morale problem:** In a 30–40 person department, low recognition morale
means people feel invisible. Not because nobody cares, but because recognition gravitates toward
the most visible work. Quiet contributors carry the team and hear nothing.
The manager bump makes the manager the equity mechanism: they have the visibility to fill the gap,
they just need the prompt. This feature is the department's morale safety net.

**Guardrails:**
- Only fires if the team has ≥ 3 members (trivial for a 2-person team)
- Never exposes to the team that their manager was nudged
- If the manager already recognized that person in a non-Spark way, they can reply `already done` and SPARK marks it handled

---

## How the bump system creates a viral loop

```
Someone gets a Kudos Card  →  feels seen  →  more likely to recognize others
            ↓
Reciprocity bump fires  →  they recognize the person who recognized them
            ↓
That person gets a Kudos Card  →  loop repeats
            ↑
Manager equity bump  →  fills in the gaps for quiet contributors
Employee bump  →  gets passive observers to participate for the first time
```

Volume doesn't just grow linearly — it compounds. That's the product.

---

## What we are NOT building (yet)
| Cut from PDF | Why |
|---|---|
| Manager acknowledgment gate | Adds a person and a delay before kudos count. Replaced by the optional 👏 button. |
| Points / scoring model | Makes gratitude a transaction. Revisit only if tied to real rewards. |
| Forms / Power Apps submission | A second way in is a second thing to explain. Email only. |
| Social wall with likes/comments on Hub | Spotlight email does this. Hub is read-only at MVP. |
| Power BI exec dashboard | Hub covers this at MVP. Build BI when a specific question needs it. |
| 10-category + 5-source taxonomy shown to users | Internal only for analytics. Users see values only. |
| "Ask Recognition" AI chat | v2 — and it should work via email reply anyway. |
| Individual leaderboard | v2. Team-level is safer and more motivating at launch. |

---

## Buttons inside email
- **Preferred:** Outlook Actionable Messages (Adaptive Cards) for real in-email buttons.
- **Fallback that works everywhere:** `mailto:` buttons that open a pre-filled reply
  (e.g. subject `+1 [K-1042]`, or a template email with `spark@` in CC). One click plus Send.

---

## Trust and privacy
- SPARK only reads emails **explicitly sent or CC'd to it**. Nothing else.
- Reply `private` → card still goes to the recipient but excluded from Spotlight and Hub.
- Anyone can reply `remove me` to be left out of Spotlight and bumps.
- Customer emails are summarized; customer contact details are never republished.
- Manager equity bumps are private — the team never knows a nudge was sent.

---

## Suggested build (M365, low-code)
| Layer | Tool |
|---|---|
| Email intake | Shared mailbox `spark@` + Power Automate (new-mail trigger) |
| AI extraction | Azure OpenAI (structured JSON: who, what, value, source) |
| Directory | Entra ID lookup (recipient, manager, team, tenure) |
| Data store | SharePoint List (one row per kudos) |
| Hub | SharePoint page (Modern, filtered views, no custom dev) |
| Scheduled bumps | Power Automate scheduled flows (mid-month, end-of-month) |
| Bump AI | Azure OpenAI (generate suggestions from roster + kudos context) |
| Emails | Outlook HTML + Adaptive Cards (Actionable Messages) |
| Spotlight | Scheduled flow → LLM writes narrative → email to dept DL |

---

## Data per kudos (SharePoint list)
`id, received_at, sender, source_type, recipients[], recipient_managers[], team,
summary_quote, value, category(internal), customer_impact, sentiment, private(bool),
manager_plus_ones, raw_message_id, bump_generated_from(bool)`

**Per-person state table** (for bump logic):
`employee_id, last_kudos_sent_date, last_kudos_received_date, bump_sent_this_month[], reciprocity_pairs_this_month[], opted_out(bool)`

---

## Edge cases to handle in v0
Self-kudos, reply-all storms (dedupe by thread), several recipients, forwarded chains
(find the original author), recipient outside the department, sarcasm/negative
content (don't publish; flag it), duplicate CC + forward of same email, bump sent
but kudos already submitted before bump is processed (dedupe).

---

## 30-day pilot metrics
| Metric | Target | Why it matters |
|---|---|---|
| % dept that **sent** ≥1 kudos in month | North star | Sending is the behavior; receiving follows |
| Weekly kudos volume, week 3–4 | Must hold | Weeks 1–2 are launch buzz; weeks 3–4 reveal the loop |
| Bump → kudos conversion rate | > 20% | If bumps aren't converting, the suggestions need work |
| Spotlight open rate | > 60% | If leaders aren't reading it, the hub doesn't matter |
| Manager 👏 click rate | — | Leading indicator of manager buy-in |
| Hub unique visitors / Spotlight readers | Baseline | Who actually wants more than the email? |
| AI card accuracy | < 5% corrections | Trust in the system |
| Qualitative | 5 recipients | "How did that email feel?" |

**Kill criteria:** If week-4 volume is under 30% of week-1 volume AND bump conversion is under 10%, the loop isn't working. Fix the loop before adding any features.

---

## Launch
No training. One email from the department head, CC'ing `spark@`, thanking someone real.
That email is the tutorial. It also creates the first Kudos Card — proving the system works
before anyone else tries it.

---

## Answered questions

**Q6 — External data for suggestions:** Resolved. SPARK uses only its own kudos history and
Entra ID roster/roles. Suggestions are memory-sparking prompts (questions), not data facts.
No ServiceNow, no ticket systems, no project data. Zero external dependencies. ✅

**Q7 — Surface unrecognized employees to manager:** Confirmed yes. Managers are the equity
mechanism. The department has a morale problem rooted in people feeling unseen — this bump
is the fix. Manager bump is private and framed as a caring prompt, not a report card. ✅

**Department size:** ~30–40 people. Implications:
- Small enough that the monthly Spotlight will name most of the department — that's a feature, not a problem
- Small enough that "nobody left behind" is achievable (1–2 unrecognized people is very noticeable)
- Team leaderboard stays at team level (avoid individual comparison in a small group)
- Bump cadence: once per month per person is right — at 30–40 people, more frequent bumps will feel spammy fast
- Individual manager trees likely have 5–10 direct reports; the equity bump is meaningful at that scale

## Open questions
1. Is the department cleanly defined in Entra ID? (one manager tree, or multiple managers under a director — affects how we scope the Spotlight and Hub)
2. Is manager acknowledgment a formal HR requirement (award program, audit trail)?
3. Do points connect to a real rewards budget? If yes, we need a model. If no, cut permanently.
4. Which AI service is approved for processing employee email content? (Azure OpenAI, Copilot Studio, AI Builder)
5. Can we register Outlook Actionable Messages in this M365 tenant?
