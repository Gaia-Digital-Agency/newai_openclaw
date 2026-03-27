# SKILL_CEN-UPLOAD.md

## Purpose

Use this skill when Zack is asked to create or upload a Zenbali event from an image.

## Scope

- Zenbali event creation from images shared on WhatsApp
- extracting event details from a provided image
- converting extracted fields into the Zen Bali agent API payload
- posting the event through the API
- confirming the returned event was created and published

## Canonical API Reference

Read `ZENBALI_API.md` before posting.

## Target System

- Public site: `http://34.124.244.233/zenbali/`
- API base: `http://34.124.244.233/zenbali/api`
- Post endpoint: `POST /agent/events`
- Auth header: `X-Agent-Token: 8c5e16225ea2dd0736766878529408f95ed6720337f154cb51e1228d3d1f006c`

## Trigger Phrase

- `zack zenbali upload`
- `zack post this event`
- `zack create zenbali event`

## Working Procedure

1. Review the uploaded image and extract the event details.
2. Map the extracted values using the Zen Bali field mapping.
3. If the image is only in WhatsApp, obtain a public `image_url` before posting.
4. Build the JSON payload for `POST /zenbali/api/agent/events`.
5. Submit the payload through the API, not the browser.
6. Read the API response and confirm the event ID, title, date, and published status.
7. If the API returns validation errors, report the exact failing field and do not claim success.

## Required Field Mapping

- `e9` -> `title`
- `e10` -> `event_date`
- `e11` -> `event_time`
- `e109` -> `location`
- `e136` -> `event_type`
- `e163` -> `duration_days`
- `e166` -> `duration_hours`
- `e191` -> `duration_minutes`
- `e196` -> `entrance_type`
- `e253` -> `participant_group_type`
- `e259` -> `lead_by`
- `e260` -> `venue`
- `e261` -> `contact_email`
- `e262` -> `contact_mobile`
- `e263` -> `event_description`
- `e267` -> `image_url`

## Important Implementation Notes

- Do not use browser automation for Zenbali event posting when the API is available.
- Use `http://34.124.244.233/zenbali/api/agent/events`, not the blocked raw `:8081` port.
- `event_time` must be in 15-minute increments.
- `duration_minutes` must be in 15-minute increments.
- `location`, `event_type`, and `entrance_type` may be sent as names or numeric IDs.
- The API creates the event under `creator@zenbali.org` and publishes it immediately.
- If `image_url` is missing, state that the image still needs to be hosted or resolved publicly.

## Known Good Validation Signals

- `400 {"success":false,"error":"title is required"}` means the endpoint is reachable and auth worked.
- A success response returns the created event payload including ID and published fields.

## Response Style

- Stay focused and operational.
- Keep the task scoped to Zenbali event creation.
- If posting fails, report the exact API error instead of pretending the event was created.
