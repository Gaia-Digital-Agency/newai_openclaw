# Operations Runbook

## Purpose

This workspace is the local coordination point for multi-server AI functionality.

Local workspace:

`/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw`

Primary use:

- Manage AI-related server work
- Track server roles and target directories
- Reduce mistakes when switching between AI, staging, production, and hosting environments

## Quick Reference

| Alias | Server Name | SSH | Main Path | Primary Use |
| --- | --- | --- | --- | --- |
| `aiserver` / `aserver` | AI server | `ssh -i ~/.ssh/gda-ce01 azlan@136.110.15.130` | `/home/azlan/.openclaw` | Openclaw AI agents |
| `sserver` / `stagingserver` | Staging server | `ssh -i ~/.ssh/gda-ce01 azlan@34.124.244.233` | `/var/www/zenbali` | Staging and prototype Next.js projects |
| `pserver` / `productionserver` | Production server | `ssh -i ~/.ssh/gda-ce01 azlan@34.158.47.112` | `/var/www/schoolcatering` | Production apps and launched sites |
| `hserver` | Hostinger server | `ssh -i ~/.ssh/id_ed25519_gaia root@88.222.245.253` | Not fixed yet | Multiple WordPress installations |

## Operating Rules

### Shared Environment Caution

The staging and production servers are multisite environments using `nginx` and `pm2`.

Before making changes on `sserver` or `pserver`:

- Confirm the target site and directory
- Check whether the change affects shared `nginx` config
- Check whether the change affects a shared `pm2` process or ecosystem config
- Avoid restarting unrelated apps or services
- Treat reloads and process restarts as potentially cross-site actions

### Naming Convention

Use these short names consistently in notes and chat:

- `aserver` = AI server
- `sserver` = staging server
- `pserver` = production server
- `hserver` = Hostinger server

## Server Runbook

### `aserver`

Role:

- Main AI development server
- Used for building AI agents with Openclaw

Access:

`ssh -i ~/.ssh/gda-ce01 azlan@136.110.15.130`

Main path:

`/home/azlan/.openclaw`

Operational focus:

- Openclaw agent development
- AI-related server-side workflows

Notes:

- Treat this as the primary AI environment

### `sserver`

Role:

- Staging and prototype environment
- Current focus is `zenbali`

Access:

`ssh -i ~/.ssh/gda-ce01 azlan@34.124.244.233`

Main path:

`/var/www/zenbali`

Operational focus:

- Staging deployments
- Prototype Next.js projects
- ZenBali work

Risk notes:

- This is a multisite environment
- Changes to `nginx` or `pm2` may affect more than one site
- Validate process names and config targets before reload/restart actions

### `pserver`

Role:

- Production environment for launched apps and sites
- Current focus is Blossom School Catering

Access:

`ssh -i ~/.ssh/gda-ce01 azlan@34.158.47.112`

Main path:

`/var/www/schoolcatering`

Operational focus:

- Production maintenance
- Live deployments
- Blossom School Catering operations

Risk notes:

- This is a multisite environment
- Changes to `nginx` or `pm2` may affect live services outside the target app
- Confirm exact target before reload, restart, or config edits

### `hserver`

Role:

- Hostinger-based infrastructure
- Used for multiple WordPress installations

Access:

`ssh -i ~/.ssh/id_ed25519_gaia root@88.222.245.253`

Operational focus:

- Multi-WordPress hosting tasks
- Additional focus areas to be defined later

Notes:

- Project-specific target directories will be documented later as needed

## Default Targets By Server

- AI work: `aserver` -> `/home/azlan/.openclaw`
- ZenBali staging/prototype work: `sserver` -> `/var/www/zenbali`
- Blossom School Catering production work: `pserver` -> `/var/www/schoolcatering`
- WordPress hosting work: `hserver` -> determine target app before making changes

## Repository Mapping

### `newai_openclaw`

Local path:

`/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw`

Server path:

`aserver` -> `/home/azlan/.openclaw`

Git remote label:

`newai_openclaw`

Source of truth:

- Server is source of current truth

### `zenbali`

Local path:

`/Users/rogerwoolie/Documents/gaiada_projects/zenbali_saas`

Server path:

`sserver` -> `/var/www/zenbali`

Source of truth:

- Server is source of current truth

### `schoolcatering`

Local path:

`/Users/rogerwoolie/Documents/gaiada_projects/blossom-schoolcatering`

Server path:

`pserver` -> `/var/www/schoolcatering`

Source of truth:

- Server is source of current truth

## Git Sync Policy

Core rule:

- Server state is the current source of truth

Sync direction:

1. Make or verify changes on the target server first
2. Commit on the server if the server repo is Git-tracked
3. Push server state to GitHub
4. Pull the same state down to local
5. Keep local aligned with server and GitHub after each completed change set

Per-project mapping:

- `newai_openclaw`: local `/Users/rogerwoolie/Documents/gaiada_projects/newai_openclaw` <-> `aserver:/home/azlan/.openclaw`
- `zenbali`: local `/Users/rogerwoolie/Documents/gaiada_projects/zenbali_saas` <-> `sserver:/var/www/zenbali`
- `schoolcatering`: local `/Users/rogerwoolie/Documents/gaiada_projects/blossom-schoolcatering` <-> `pserver:/var/www/schoolcatering`

Operational note:

- If local and server differ, treat the server copy as authoritative unless explicitly decided otherwise
- Do not assume local is current until server state has been checked
- Push and pull should be used to synchronize completed server work back to GitHub and local

## Pre-Change Checklist

Before making server changes:

1. Confirm the correct server alias
2. Confirm the target directory
3. Confirm whether the server is shared or multisite
4. Check whether `nginx` or `pm2` changes can affect other apps
5. Confirm whether the server repo is the current Git source of truth
6. Only then proceed with edits, reloads, restarts, commit, push, and sync
