# SPARK — Claude API Prompt (POC)

This is the exact prompt to send to Claude inside Make.com's HTTP module.

## System prompt

```
You are SPARK, a recognition intelligence assistant. Your job is to read
employee kudos and recognition emails and extract structured information
from them.

Extract only what is explicitly stated or strongly implied by the email.
Do not invent details. If a field is unclear, use null and set
needs_clarification to true.

Always return valid JSON. Nothing else. No preamble, no explanation.
```

## User message (assembled by Make.com)

```
Extract recognition details from this email.

Sender name: {{sender_name}}
Sender email: {{sender_email}}

Email subject: {{email_subject}}

Email body:
---
{{email_body}}
---

Return a JSON object with exactly these fields:
{
  "recipient_name": string or null,
  "recipient_email": string or null,
  "sender_name": string,
  "source_type": "customer" | "peer" | "manager" | "leader" | "partner",
  "what_happened": string (one warm, specific sentence, max 40 words),
  "core_value": "Customer-driven" | "Put People First" | "We value different perspectives",
  "sentiment": "positive" | "negative" | "neutral",
  "is_recognition": boolean,
  "needs_clarification": boolean
}

Rules:
- source_type: if the email was originally from a customer (forwarded), use "customer".
  If sent by a manager about their direct report, use "manager". Otherwise "peer".
- what_happened: rephrase in third person ("Maria resolved..."), warm but factual.
- core_value: pick the single best match. When in doubt, "Customer-driven".
- is_recognition: false if this looks like spam, an auto-reply, or is unrelated to recognition.
- If recipient is not clearly named, set recipient_name to null and needs_clarification to true.
```

## Make.com HTTP module settings

- Method: POST
- URL: `https://api.anthropic.com/v1/messages`
- Headers:
  - `x-api-key`: your Claude API key
  - `anthropic-version`: `2023-06-01`
  - `content-type`: `application/json`
- Body (JSON):
```json
{
  "model": "claude-haiku-4-5-20251001",
  "max_tokens": 500,
  "system": "[paste system prompt above]",
  "messages": [
    {
      "role": "user",
      "content": "[paste assembled user message above, with Make.com variables]"
    }
  ]
}
```

## Parse the response in Make.com

Claude returns:
```json
{
  "content": [{ "text": "{ ...the JSON you asked for... }" }]
}
```

Use Make.com's JSON parse module on `content[0].text` to get the fields.

## Cost estimate

- Model: claude-haiku-4-5 (fastest, cheapest)
- ~500 input tokens + ~150 output tokens per email
- Cost: ~$0.001 per kudos email
- 100 kudos/month ≈ $0.10/month
