# Appointment Slot Engine

## Inputs

The availability workflow accepts:

- clinic
- dentist
- service
- requested date
- optional part of day

## Data used

### Service rules

Each service provides:

- duration in minutes
- optional preparation/buffer time
- whether all dentists can provide it

### Dentist schedule

A weekly schedule defines one or more working windows per weekday.

Example:

```text
Monday
09:00–13:00
14:00–18:00
```

### Clinic closures

Specific dates/times can block:

- public holidays
- clinic closures
- dentist leave
- staff meetings

### Existing appointments

Confirmed/rescheduled appointments are treated as busy time.

### Google Calendar

The dentist calendar is queried for booked intervals in the requested window.

## Slot generation

For each working window:

1. create candidate starts using the configured slot interval
2. calculate end time using service duration + buffer
3. reject candidates that exceed the working window
4. reject candidates overlapping a closure
5. reject candidates overlapping Supabase appointments
6. reject candidates overlapping Google Calendar busy intervals
7. apply morning/afternoon/evening filter when requested
8. return the first useful set of options

## Time zones

Every clinic has an explicit IANA timezone.

The demo uses:

```text
Asia/Kolkata
```

The workflow should not depend on the n8n server timezone.

## Booking revalidation

The booking endpoint rechecks the exact selected interval against Google Calendar immediately before creating the event.

A production deployment should additionally use transactional / locking strategies where required for high booking volume.
