# Clinic Onboarding Checklist

Use this when adapting the system for a real dental clinic.

## Clinic basics

- clinic name
- address
- phone
- WhatsApp number
- timezone
- opening hours
- closed days / holidays
- parking / directions
- accepted payment methods

## Dentists

For each dentist:

- name
- specialties / services
- normal working schedule
- Google Calendar ID
- planned leave / closures

## Services

For each service:

- public name
- duration
- booking buffer
- clinic-approved price or price range
- which dentists provide it
- whether phone booking is allowed

## Appointment rules

- minimum notice
- maximum booking horizon
- cancellation policy
- reschedule policy
- late-arrival rule
- new-patient information required

## Reception rules

- when AI should hand off
- after-hours behavior
- urgent-call wording approved by clinic
- languages
- human contact / escalation destination

## Messaging

- WhatsApp Cloud API configuration
- approved templates where required
- confirmation copy
- reminder timing
- cancellation/reschedule instructions

## Testing before launch

Run at least:

- booking
- no availability
- reschedule
- cancellation
- duplicate booking attempt
- clinic closed
- dentist unavailable
- human request
- unclear caller
- urgent symptom language
