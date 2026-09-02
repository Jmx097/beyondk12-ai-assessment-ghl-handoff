# BeyondK12 AI Readiness Assessment

Private implementation handoff for the BeyondK12 GoHighLevel (GHL) assessment rebuild.

## What this repository contains

- `assessment-source/` — the current production static assessment source, preserved as a rollback/reference implementation.
- `docs/CENTRAL_BUILD_BRIEF.md` — the authoritative GHL build, tracking, workflow, and QA requirements.
- `docs/BeyondK12-Assessment-GHL-Implementation-Guide-v2.pdf` — supplied implementation guide, retained as a reference.

## Builder rules

1. Build the GHL quiz, fields, pipeline, calendar connection, Trigger Links, and workflows as **drafts only**.
2. Do not publish, activate workflows, send campaign email, make a public URL cutover, or modify the live rollback assessment until Ernie approves the exact tested artifact.
3. Do not route on raw pillar score totals. Follow the normalized pillar calculation in the central brief.
4. Do not recreate uncited funding claims or unattributed testimonials from the static page without Ernie's documented approval.

## Current production and rollback

- Branded assessment: https://assessment.beyondk12.com
- Legacy rollback: https://beyondk12-ai-assessment.plinkosolutions.com

The static source currently posts completed submissions to an n8n webhook. That endpoint is a runtime integration reference, not a credential. Do not replace it, invoke it for testing, or expose additional configuration without approval.

## GHL target

The GHL implementation becomes the source of truth for known-contact Trigger Link clicks, quiz completion, score/tier/pillar outcomes, booking intent, booked appointments, replies, and governed follow-up status.

Read `docs/CENTRAL_BUILD_BRIEF.md` before implementing any workflow.
