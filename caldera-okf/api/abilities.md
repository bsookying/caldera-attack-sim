---
type: API Reference
title: Abilities API
description: REST API endpoints for browsing, filtering, creating, and managing Caldera abilities.
tags:
  - caldera
  - api
  - abilities
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Abilities API

This instance has **2,360 abilities** across 16 tactics and 3 platforms. See [concepts/ability](../concepts/ability.md) for the full schema.

## Endpoints

### List All Abilities
```
GET /api/v2/abilities
```

Returns all abilities. Response can be large (2,360 items). Use query filters:

| Param | Example | Description |
|---|---|---|
| `include` | `ability_id,name,tactic` | Fields to include |
| `exclude` | `executors` | Fields to omit |

### Get One Ability
```
GET /api/v2/abilities/{ability_id}
```

### Create a Custom Ability
```
POST /api/v2/abilities

{
  "name": "Enumerate Local Admins",
  "description": "List members of the local Administrators group",
  "tactic": "discovery",
  "technique_id": "T1069.001",
  "technique_name": "Permission Groups Discovery: Local Groups",
  "executors": [
    {
      "platform": "windows",
      "name": "psh",
      "command": "net localgroup Administrators",
      "timeout": 60
    }
  ]
}
```

### Update an Ability
```
PATCH /api/v2/abilities/{ability_id}
```

### Delete an Ability
```
DELETE /api/v2/abilities/{ability_id}
```

## Useful Ability IDs (this instance)

### Discovery
| ID | Name | Technique |
|---|---|---|
| `ba0b398d-91b8-490a-bed2-f959afa8e1aa` | System Network Connections Discovery | T1049 |
| `567eaaba-94cc-4a27-83f8-768e5638f4e1` | NMAP scan | T1595 |

### Lateral Movement
| ID | Name | Technique |
|---|---|---|
| `8cd933afe764c4159000cadea55f8ca5` | Change RDP Port (cmd) | T1021.001 |
| `aabe23a58568de3f9cc40e42e5f1b223` | Change RDP Port (PowerShell) | T1021.001 |

### Credential Access
| ID | Name | Technique |
|---|---|---|
| `2d78dfbfea7fa4ec7349993b83048079` | ADFS token cert theft (local) | T1552.004 |

### Execution
| ID | Name | Technique |
|---|---|---|
| `c0177717b47f2cd07949186523fa3c6b` | PowerShell -Command variations | T1059.001 |

### Persistence
| ID | Name | Technique |
|---|---|---|
| `e5d6ce329c91d382aaa32b4915927cc7` | AD Create Admin Account | T1136.002 |

## Example: Find All Windows Discovery Abilities

```powershell
$abilities = (Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/abilities" `
    -WebSession $session -UseBasicParsing).Content | ConvertFrom-Json

$abilities | Where-Object {
    $_.tactic -eq "discovery" -and
    ($_.executors | Where-Object { $_.platform -eq "windows" })
} | Select-Object ability_id, name, technique_id | Format-Table
```
