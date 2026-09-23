# AI Dental Receptionist

A **voice-first AI receptionist for dental clinics** built with **n8n, a voice AI provider, Google Calendar, Supabase/PostgreSQL, and WhatsApp confirmations**.

The system is designed to handle routine reception work while keeping real clinic rules and human escalation in control.

It can:

- answer common clinic questions
- check real appointment availability
- offer valid appointment slots
- book appointments
- reschedule appointments
- cancel appointments
- send WhatsApp confirmations and reminders
- recover after-hours or missed-call enquiries
- hand the conversation to clinic staff when needed

> **Portfolio implementation:** this repository is a sanitized public implementation based on appointment, WhatsApp, reminder, and conversational automation patterns I have built and worked with in private projects. It contains fictional clinic data and no real patient information, production credentials, private prompts, or proprietary project code.

## Demo clinic

The public demo uses a fictional clinic:

**BrightSmile Dental Clinic**

with fictional dentists, services, prices, schedules, and contact details.

The architecture is configuration-driven so the same workflow can later be configured for another dental clinic without rebuilding the core logic.

## Voice architecture

The recommended deployment keeps the live conversation layer separate from business automation:

```text
Patient phone call
      ↓
Voice AI provider
(Sarvam / ElevenLabs)
      ↓
Tool call
      ↓
n8n
      ↓
┌─────────────────────────────┐
│ check_availability          │
│ book_appointment            │
│ reschedule_appointment      │
│ cancel_appointment          │
│ get_clinic_info             │
│ request_human               │
└─────────────────────────────┘
      ↓
Google Calendar
+
Supabase
+
WhatsApp
      ↓
Structured result returned
to the voice agent
```

The voice provider handles the natural conversation. **n8n remains the business-automation layer.**

See [docs/voice-providers.md](docs/voice-providers.md).

## Why the slot engine matters

The receptionist must never invent availability.

For example:

> Patient: “I need a cleaning tomorrow afternoon.”

The system:

1. identifies the requested service
2. loads the service duration
3. loads the dentist's clinic schedule
4. applies clinic buffers / closures
5. checks existing appointments
6. checks Google Calendar busy time
7. returns only valid slots

Example result:

```json
{
  "available": true,
  "slots": [
    {"start": "2026-09-24T14:30:00+05:30", "end": "2026-09-24T15:00:00+05:30"},
    {"start": "2026-09-24T15:30:00+05:30", "end": "2026-09-24T16:00:00+05:30"},
    {"start": "2026-09-24T17:00:00+05:30", "end": "2026-09-24T17:30:00+05:30"}
  ]
}
```

Before booking, the chosen slot is checked **again** to reduce double-booking risk.

## Appointment lifecycle

```text
NEW_REQUEST
     ↓
COLLECTING_DETAILS
     ↓
CHECKING_AVAILABILITY
     ↓
SLOT_OFFERED
     ↓
CONFIRMED
```

Side states:

```text
RESCHEDULING
CANCELLED
COMPLETED
NO_SHOW
HUMAN_HANDOFF
```

## What this project demonstrates

- voice-agent tool architecture
- n8n webhook tools
- dynamic appointment-slot generation
- service-duration rules
- dentist working schedules
- calendar conflict checking
- booking revalidation
- Google Calendar event creation/update/delete
- Supabase appointment persistence
- rescheduling and cancellation
- WhatsApp confirmation/reminder workflows
- after-hours enquiry handling
- human escalation
- medical-boundary handling
- reusable per-clinic configuration

## Clinic configuration

Instead of hard-coding the workflow for one dentist, clinic-specific information lives in configuration and database tables:

```text
clinic_name
timezone
address
opening_hours
phone
WhatsApp number
services
service duration
service pricing
dentists
dentist working hours
calendar IDs
appointment buffer
closure dates
human escalation contact
```

See [config/sample-clinic.json](config/sample-clinic.json).

## Medical boundary

This project is a **receptionist**, not a dentist.

It should not diagnose conditions or recommend treatment.

If someone asks:

> “Do I need a root canal?”

the assistant should explain that a dentist needs to assess the problem and offer to arrange an appointment.

Potentially urgent or unsafe situations should move to the clinic's configured escalation message / human process rather than receiving invented medical advice.

See [docs/safety-boundaries.md](docs/safety-boundaries.md).

## Repository structure

```text
.
├── workflow/
│   ├── check-availability.json
│   ├── book-appointment.json
│   ├── manage-appointment.json
│   ├── clinic-info-and-handoff.json
│   └── appointment-reminder-worker.json
├── supabase/
│   └── schema.sql
├── config/
│   └── sample-clinic.json
├── docs/
│   ├── architecture.md
│   ├── slot-engine.md
│   ├── voice-providers.md
│   ├── safety-boundaries.md
│   └── clinic-onboarding.md
├── demo/
│   ├── demo-script.md
│   ├── sample-conversations.md
│   └── clinic-pitch.md
├── examples/
│   ├── check-availability.json
│   ├── book-appointment.json
│   └── reschedule-appointment.json
├── .env.example
├── LICENSE
└── README.md
```

## Tool contracts

The voice agent can call n8n tools with structured JSON.

### Check availability

```json
{
  "clinic_id": "brightsmile-demo",
  "dentist_id": "dr-aditi",
  "service_id": "cleaning",
  "date": "2026-09-25",
  "part_of_day": "afternoon"
}
```

### Book

```json
{
  "clinic_id": "brightsmile-demo",
  "patient_name": "Rahul",
  "patient_phone": "+919999000001",
  "dentist_id": "dr-aditi",
  "service_id": "cleaning",
  "start_time": "2026-09-25T15:30:00+05:30"
}
```

### Reschedule

```json
{
  "action": "reschedule",
  "patient_phone": "+919999000001",
  "appointment_id": "demo-appointment-id",
  "new_start_time": "2026-09-26T10:30:00+05:30"
}
```

## Google Calendar + Supabase

The project uses the two systems for different jobs:

**Supabase** stores clinic rules and appointment records.

**Google Calendar** is used as a real-world calendar conflict layer and event destination.

That separation lets the system understand business rules such as service duration and dentist schedules while still respecting calendar events that may have been created outside the AI receptionist.

## WhatsApp follow-up

After confirmation, the system can send:

- appointment confirmation
- reminder before appointment
- reschedule/cancel instructions

The public workflow keeps credentials external and does not include any real Meta configuration.

## Sales-demo goal

This repository is also structured so it can be demonstrated to a clinic owner.

A short demo can show:

1. booking a cleaning
2. checking real availability
3. rescheduling
4. answering clinic hours / fee questions
5. handling a request to speak with a human
6. refusing to diagnose and offering an appointment instead

See [demo/demo-script.md](demo/demo-script.md).

## Planned validation

This repository is **not yet marked as tested**.

The later validation pass will cover:

- valid slot lookup
- fully booked period
- service-duration differences
- dentist unavailable
- clinic closure
- Google Calendar conflict
- recheck before booking
- successful booking
- attempted double booking
- reschedule
- cancel
- clinic FAQ
- human handoff
- medical-boundary response
- WhatsApp confirmation
- appointment reminder

Screenshots, sample calls, and execution evidence will be added after live validation.

## License

MIT License. See [LICENSE](LICENSE).
