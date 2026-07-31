---
type: API Reference
title: Operations API
description: REST API endpoints for creating, controlling, and querying Caldera operations and their execution chains.
tags:
  - caldera
  - api
  - operations
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Operations API

See [concepts/operation](../concepts/operation.md) for the full operation schema.

## Endpoints

### List All Operations
```
GET /api/v2/operations
```

### Get One Operation
```
GET /api/v2/operations/{id}
```

Includes the full `chain` (link history) by default.

### Create an Operation
```
POST /api/v2/operations
```

**Minimal payload:**
```json
{
  "name": "My Operation",
  "adversary": {"adversary_id": "0f4c3c67-845e-49a0-927e-90ed33c044e0"},
  "group": "red"
}
```

**Full payload:**
```json
{
  "name": "Lateral Movement Scenario",
  "adversary": {"adversary_id": "0f4c3c67-845e-49a0-927e-90ed33c044e0"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "ed32b9c3-9593-4c33-b0db-e2007315096b"},
  "obfuscator": "base64",
  "jitter": "2/8",
  "autonomous": 1,
  "auto_close": true,
  "visibility": 51
}
```

### Control Operation State
```
PATCH /api/v2/operations/{id}

{"state": "paused"}    # pause
{"state": "running"}   # resume
{"state": "finished"}  # stop
```

### Delete Operation
```
DELETE /api/v2/operations/{id}
```

### Get Operation Links (Chain)
```
GET /api/v2/operations/{id}/links
```

### Approve/Update a Link (manual mode)
```
PATCH /api/v2/operations/{id}/links/{link_id}

{"status": 0}   # approve for execution
{"status": -2}  # discard this link
```

### Add a Manual Command
```
POST /api/v2/operations/{id}/potential-links

{
  "paw": "abc123",
  "executor": {
    "name": "psh",
    "platform": "windows",
    "command": "whoami /all"
  }
}
```

### Get Operation Facts
```
GET /api/v2/facts/{operation_id}
```

## Common Operation Recipes

### Quick Discovery Sweep
```json
{
  "name": "Discovery Sweep",
  "adversary": {"adversary_id": "0f4c3c67-845e-49a0-927e-90ed33c044e0"},
  "group": "red",
  "planner": {"id": "788107d5-dc1e-4204-9269-38df0186d3e7"},
  "obfuscator": "plain-text",
  "autonomous": 1,
  "auto_close": true
}
```

### Stealth APT29 Emulation
```json
{
  "name": "APT29 Emulation",
  "adversary": {"adversary_id": "4975696e-1d41-11eb-adc1-0242ac120002"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "152ca62e-32b6-5c2a-84e8-89fedd44d621"},
  "obfuscator": "base64jumble",
  "jitter": "5/15",
  "autonomous": 1,
  "visibility": 51
}
```

## Link Status Codes

| Code | Meaning |
|---|---|
| `0` | Success |
| `-1` | Not yet run |
| `-2` | Discarded by operator |
| `-3` | Timeout |
| `-4` | Failed / error |
| `-5` | Untrusted agent |
