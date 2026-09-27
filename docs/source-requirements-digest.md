# S.P.A.R.K. — Source Requirements Digest

Condensed, faithful text of `SPARK_RR_AI_Platform_Requirements_v1.pdf` (9 pages).
Use this file instead of re-uploading the PDF. Pages 8–9 held only an architecture
diagram (image, no text).

**Name:** S.P.A.R.K. — Spreading Positivity and Appreciation through Recognition and Kudos
**Tagline:** Recognize. Celebrate. Learn.

## Vision (original flow)
1. Manager/associate sends kudos by email (entry point).
2. Recipient's manager acknowledges and CCs a dedicated Kudos DL.
3. AI agent monitors the DL: extracts details, reformats to a standard template,
   assigns points from an agreed scoring model, publishes to SharePoint and/or Teams.
4. AI agent updates a Power BI dashboard and generates a monthly summary/newsletter.

Workflow: Kudos emails → AI extraction → central repository → Recognition Board →
Analytics → AI insights/newsletters.

## 1. Core platform (suggested tools)
| Capability | Tool |
|---|---|
| Kudos submission | MS Forms / Power Apps |
| Email ingestion | Power Automate |
| Storage | SharePoint / Dataverse |
| AI extraction | Azure AI / Copilot / AI Builder |
| Directory | Entra ID / HR data |
| Kudos portal | Power Apps + SharePoint |
| Dashboards | Power BI |
| Notifications | Teams / Outlook |
| Newsletter | Power Automate + Copilot |
| Advanced analysis | Azure OpenAI / Fabric |

## 2. Automatic email ingestion
Employees forward recognition emails (incl. from customers) to e.g. `recognition@company.com`.
Power Automate detects → AI extracts: recipient, sender, source (customer/manager/peer),
what happened, category, values shown, customer impact, team, location, sentiment,
key accomplishments → generates a concise Kudos Card → stored in Dataverse/SharePoint →
employee notified "🎉 Your kudos has been added to the Recognition Wall!" → visible on portal.

## 3. Kudos classifier
- **Source:** Customer, Manager, Peer, Senior Leader, External Partner
- **Type:** Customer Service, Technical Excellence, Leadership, Collaboration, Innovation,
  Going Above & Beyond, Problem Solving, Operational Excellence, Mentoring, Delivery Excellence
- **Core values:** Put People First; Customer-driven; We value different perspectives

## 4. Virtual Recognition Hub
Social wall with stats (e.g. 427 kudos, 312 recognized, 18 teams, 96 customer kudos),
scrolling feed of cards (name, team, quote, value tags, "recognized by"),
reactions: 👏 Celebrate, ❤ Like, 💬 Comment.

## 5. Power BI "Recognition Intelligence"
Answers: most-recognized teams/employees, most frequent values, who recognizes whom,
customer vs peer vs manager mix, by location, monthly trend, emerging themes.
Example exec dashboard: totals, % positive customer recognition, MoM change,
top categories (%), most-recognized teams leaderboard.

## 6. AI "Recognition Insights"
Monthly narrative, e.g. "Customer recognition up 18% vs August, driven by benefits support;
cross-team collaboration appeared in 31% of submissions; three teams rose…"

## 7. AI newsletter
Monthly "Recognition Spotlight": standout recognition, Customer Voices count,
Collaboration Champions, Emerging Theme (with % change), congratulations list.
Distributed via Outlook/Teams by Power Automate.

## 8. "Ask Recognition" AI
Natural-language Q&A for leadership over the recognition dataset (not raw emails), e.g.
"Which teams got the most customer recognition this quarter?",
"Who was recognized by customers >3 times this year?",
"What are customers praising HCM teams for?"
