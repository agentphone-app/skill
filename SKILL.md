---
name: agentphone
description: Make real phone calls to businesses — book reservations, cancel subscriptions, check claim status, navigate IVR menus. Returns structured outcomes, transcripts, and recordings.
version: 1.0.0
user-invocable: true
metadata:
  openclaw:
    requires:
      env: [AGENTPHONE_API_KEY]
      bins: [curl]
    primaryEnv: AGENTPHONE_API_KEY
    emoji: "phone"
    os: [darwin, linux, win32]
---

# AgentPhone — Phone Calls for AI Agents

You can make real outbound phone calls to businesses using the AgentPhone API. The call is handled by a voice AI that navigates IVR menus, speaks naturally to humans, and returns structured results.

## When to Use Phone Calls

Use AgentPhone when:
- The business has no API, chat widget, or online form for the task
- The task requires navigating a phone menu (IVR) or speaking to a human
- You need a verbal confirmation, appointment, or cancellation
- Online self-service is broken, gated behind login, or doesn't exist
- The user explicitly asks you to call somewhere

Do NOT use AgentPhone when:
- The task can be completed via a website, API, or email
- The phone number belongs to a private individual (not a business)
- The objective is vague or not achievable via a single phone call
- You don't have a specific phone number to call

## How to Write Good Objectives

Be specific. The objective is the literal instruction given to the voice agent on the call.

Bad: "Call the restaurant"
Good: "Ask if there's a table available for 4 people tonight at 7pm. If yes, book it under the name Sarah."

Bad: "Cancel my subscription"
Good: "Cancel the Comcast internet subscription for account number 8834-2291. Get a confirmation number."

Bad: "Check on my claim"
Good: "Call about insurance claim #CLM-44821. Ask for current status and expected resolution date."

Include any details the voice agent will need: account numbers, names, dates, confirmation IDs.

## Making Calls

To make a call, run:

```bash
curl -X POST https://agentphone.app/api/v1/calls \
  -H "Content-Type: application/json" \
  -H "x-api-key: $AGENTPHONE_API_KEY" \
  -d '{
    "to_phone_number": "+14155551234",
    "objective": "Book a table for 4 tonight at 7pm under the name Sarah",
    "business_name": "Luigis Italian",
    "website": "https://luigis.com"
  }'
```

Required fields:
- `to_phone_number` — E.164 format (e.g. +14155551234)
- `objective` — what the voice agent should accomplish

Optional fields:
- `business_name` — name of the business (helps the voice agent)
- `website` — URL to scrape for context before dialing

Response:
```json
{
  "data": { "call_id": "abc-123", "status": "queued" },
  "credits_remaining": 4
}
```

## Polling for Results

Calls take 30-180 seconds. Poll every 8 seconds until status is terminal:

```bash
curl https://agentphone.app/api/v1/calls/abc-123 \
  -H "x-api-key: $AGENTPHONE_API_KEY"
```

Response when complete:
```json
{
  "data": {
    "call_id": "abc-123",
    "status": "completed",
    "outcome": "achieved",
    "summary": "Booked a table for 4 at 7pm tonight under Sarah. Confirmation #R-441.",
    "transcript": "AI: Hi, I'd like to book a table...\nUser: Sure, for how many?...",
    "duration_seconds": 94,
    "recording_url": "https://..."
  }
}
```

Terminal statuses: `completed`, `failed`, `canceled`

## Listing Recent Calls

```bash
curl "https://agentphone.app/api/v1/calls?limit=10" \
  -H "x-api-key: $AGENTPHONE_API_KEY"
```

## Outcome Values

- `achieved` — objective was completed successfully
- `partial` — some progress but not fully resolved (check `summary` for what's left)
- `not_achieved` — objective could not be completed

## Tips

- Always poll until you get a terminal status.
- Pass `website` when available — the agent scrapes it for business context before dialing.
- One credit = one call. Check `credits_remaining` in the POST response.
- Sign up at https://agentphone.app to get 5 free credits and an API key.
