---
type: API Reference
title: Config API
description: REST API endpoints for reading and updating Caldera server configuration including C2 contact settings.
tags:
  - caldera
  - api
  - config
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Config API

```
GET   /api/v2/config/main         # read server config
PATCH /api/v2/config/main         # update a config setting
GET   /api/v2/config/agents       # read agent-specific config
PATCH /api/v2/config/agents       # update agent config
```

## Current Server Config

| Key | Value |
|---|---|
| `app.contact.http` | `http://192.168.8.121:8888` |
| `app.contact.tcp` | `0.0.0.0:7010` |
| `app.contact.udp` | `0.0.0.0:7011` |
| `app.contact.websocket` | `0.0.0.0:7012` |
| `app.contact.dns.socket` | `0.0.0.0:8853` |
| `app.contact.dns.domain` | `mycaldera.caldera` |
| `app.contact.tunnel.ssh.socket` | `0.0.0.0:8022` |
| `app.contact.tunnel.ssh.user_name` | `sandcat` |
| `app.contact.tunnel.ssh.user_password` | `s4ndc4t!` |
| `app.contact.ftp.host` | `0.0.0.0` |
| `app.contact.ftp.port` | `2222` |
| `app.contact.ftp.user` | `caldera_user` |
| `app.contact.ftp.pword` | `caldera` |
| `app.contact.html` | `/weather` |
| `objects.planners.default` | `atomic` |
| `ability_refresh` | `60` (seconds) |
| `session_expiration_days` | `7` |
| `exfil_dir` | `/tmp/caldera` |
| `reports_dir` | `/tmp` |
| `api_upload_max_size_mb` | `100` |
| `reachable_host_traits` | `remote.host.fqdn`, `remote.host.ip` |

## Update a Config Value

```json
PATCH /api/v2/config/main
{
  "prop": "app.contact.http",
  "value": "http://192.168.8.121:8888"
}
```

## Agent Config

```
GET   /api/v2/config/agents
PATCH /api/v2/config/agents
```

Controls global agent defaults (sleep intervals, implant names, etc.).
