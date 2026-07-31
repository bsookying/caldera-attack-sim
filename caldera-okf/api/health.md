---
type: API Reference
title: Health Endpoint
description: GET /api/v2/health returns server version, access level, and enabled plugin list.
tags:
  - caldera
  - api
  - health
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Health Endpoint

```
GET /api/v2/health
```

Returns server status, version, authenticated user's access group, and loaded plugins.

## Response

```json
{
  "application": "Caldera",
  "version": "5.3.0",
  "access": "RED",
  "plugins": [
    {
      "name": "sandcat",
      "description": "A custom multi-platform RAT",
      "enabled": true,
      "address": "/plugin/sandcat/gui"
    },
    ...
  ]
}
```

## Fields

| Field | Description |
|---|---|
| `application` | Always `"Caldera"` |
| `version` | Caldera version string |
| `access` | Authenticated user's access level: `RED` or `BLUE` |
| `plugins[].name` | Plugin identifier |
| `plugins[].enabled` | Whether the plugin is currently active |
| `plugins[].address` | URL path to the plugin's UI (if any) |

## Use Cases

- Verify connectivity and authentication in one call
- Confirm access level before attempting write operations
- Check which plugins are available for a given session
