# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.

---

## BSC — Blossom School Catering

**API Base:** `http://34.158.47.112/schoolcatering/api/v1`
**Site:** `http://34.158.47.112/schoolcatering/`
**Quick Order Page:** `http://34.158.47.112/schoolcatering/tools/quick-order`

### Agent Account (for placing orders via API)
- **Username:** `admin`
- **Password:** `teameditor123`
- **Role:** `ADMIN`
- Note: Admin role can place orders for ANY registered student by username — no family scoping.

### How to Place Orders
Use `SKILL-BSC-ORDER.md` — call the API via curl, NOT the browser.
The browser Add buttons are not automation-friendly. The API works perfectly.

### Key Endpoints
| Action | Method | Path |
|---|---|---|
| Login | POST | `/auth/login` |
| Place order (all-in-one) | POST | `/order/quick` |

### Token Handling
- Login returns `accessToken` — use as `Authorization: Bearer <token>`
- Tokens expire — if you get 401, re-login and retry once
