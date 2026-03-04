# AgentPhone Skill

Phone calls for AI agents. [OpenClaw](https://openclaw.com)-compatible skill.

## Demo

https://github.com/user-attachments/assets/demo.mp4

> *See it in action — booking a restaurant reservation via API call.*

## What it does

Your AI agent sends a phone number + objective. A voice AI makes the call, navigates IVR menus, talks to humans, and returns structured results: outcome, transcript, and recording.

## Quick start

```bash
curl -X POST https://agentphone.app/api/v1/calls \
  -H "Content-Type: application/json" \
  -H "x-api-key: $AGENTPHONE_API_KEY" \
  -d '{
    "to_phone_number": "+14155551234",
    "objective": "Book a table for 4 tonight at 7pm under the name Sarah",
    "business_name": "Luigis Italian"
  }'
```

**5 free credits on signup.** → [agentphone.app](https://agentphone.app)

## Install as OpenClaw skill

See [`SKILL.md`](./SKILL.md) for the full skill definition.

## Links

- [Website](https://agentphone.app)
- [API Docs](https://agentphone.app/docs)
- [Sign up](https://agentphone.app) — 5 free credits
