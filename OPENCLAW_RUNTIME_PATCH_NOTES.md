# OpenClaw Runtime Patch Notes

This file tracks live runtime patches applied on `aserver` for the WhatsApp/OpenClaw agent setup.

These are not source-level TypeScript changes. They are modifications to the installed OpenClaw bundle and must be re-applied or ported if OpenClaw is upgraded or reinstalled.

Upstream package metadata points to:

- Repository: [openclaw/openclaw](https://github.com/openclaw/openclaw)
- Previously installed package version on `aserver`: `2026.3.23-2`

## Live Runtime Target

- Server: `aserver`
- SSH: `ssh -i ~/.ssh/gda-ce01 azlan@136.110.15.130`
- Current installed package version:
  `2026.3.26`
- Current installed bundle files containing the ported behavior:
  - `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-BIK8FKW6.js`
  - `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel-reply-pipeline-BlO2qGni.js`

## Active Behavior

### Direct Messages

- Owner DM `+628176917122` can address:
  - `Michael` -> `main`
  - `Brian` -> `bsc`
  - `Zack` -> `zen`
- Owner DM with no agent name gets no response.
- Non-owner DM:
  - `Michael` -> `main`
  - `Brian` / `Zack` -> no response
  - no name -> no response

### Group Messages

- Group messages respond only when `Brian` is explicitly mentioned.
- `Brian` mention -> `bsc`
- No `Brian` mention -> no response
- `Michael` and `Zack` do not respond in group chat.

### Response Prefixes

- `main` replies are prefixed with `Michael ♾️`
- `bsc` replies are prefixed with `Brian ♾️`
- `zen` replies are prefixed with `Zack ♾️`

## Live Patch Anchors

Verified on `2026-03-27` in the installed `2026.3.26` bundle:

- `channel.runtime-BIK8FKW6.js`
  - line `2225`
    - group skip when `Brian` is not mentioned
  - line `2240`
    - direct-message skip when no allowed named agent is present
- `channel-reply-pipeline-BlO2qGni.js`
  - line `57`
    - identity-based WhatsApp prefix `${identityName} ♾️`

Relevant grep markers from the live files:

- `Skipping WhatsApp group message without Brian mention`
- `Skipping WhatsApp direct message without allowed named agent`
- `♾️`

## Bundle Backups

The following backups exist on `aserver`:

- `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js.pre-group-brian-patch.bak`
- `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js.pre-dm-owner-routing.bak`
- `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js.pre-soft-brian-security.bak`
- `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js.pre-group-prefix.bak`
- `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js.pre-agent-prefixes.bak`

## Related Workspace State

Tracked workspace state in this repo reflects the live agent split:

- `workspace-main` -> Michael
- `workspace-bsc` -> Brian
- `workspace-zen` -> Zack

Additional behavior notes:

- Brian is BSC-only in active workspace memory.
- Zack has the canonical Zen skill file:
  - `workspace-zen/SKILL_ZEN_UPLOAD.md`

## Durable Follow-Up

This is now ported into upstream source and deployed, but there are still two follow-ups:

1. Upstream source branch:
   - local repo: `/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw/openclaw_upstream`
   - branch: `codex/whatsapp-routing-source-port`
   - commit: `1dbaaa9240`
2. Packaging gap:
   - `aserver` logs warn that `dist/control-ui/index.html` is missing in the deployed package.
   - gateway and WhatsApp routing are healthy, but the Control UI asset packaging should be fixed in a future rebuild path.

## Tracked Patch Artifact

This repo tracks both the original live-bundle patch and the clean upstream source patch:

- [openclaw-2026.3.23-2-whatsapp-routing.patch](/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw/patches/openclaw-2026.3.23-2-whatsapp-routing.patch)
- [openclaw-upstream-1dbaaa9240-whatsapp-routing.patch](/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw/patches/openclaw-upstream-1dbaaa9240-whatsapp-routing.patch)
