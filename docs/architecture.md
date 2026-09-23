# Architecture

## Product goal

The system is designed as a reusable dental-clinic receptionist, not a one-off chatbot.

The architecture separates:

```text
voice conversation   → Sarvam / ElevenLabs
business automation  → n8n
clinic rules         → Supabase/PostgreSQL
calendar truth       → Google Calendar
patient follow-up    → WhatsApp
```

## Tool-oriented voice integration

The voice agent should not be given direct database or calendar access.

Instead it calls narrow n8n tools:

- check_availability
- book_appointment
- reschedule_appointment
- cancel_appointment
- get_clinic_info
- request_human

n8n validates the request, applies clinic rules, calls external systems, and returns a small structured result.

## Appointment booking path

```text
voice request
  ↓
check availability
  ↓
offer slots
  ↓
patient selects one
  ↓
recheck selected slot
  ↓
Google Calendar create event
  ↓
Supabase appointment record
  ↓
WhatsApp confirmation
```

## Why recheck before booking?

Availability can change between the moment slots are offered and the moment a patient chooses one.

A booking workflow should therefore verify the selected interval immediately before creating the appointment.

## Failure boundary

If the calendar, database, or booking tool fails, the voice agent should not pretend the appointment succeeded.

Return a structured failure and offer human follow-up.

## Multi-clinic path

The portfolio version supports a `clinic_id` throughout the schema so the same core workflows can later serve multiple clinic configurations.

A production multi-tenant deployment would also add stricter authorization and tenant isolation.
