# SPARK — Gemini via Vertex AI (replaces claude-prompt.md)

## What changes vs. Claude
| | Claude API | Gemini (Vertex AI) |
|---|---|---|
| URL | `api.anthropic.com/v1/messages` | `{region}-aiplatform.googleapis.com/v1/...` |
| Auth header | `x-api-key: YOUR_KEY` | `Authorization: Bearer YOUR_TOKEN` |
| System prompt field | `"system": "..."` | `"systemInstruction": { "parts": [{"text": "..."}] }` |
| Message field | `"messages": [{"role":"user","content":"..."}]` | `"contents": [{"role":"user","parts":[{"text":"..."}]}]` |
| Response path | `content[0].text` | `candidates[0].content.parts[0].text` |
| Structured output | Requires Parse JSON step | Built-in via `responseMimeType: "application/json"` |

Everything else — the Power Automate flow, SharePoint, Outlook emails — is unchanged.

---

## Step 1: Get your API key (5 minutes)

Two options. Option A is faster for POC:

### Option A — Google AI Studio API key (fastest for POC)
1. Go to [aistudio.google.com](https://aistudio.google.com)
2. Click **Get API key** → **Create API key in new project**
3. Copy the key — it looks like `AIzaSy...`
4. Use it as `?key=YOUR_KEY` in the URL (no auth header needed)

> Good for POC. Check with IT whether Google AI Studio is under your enterprise agreement.
> If it needs to be fully under Vertex AI, use Option B.

### Option B — Vertex AI service account (enterprise-proper)
1. Go to `console.cloud.google.com` → **IAM & Admin** → **Service Accounts**
2. Create a service account → grant role **Vertex AI User**
3. Create a key (JSON) → download it
4. In Power Automate: HTTP action calls Google's token endpoint first to exchange the
   service account key for a Bearer token, then calls Gemini with it

> Option B is two HTTP actions instead of one. For POC, start with Option A.

---

## HTTP action settings in Power Automate

### Using Option A (API key in URL)
```
Method:  POST
URL:     https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=YOUR_API_KEY
Headers:
  Content-Type: application/json
Body:    (see below)
```

### Using Option B (Vertex AI + Bearer token)
```
Method:  POST
URL:     https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID
         /locations/us-central1/publishers/google/models/gemini-2.0-flash:generateContent
Headers:
  Authorization: Bearer YOUR_ACCESS_TOKEN   ← from the token exchange step
  Content-Type:  application/json
Body:    (see below — identical)
```

---

## Request body (same for both options)

```json
{
  "systemInstruction": {
    "parts": [{
      "text": "You are SPARK, a recognition intelligence assistant. Extract recognition details from employee emails. Always return valid JSON only matching the schema. No preamble, no explanation."
    }]
  },
  "contents": [{
    "role": "user",
    "parts": [{
      "text": "Sender name: [DYNAMIC - from email trigger]\nSender email: [DYNAMIC]\nSubject: [DYNAMIC]\nBody:\n---\n[DYNAMIC - email body]\n---\n\nExtract recognition details. Return JSON with these exact fields:\nrecipient_name, sender_name, source_type (customer/peer/manager/leader/partner), what_happened (one warm sentence max 40 words, third person), core_value (Customer-driven OR Put People First OR We value different perspectives), sentiment (positive/negative/neutral), is_recognition (boolean), needs_clarification (boolean)."
    }]
  }],
  "generationConfig": {
    "responseMimeType": "application/json",
    "temperature": 0.1
  }
}
```

> `responseMimeType: "application/json"` tells Gemini to return structured JSON directly.
> No Parse JSON step needed — the fields are immediately available as dynamic content.

---

## Gemini response (what Power Automate receives)

```json
{
  "candidates": [{
    "content": {
      "parts": [{
        "text": "{\"recipient_name\":\"Maria Santos\",\"sender_name\":\"James Reyes\",\"source_type\":\"peer\",\"what_happened\":\"Maria stayed late to resolve a benefits system outage before it could impact employees.\",\"core_value\":\"Customer-driven\",\"sentiment\":\"positive\",\"is_recognition\":true,\"needs_clarification\":false}"
      }],
      "role": "model"
    }
  }],
  "usageMetadata": {
    "promptTokenCount": 280,
    "candidatesTokenCount": 92
  }
}
```

## Power Automate: extract the text

In the Parse JSON step, set Input to:

```
body('HTTP_-_Call_Gemini')?['candidates'][0]['content']['parts'][0]['text']
```

Then use the same JSON schema from `claude-prompt.md` — the output fields are identical.

---

## Cost estimate (Gemini 2.0 Flash)

- Input: ~$0.075 per 1M tokens
- Output: ~$0.30 per 1M tokens
- Per kudos email: ~300 input tokens + 100 output tokens
- Cost per email: ~$0.00005 (less than 1 cent per 100 emails)
- 100 kudos/month ≈ **essentially free**

---

## Full updated flow (8 actions, same as before)

```
[Trigger]    Email arrives in SPARK Inbox folder (Outlook)
     ↓
[Condition]  Skip if sender = own email (loop prevention)
     ↓
[Get items]  SharePoint → SPARK Roster → look up recipient's manager + team
     ↓
[HTTP]       POST to Gemini (Vertex AI or AI Studio)
     ↓
[Parse JSON] Extract fields from candidates[0].content.parts[0].text
     ↓
[Condition]  needs_clarification = true → ask sender → terminate
     ↓
[Create item] SharePoint → SPARK Recognition list → new row
     ↓
[Send email] Kudos Card HTML → recipient
     ↓
[Send email] Manager notification → manager
```

Zero changes to SharePoint, Outlook connectors, or email templates.
Only the HTTP action (step 4) is different from the Claude version.
