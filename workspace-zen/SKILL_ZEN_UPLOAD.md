# SKILL_ZEN_UPLOAD.md

## Purpose

Use this skill when Zack is asked to create or upload a Zenbali event from an image.

## Scope

- Zenbali event creation from images shared on WhatsApp
- extracting event details from a provided image
- inferring missing event details conservatively
- applying approved default values when extraction is exhausted
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
2. Fill fields that are explicit in the poster first.
3. Infer missing fields only when the poster strongly implies the value.
4. If extraction is exhausted, apply the approved defaults in this file.
5. If Zack has the raw image file, upload it first to `POST /zenbali/api/agent/uploads/event-image`.
6. Read `data.image_url` from the upload response and use it as `e267` / `image_url`.
7. Build the JSON payload for `POST /zenbali/api/agent/events`.
8. Submit the payload through the API, not the browser.
9. Read the API response and confirm the event ID, title, date, and published status.
10. If the API returns validation errors, report the exact failing field and do not claim success.

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
- price from poster -> `price_thousands`

## Approved Default Values

Use these only after extraction and safe inference are exhausted.

- `e163` / `duration_days`: `0`
- `e166` / `duration_hours`: `1`
- `e191` / `duration_minutes`: `0`
- `price_thousands`: `0`

## Price Rule

- Use `price_thousands` as the preferred API field for ticket price.
- `price_thousands` must be an integer from `0` to `100000`.
- `1` means `IDR 1,000`.
- `150` means `IDR 150,000`.
- If the poster shows a rupiah amount, convert it into `price_thousands`.
- If the poster shows no price, use `price_thousands = 0`.
- Zack may also send `entrance_fee = price_thousands * 1000` for backward compatibility, but `price_thousands` is the canonical field.

## Extraction And Inference Rules

- If multiple dates appear on the poster, set `e10` / `event_date` to the first event date shown.
- Put the remaining dates into `e263` / `event_description` in clear human wording.
- If a price is shown, infer `e196` / `entrance_type` as `Paid`.
- If no price is shown and free entry is explicit, set `e196` / `entrance_type` to `Free`.
- If social links, social handles, website links, WhatsApp links, or Instagram, Facebook, TikTok references appear on the poster, include them in `e263` / `event_description`.
- If important poster information does not fit the structured fields, place it in `e263` / `event_description`.

## Description Rule For e263

Build a useful event description from poster information that is not already captured well by the other structured fields.

The description should include, when available:
- subtitle or supporting tagline
- facilitator or host context
- secondary dates beyond the first event date
- pricing notes
- reservation notes
- links, handles, website URLs, QR-linked destinations if visible as text, and social media references
- other useful event context that appears on the poster

Do not make up facts that are not visible or strongly implied.

## Important Implementation Notes

- Do not use browser automation for Zenbali event posting when the API is available.
- Use `http://34.124.244.233/zenbali/api/...`, not the blocked raw `:8081` port.
- `event_time` must be in 15-minute increments.
- `duration_minutes` must be in 15-minute increments.
- `location`, `event_type`, and `entrance_type` may be sent as names or numeric IDs.
- The API creates the event under `creator@zenbali.org` and publishes it immediately.
- If only the image file is available, upload it first and use the returned `image_url`.
- If no public image URL can be obtained, say so clearly.

## Success Response Format

On successful posting, respond with only:

Post Successfully
Event Title: <title>
Event Date: <event_date>
Event ID/UUID: <id>

Do not add extra summary text when the post succeeded.

## Known Good Validation Signals

- `201` with `data.image_url` means the image upload worked.
- `201` with event data means the post worked.
- `400 {"success":false,"error":"title is required"}` means the event endpoint is reachable and auth worked.

## Response Style

- Stay focused and operational.
- Keep the task scoped to Zenbali event creation.
- If upload or posting fails, report the exact API error instead of pretending the event was created.
