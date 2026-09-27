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

### 5. The Hub — the director's single source of truth
**Design principle: the email is the channel. The hub is the database.**

The email does the emotional work — the Kudos Card, the Spotlight. The Hub does the
analytical work — it answers the question a director asks once a month: *"How is my team
doing in terms of recognition?"* Without the Hub, answering that means digging through
30 emails. That's not a director's job.

The Hub is not a social wall. It's a command center.

**MVP Hub = one SharePoint page, three sections:**

| Section | What it answers |
|---|---|
| **This month at a glance** | Is recognition happening? (Total kudos, # recognized, # givers, customer kudos) |
| **Recognition feed** | Who's being recognized and for what? (Filterable by team, value, month) |
| **Team leaderboard** | Which teams are thriving? (Team-level only — individual ranking demotivates at this stage) |

The Spotlight email links here. That's the only navigation needed.

**Access:** Scoped to the department in SharePoint. M365 login = access. Nothing new to set up.

**Who uses it:**
- **Director:** monthly check-in, big picture. Is the culture of recognition growing?
- **Managers:** drill into their team. Are my people being seen?
- **ICs:** curiosity. Nobody's excluded — but it's not designed for them.

### 6. Bumps — two bumps, two audiences
One bump for individual contributors. One for managers. That's it.

---

#### 6a. IC bump — "You haven't recognized anyone yet this month"
**Audience:** Individual contributors (and leads without direct reports)
**Trigger:** Around the 15th. You haven't CC'd spark@ once this month.

**Structure: personal hook first, then fallbacks**

SPARK checks one thing before building the email: *did anyone recognize this person recently
but hasn't been recognized back?* If yes — that's the lead suggestion. The reciprocity hook
is the most compelling reason to act because it's personal and specific.

If no reciprocity hook exists, it falls back to memory-sparking prompts from the team roster.

**Example — with reciprocity hook:**
> *Hey [Name] — you haven't recognized anyone this month yet. Here's an easy place to start:*
>
> 🔹 **Sophia recognized you last month** for your work on the benefits rollout. Maybe it's time to return the appreciation?
> 🔹 Does someone on your team always pick up the slack without being asked?
> 🔹 Who made your work easier this month, even in a small way?
>
> [**✉ Recognize Sophia →**]  [**✉ Recognize someone else →**]
>
> *Clicking opens a pre-filled email. Edit two words. Hit send.*

**Example — without reciprocity hook:**
> *Hey [Name] — you haven't recognized anyone this month yet. Here are a few prompts:*
>
> 🔹 Is there someone who always shows up when things get hard?
> 🔹 Who on the team quietly makes things better without the credit?
> 🔹 Did someone help you think through something difficult recently?
>
> [**✉ Recognize someone →**]

**The suggestion engine — memory sparking, not data reporting**

SPARK has no access to ticket systems or project data. That's intentional.
The goal of a suggestion is not to tell the sender what someone did.
It's to **spark the memory they already have.**

SPARK only uses what it knows:
- Entra ID: team roster, names, role/title
- Its own kudos history: who hasn't been recognized, who recognized *this person* (reciprocity)

Suggestions are questions, not statements. Questions can't be wrong.
A question like *"who made your work easier this month?"* works for any team, any role, any context.

**AI logic:**
- Reciprocity: lead with anyone who recognized this person in the last 30 days but hasn't been recognized back
- Equity: include people on their roster with zero kudos this month
- Role-appropriate tone from Entra ID title (IC vs. lead)
- Max 3 suggestions per email. One pre-filled template per suggestion.

**Guardrails:**
- One IC bump per person per month, maximum
- Never sent to someone who has already recognized someone this month
- Tone is warm and optional — never guilt-tripping

---

#### 6b. Manager equity bump — "Someone on your team hasn't been recognized yet"
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

## How the two bumps create a recognition flywheel

```
Someone gets a Kudos Card
        ↓
Feels seen → more likely to recognize others
        ↓
IC bump fires mid-month if they haven't acted yet
  → reciprocity hook: "Sophia recognized you — maybe return it?"
  → they recognize Sophia
        ↓
Sophia gets a Kudos Card → loop repeats

Meanwhile:
Manager equity bump (end of month)
  → quiet contributors who slipped through get recognized
  → they receive a Kudos Card → enter the flywheel
```

Two bumps. Two audiences. One flywheel. Volume compounds — that's the product.

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
