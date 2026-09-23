# Safety Boundaries

This system is a **clinic receptionist**, not a clinical decision-maker.

## Allowed

The receptionist can:

- explain clinic opening hours
- list configured services
- provide clinic-supplied pricing
- check dentist availability
- schedule/reschedule/cancel appointments
- take a message
- connect/escalate to staff

## Not allowed

The receptionist should not:

- diagnose a condition
- tell a caller they need a specific treatment
- prescribe medication
- interpret X-rays or reports
- guarantee a clinical outcome
- fabricate emergency advice

## Symptom questions

If a caller asks:

> Do I need a root canal?

the safe operational response is to explain that a dentist must assess the tooth and offer an appointment.

## Urgent symptoms

The clinic should configure an approved escalation message for urgent situations.

The automation should favor:

```text
urgent concern detected
→ do not diagnose
→ provide clinic-approved urgent-care instruction
→ human handoff / appropriate escalation
```

The public demo does not attempt to encode medical triage protocols.
