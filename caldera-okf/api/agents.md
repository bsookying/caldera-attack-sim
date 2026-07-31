---
type: API Reference
title: Agents API
description: REST API endpoints for listing, configuring, and terminating Caldera agents (implants) on target hosts.
tags:
  - caldera
  - api
  - agents
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Agents API

Agents are implants running on target hosts. See [concepts/agent](../concepts/agent.md) for full schema.

## Endpoints

### List All Agents
```
GET /api/v2/agents
```

Returns an array of all agent objects. Filter by query params:

| Param | Example | Description |
|---|---|---|
| `include` | `paw,host,platform` | Comma-separated fields to include |
| `exclude` | `links` | Comma-separated fields to omit |

### Get One Agent
```
GET /api/v2/agents/{paw}
```

### Update Agent Config
```
PATCH /api/v2/agents/{paw}

{
  "sleep_min": 5,
  "sleep_max": 15,
  "group": "blue",
  "watchdog": 0,
  "trusted": true
}
```

### Kill Agent Process
```
POST /api/v2/agents/kill/{paw}
```
Sends a kill instruction to the agent; the agent terminates its process.

### Delete Agent Record
```
DELETE /api/v2/agents/{paw}
```
Removes the agent record from Caldera without terminating the process.

## Deploying Agents

Agents are not deployed via the API — they are downloaded and run on targets directly. See [concepts/agent](../concepts/agent.md) for deployment commands.

## Example: List Agents (PowerShell)

```powershell
# (within an authenticated session)
$agents = Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/agents" `
    -WebSession $session -UseBasicParsing
$agents.Content | ConvertFrom-Json | Format-Table paw, host, platform, username, group, alive
```

## Example: Change Agent Group

```powershell
$body = '{"group":"target-workstations"}'
Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/agents/abc123" `
    -Method Patch -Body $body -ContentType "application/json" `
    -WebSession $session -UseBasicParsing
```
