# BeyondK12 — Central GHL Assessment Build Brief

**Audience:** Ernie Delgado (approvals/calendar), Sajeel (GHL builder), Plinko (technical support/rollback).

**Status:** Draft build and internal QA only. This repository does **not** authorize publication, workflow activation, external follow-up, campaign sending, appointment creation, or modification of the live static assessment.

## Goal

Recreate the BeyondK12 AI Readiness Assessment in GoHighLevel so known-contact link clicks, quiz completion, score/routing, booking intent, booked appointments, and replies appear on one contact timeline.

## Current verified assessment behavior

Source: `assessment-source/index.html`, matching https://assessment.beyondk12.com as reviewed 2 September 2026.

- Eight required single-choice questions, 0–3 points each; total range 0–24.
- Tiers: AI Leader 18–24; AI Aware 12–17; AI At-Risk 6–11; AI Unprepared 0–5.
- Question distribution: Policy 2, People 2, Curriculum 1, Guardrails 2, Funding 1.
- Keep the static assessment live as rollback until GHL passes all tests and Ernie approves cutover.

## Quiz

Build: introduction → eight scored questions → lead capture → dynamic result page + GHL calendar CTA.

Lead fields: first name, last name, school email, title, school name, city/state, approximate enrollment. Use Ernie-approved privacy/consent text only.

Use the source question copy and answers exactly unless Ernie approves changes.

Do not repeat the existing uncited funding figure or unattributed testimonials without documented approval.

## Required contact fields

Create the `AI Readiness Assessment` group.

- Assessment version; source campaign; source link/Trigger Link name; first clicked at; started status; completed at; booking intent at.
- Assessment channel (`phone`, `voicemail`, `email`, `print`, `web`, `direct`); landing UTMs; optional referrer.
- School name; school city/state; enrollment; role/title.
- Overall score; tier; Policy/People/Curriculum/Guardrails/Funding scores; Lowest pillar; Routing branch; Pillar tie occurred; recommended next steps.

## Critical routing calculation

Never select `Lowest pillar` from raw category points. The maximums differ.

```text
Policy readiness      = Policy score / 6
People readiness      = People score / 6
Curriculum readiness  = Curriculum score / 3
Guardrails readiness  = Guardrails score / 6
Funding readiness     = Funding score / 3
```

Choose the smallest normalized readiness value. On a tie set `Pillar tie occurred = true` and choose in order:

```text
Policy → Guardrails → People → Curriculum → Funding
```

Use native GHL category percentages only after proving they implement the same calculations; otherwise calculate in an approved custom-code/workflow action before routing.

Routing:

1. Total below 8/24 → `Bypass—LowScore` (approved direct-consult path).
2. Otherwise: Policy or Guardrails → `A—Governance`; People → `B—Capability`; Curriculum → `C—Pathway`; Funding → `D—Funding`.

Tier controls urgency/pacing. Lowest pillar controls first content branch.

## Attribution

Known contacts emailed through GHL receive a dedicated GHL Trigger Link, named `BK12 — AI Assessment — Cold Email — September 2026`. Its click is `Assessment opened`—not proof the recipient answered a question.

For anonymous channels use channel URLs:

| Channel | Parameter |
|---|---|
| Phone | `?src=phone` |
| Voicemail | `?src=voicemail` |
| Print | `?src=print` |
| Website/organic | `?src=web` |
| No parameter | `direct` |

First prove the actual GHL quiz can capture `src` and UTM query values into contact fields. If it cannot, document the limitation before finalizing phone/print material. Never expose raw contact IDs in public URLs.

## Draft workflows

1. **Assessment link clicked:** Trigger Link click → first-click timestamp, opened status, tag, opportunity; after 24h with no completion/booking/reply → incomplete state + human task or approved single reminder.
2. **Quiz submitted:** create/update contact by email; map results/channel; completed state; remove incomplete path; normalized-pillar route; owner notification.
3. **Booking intent:** booking CTA click → intent timestamp/tag/stage; if still unbooked after approved wait, human task or approved reminder.
4. **Customer booked appointment:** booked stage/tag; stop conflicting reminders; approved confirmation/reminders; owner notification; prep task.
5. **Email reply:** human-review stage/tag; stop incomplete/booking reminder paths; create human task. Do not auto-send substantive replies.

## Calendar

Use the existing BeyondK12 GHL calendar for a 30-minute AI/Technology Plan Audit, or a dedicated calendar only after Ernie specifies owner, availability, timezone, buffers, minimum notice, notification recipient, confirmation/reminder copy, cancellation policy, and lead-response SLA.

A recipient must select the time. Automation must not create appointments.

## Acceptance tests

Use labeled internal test contacts only.

- Known Trigger Link click: timeline/tag/timestamp/stage read-back.
- Incomplete: test-copy short wait; exactly one intended action.
- Quiz submission: correct score, tier, five category fields, Lowest pillar, branch, tags, stage.
- Booking intent: click without booking creates no appointment.
- Cancellable booking: owner notification, task, stage and reminder suppression.
- Reply: human review plus suppression.
- `src` values: phone, print, email, absent.
- Normalization: Policy 3/6 versus Curriculum 2/3 must route Policy; Funding 1/3 routes Funding; Policy/Guardrails 3/6 tie resolves Policy with tie flag.
- Low score: below 8 uses bypass.
- Tier boundaries: 5, 6, 11, 12, 17, 18.
- Desktop/mobile, HTTPS, calendar flow, and rollback verified.

## Inputs Sajeel needs

From Ernie: correct GHL location access, calendar owner/availability/notifications, privacy/consent copy, approved follow-up copy, send-channel decision, approved claims/testimonials or instruction to omit, and channel wording.

From Plinko: retain static rollback; assist only where GHL cannot do normalized routing or query-parameter capture natively.

## Release gate

No live send until every acceptance test passes, the approved copy/privacy/calendar policy is configured, rollback remains available, and Ernie approves the exact campaign artifact, target segment, and send.
