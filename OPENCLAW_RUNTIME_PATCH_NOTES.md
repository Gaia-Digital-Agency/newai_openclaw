# OpenClaw Runtime Patch Notes

This file tracks live runtime patches applied on `aserver` for the WhatsApp/OpenClaw agent setup.

These are not source-level TypeScript changes. They are modifications to the installed OpenClaw bundle and must be re-applied or ported if OpenClaw is upgraded or reinstalled.

## Live Runtime Target

- Server: `aserver`
- SSH: `ssh -i ~/.ssh/gda-ce01 azlan@136.110.15.130`
- Installed bundle file:
  `/home/azlan/.npm-global/lib/node_modules/openclaw/dist/channel.runtime-Cu2TxQlg.js`

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

Verified on `2026-03-27` in the installed bundle:

- line `2208`
  - forced agent reply prefixes
- line `2209`
  - response prefix resolution
- line `2383`
  - owner WhatsApp number normalization
- line `2403`
  - group skip when `Brian` is not mentioned
- line `2408`
  - owner DM detection
- line `2416`
  - direct-message skip when no allowed named agent is present

Relevant grep markers from the live file:

- `forcedAgentPrefix`
- `ownerE164`
- `Skipping WhatsApp group message without Brian mention`
- `Skipping WhatsApp direct message without allowed named agent`

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

To make this durable beyond the installed bundle:

1. Locate the actual OpenClaw source repository used to build the installed package.
2. Port the DM/group routing logic into source.
3. Port the forced agent-prefix logic into source.
4. Rebuild and reinstall OpenClaw on `aserver`.
5. Replace this note with source commit references once available.
