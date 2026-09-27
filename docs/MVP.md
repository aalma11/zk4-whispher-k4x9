# SPARK MVP — "Just CC Spark"

> Status: draft v0, for brainstorming. Departs from the source PDF on purpose.

## The one idea
People already write thank-you emails. SPARK asks them to do **one** extra thing: add
`spark@` to the CC line. Nothing to learn, install, open, or log into.

## Principles
1. **Zero new behavior.** No forms, no portal, no subject-line syntax, no tagging, no categories to pick.
2. **The recipient's moment is the product.** Everything serves the second someone learns they were appreciated.
3. **Say no to almost everything.** One input (an email), two outputs (a card and a monthly story).
4. **The AI stays invisible.** Nobody should have to think "I'm using an AI tool." It just works.
5. **Every email SPARK sends must be worth opening.** If it's noise, people filter it, and then the product is dead.

## What the MVP does (4 things)

### 1. Capture — CC or forward
- Writing a thank-you? CC `spark@`.
- Got praise from a customer or partner? Forward it to `spark@`.
- That's it. No manager approval gate before it counts.

### 2. Understand — AI, behind the curtain
From the email alone, SPARK figures out:
- **Who** was recognized (matched against the directory; handles several people)
- **Who** recognized them, and the source (customer, peer, manager, leader, partner)
- **What** happened, in one warm sentence
- **Which core value** it shows (just the 3 company values, not 10 categories)

If it's unsure (e.g. "thanks team!" with no names), it asks the sender **one** question
by email, answered by clicking a button.

### 3. Deliver — the magic moment
- The **recipient** gets a short, well-designed Kudos Card email: the quote, who sent it,
  the value it shows. That one email is the reward.
- The recipient's **manager** is informed automatically, with a one-click
  **👏 Add my thanks** button. This turns the PDF's "manager must acknowledge" gate into
  an optional amplifier.
- The **sender** gets nothing, or one quiet line at most. No confirmation spam.

### 4. Reflect — one monthly email
On the first working day of the month, the whole department gets **SPARK Spotlight**,
written by the AI: 3–5 standout stories, customer voices, one emerging theme, and a
list of everyone recognized. That email **is** the MVP dashboard.

## What we are NOT building (yet)
| Cut from PDF | Why |
|---|---|
| Manager acknowledgment + DL relay | Adds a second person and a delay to every kudos. It's where recognition goes to die. |
| Points / scoring model | Makes gratitude a transaction and invites gaming and disputes. Revisit only if it's tied to real rewards. |
| Forms / Power Apps submission | A second entry point is a second thing to explain. Email only. |
| Recognition Wall / portal with likes & comments | Nobody visits portals. The Spotlight email does this job. |
| Power BI exec dashboard | Build it when a leader asks a question the Spotlight can't answer. Keep the data clean so it's easy later. |
| 10-category + 5-source taxonomy shown to users | Kept internally for analytics, never shown or asked for. Users see values only. |
| "Ask Recognition" chat | v2, and via email: reply to SPARK with a question, get an answer back. |

## Buttons inside email
- **Preferred:** Outlook Actionable Messages (Adaptive Cards) for real in-email buttons.
- **Fallback that works everywhere:** `mailto:` buttons that open a pre-filled reply
  (e.g. subject `+1 [K-1042]`). One click plus Send. Still pure email.

## Trust and privacy
- SPARK only reads emails **explicitly sent or CC'd to it**. Nothing else.
- Reply `private` → the card still goes to the recipient but is left out of Spotlight.
- Anyone can reply `remove me` to be left out of Spotlight.
- Customer emails are summarized; customer contact details are not republished.

## Suggested build (M365, low-code)
Shared mailbox `spark@` → Power Automate (new-mail trigger) → Azure OpenAI
(structured JSON extraction) → Entra ID lookup (recipient, manager, team) →
SharePoint List (one row per kudos) → Outlook HTML/Adaptive Card emails.
Monthly scheduled flow → LLM writes Spotlight from that month's rows → email to the department DL.

## Data per kudos (SharePoint list)
`id, received_at, sender, source_type, recipients[], recipient_managers[], team,
summary_quote, value, category(internal), customer_impact, sentiment, private(bool),
manager_plus_ones, raw_message_id`

## Edge cases to handle in v0
Self-kudos, reply-all storms (dedupe by thread), several recipients, forwarded chains
(find the original author), recipient outside the department, sarcasm/negative
content (don't publish; flag it), duplicate CC + forward of the same email.

## 30-day pilot and how we know it works
- **North star:** % of the department that *sent* at least one kudos in the month.
- Kudos per week, trend after the launch bump fades (weeks 3–4 matter most).
- Spotlight open rate.
- Manager 👏 click rate.
- AI accuracy: % of cards needing correction (target < 5%).
- Qualitative: 5 recipients, "how did that email feel?"

**Kill criteria:** if week-4 volume is under 30% of week-1 volume, the loop isn't working.
Fix the loop before adding any features.

## Launch
No training. One email from the department head, CC'ing `spark@`, thanking someone
real. That email is the tutorial.

## Open questions
1. Department size, and does it all sit under one DL / manager tree in Entra ID?
2. Is manager acknowledgment a formal requirement (HR award program, audit)?
3. Do points link to any real reward budget?
4. Which AI service is approved for employee email content (Azure OpenAI, Copilot Studio, AI Builder)?
5. Can we register Outlook Actionable Messages in this tenant?
