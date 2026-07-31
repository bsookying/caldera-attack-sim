---
type: API Reference
title: Facts & Sources API
description: REST API endpoints for managing fact sources (seed data) and retrieving facts collected during operations.
tags:
  - caldera
  - api
  - facts
  - sources
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Facts & Sources API

See [concepts/fact](../concepts/fact.md) for full schema and trait reference.

## Fact Sources

```
GET    /api/v2/sources              # list all sources (13 available)
GET    /api/v2/sources/{id}         # get one source
POST   /api/v2/sources              # create a source
PATCH  /api/v2/sources/{id}         # update a source
DELETE /api/v2/sources/{id}         # delete
```

### Create a Lab-Specific Fact Source

```json
POST /api/v2/sources
{
  "name": "Lab Targets",
  "facts": [
    {"trait": "remote.host.ip", "value": "192.168.3.31", "score": 1},
    {"trait": "remote.host.ip", "value": "192.168.3.32", "score": 1},
    {"trait": "domain.user.name", "value": "lab-user", "score": 1},
    {"trait": "domain.user.password", "value": "Paloalto1!", "score": 1},
    {"trait": "domain.user.name", "value": "administrator", "score": 1},
    {"trait": "domain.user.password", "value": "Paloalto1!", "score": 1}
  ]
}
```

## Operation Facts

```
GET    /api/v2/facts/{operation_id}     # get all facts from an operation
POST   /api/v2/facts                    # add facts to a running operation
DELETE /api/v2/facts                    # remove facts from an operation
```

### Add Facts to a Running Operation

```json
POST /api/v2/facts
{
  "operation_id": "<uuid>",
  "facts": [
    {"trait": "remote.host.ip", "value": "192.168.1.31", "score": 1}
  ]
}
```

## Key Source IDs

| ID | Name |
|---|---|
| `ed32b9c3-9593-4c33-b0db-e2007315096b` | basic |
| `152ca62e-32b6-5c2a-84e8-89fedd44d621` | APT29 (Emu) |
| `fda0f88b-4cd7-5fb7-9424-ae23f61218be` | OilRig (Emu) |
| `ec9d08d5-a574-5d8f-9697-bba9b4f033f7` | Sandworm Team (Emu) |
| `6a6bf917-a864-546f-b291-3419d642d67a` | Carbanak (Emu) |
| `7cfe465a-9b3d-5a8f-8d65-10b5ba37a5d5` | FIN6 (Emu) |
| `68908278-cf73-5145-9fe9-ebff1c533b06` | Wizard Spider (Emu) |
| `a66d2d89-ce1f-50d1-8730-a79e45a1b5a2` | menuPass (Emu) |
| `2ccb822c-088a-4664-8976-91be8879bc1d` | Exfil Operation |
