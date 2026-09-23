# Voice Provider Layer

The public repository keeps the voice layer provider-neutral.

## Primary demo option — Sarvam

Sarvam can be used as the conversational voice layer for an India-focused clinic demo, particularly when multilingual or Hindi/English code-mixed conversations are useful.

The voice agent should call the n8n tool webhooks rather than receive direct database access.

## Alternative — ElevenLabs

ElevenLabs can sit in the same position:

```text
phone call
→ voice agent
→ n8n tool
→ structured result
→ voice response
```

## Provider-independent tool schema

The important contract is not the provider-specific API.

It is the tool interface.

Example:

```json
{
  "tool": "check_availability",
  "arguments": {
    "clinic_id": "brightsmile-demo",
    "dentist_id": "dr-aditi",
    "service_id": "cleaning",
    "date": "2026-09-25",
    "part_of_day": "afternoon"
  }
}
```

The provider receives a structured response from n8n and turns it into natural speech.

## Why keep the provider replaceable?

Voice vendors change more quickly than clinic scheduling logic.

Keeping business rules in n8n makes it easier to:

- change voice provider
- change telephony provider
- support WhatsApp later
- reuse the same appointment backend
