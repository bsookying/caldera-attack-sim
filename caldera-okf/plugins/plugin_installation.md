---
type: Operations Guide
title: Plugin Installation
description: How to install and enable Caldera plugins in the Docker-based lab setup. The config persistence model is non-obvious and requires a specific sequence.
tags:
  - plugin
  - docker
  - operations
  - config
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31T00:00:00Z'
status: stable
---

# Plugin Installation

The Caldera instances run inside Docker containers. Config persistence works differently than a bare-metal install — editing files directly or restarting naively will lose changes.

## How Caldera Config Persistence Works

- On startup: Caldera reads `conf/local.yml` as the main config (since it's started with `-E local`).
- On shutdown (SIGTERM): `teardown()` writes the **current in-memory config** back to `conf/local.yml`.
- `local.yml` and `default.yml` are **single-file configs** (not merged) — `local.yml` is the authoritative runtime config.

**The trap:** If you edit `local.yml` directly while Caldera is running, your change is invisible to the running process. When you then `docker restart` (SIGTERM → graceful shutdown), Caldera overwrites `local.yml` with its in-memory config, erasing your edit.

## Correct Installation Sequence

### Step 1 — Install plugin files into the container

If the plugin directory isn't already present:

```bash
ssh -i ~/.ssh/id_rsa lab-user@192.168.8.121
PID=$(pgrep -f server.py | head -1)
PLUGIN_DIR=/proc/$PID/root/usr/src/app/plugins/myplugin
# clone or copy files in via sudo
```

Both containers already have all standard MITRE plugins pre-installed in their image.

### Step 2 — Install Python requirements (if any)

```bash
sudo docker exec focused_raman pip3 install -r /usr/src/app/plugins/myplugin/requirements.txt -q
```

### Step 3 — Add the plugin to Caldera's live config via API

This updates the in-memory config so teardown will persist it to `local.yml`:

```bash
curl -s -X PATCH http://192.168.8.121:8888/api/v2/config/main \
  -H "Content-Type: application/json" \
  -b "$(curl -sc - -X POST http://192.168.8.121:8888/enter -d 'username=admin&password=admin')" \
  -d '{"prop": "plugin", "value": "myplugin"}'
```

Or from Windows PowerShell:

```powershell
$s = New-Object Microsoft.PowerShell.Commands.WebRequestSession
Invoke-WebRequest -Uri "http://192.168.8.121:8888/enter" -Method POST -Body "username=admin&password=admin" `
  -ContentType "application/x-www-form-urlencoded" -WebSession $s -UseBasicParsing -MaximumRedirection 0 -EA SilentlyContinue | Out-Null
Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/config/main" -Method PATCH `
  -Body '{"prop": "plugin", "value": "myplugin"}' -ContentType "application/json" -WebSession $s -UseBasicParsing
```

### Step 4 — Restart the container

```bash
sudo docker restart focused_raman   # caldera-1
sudo docker restart charming_kowalevski   # caldera-2
```

The shutdown writes the updated config (with your plugin) to `local.yml`. The next startup reads it and loads the plugin.

### Step 5 — Verify

```powershell
# plugins API returns enabled: True for the new plugin
(Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/plugins" -WebSession $s -UseBasicParsing).Content |
  ConvertFrom-Json | Where-Object { $_.name -eq "myplugin" } | Select-Object name, enabled
```

## Container Names

| Instance | Container name |
|---|---|
| caldera-1 (192.168.8.121) | `focused_raman` |
| caldera-2 (192.168.3.21) | `charming_kowalevski` |

Both use image `caldera:latest` with restart policy `unless-stopped`.

## What NOT to Do

- Do not edit `conf/local.yml` or `conf/default.yml` while Caldera is running and then `docker restart` — the graceful shutdown overwrites `local.yml` with the in-memory config, erasing your edit.
- Do not `PATCH /api/v2/plugins/{name}` — that endpoint returns 405 (not supported).
- Do not `POST /api/v2/config/main` — use PATCH, not POST.
