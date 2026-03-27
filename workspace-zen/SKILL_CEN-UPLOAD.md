# SKILL_ZEN_UPLOAD.md

## Purpose

Use this skill when Zack is asked to create or upload a Zenbali event from an image.

## Scope

- Zenbali event creation from images shared on WhatsApp
- extracting event details from a provided image
- uploading the raw image file through the API when needed
- converting extracted fields into the Zen Bali agent API payload
- posting the event through the API
- confirming the returned event was created and published

## Canonical API Reference

Read `ZENBALI_API.md` before posting.

## Target System

- Public site: `http://34.124.244.233/zenbali/`
- API base: `http://34.124.244.233/zenbali/api`
- Upload endpoint: `POST /agent/uploads/event-image`
- Event endpoint: `POST /agent/events`
- Auth header: `X-Agent-Token: 8c5e16225ea2dd0736766878529408f95ed6720337f154cb51e1228d3d1f006c`

## Trigger Phrase

- `zack zenbali upload`
- `zack post this event`
- `zack create zenbali event`

## Working Procedure

1. Review the uploaded WhatsApp image and extract the event details.
2. Map the extracted values using the Zen Bali field mapping.
3. If Zack has the raw image file, upload it first to `POST /zenbali/api/agent/uploads/event-image`.
4. Read `data.image_url` from the upload response and use it as `e267` / `image_url`.
5. Build the JSON payload for `POST /zenbali/api/agent/events`.
6. Submit the payload through the API, not the browser.
7. Read the API response and confirm the event ID, title, date, and published status.
8. If the API returns validation errors, report the exact failing field and do not claim success.

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
- Use `http://34.124.244.233/zenbali/api/...`, not the blocked raw `:8081` port.
- `event_time` must be in 15-minute increments.
- `duration_minutes` must be in 15-minute increments.
- `location`, `event_type`, and `entrance_type` may be sent as names or numeric IDs.
- The API creates the event under `creator@zenbali.org` and publishes it immediately.
- If only the image file is available, upload it first and use the returned `image_url`.
- If no public image URL can be obtained, say so clearly.

## Known Good Validation Signals

- `201` with `data.image_url` means the image upload worked.
- `201` with event data means the post worked.
- `400 {"success":false,"error":"title is required"}` means the event endpoint is reachable and auth worked.

## Response Style

- Stay focused and operational.
- Keep the task scoped to Zenbali event creation.
- If upload or posting fails, report the exact API error instead of pretending the event was created.
