---
type: API Reference
title: Planners API
description: REST API endpoint for listing available Caldera planners.
tags:
  - caldera
  - api
  - planners
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Planners API

Planners are read-only via the API (they are defined in plugin code). See [concepts/planner](../concepts/planner.md) for descriptions of all 9 available planners.

```
GET /api/v2/planners            # list all planners
GET /api/v2/planners/{id}       # get one planner
```

## Key Planner IDs

| ID | Name |
|---|---|
| `aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a` | atomic (default) |
| `788107d5-dc1e-4204-9269-38df0186d3e7` | batch |
| `f36c34f5-9439-4417-9640-fe83f4b7b12d` | buckets |
| `5bdec528-07ce-44cc-8997-09fe41aa2007` | guided |
| `48e1a882-1606-4910-8f2d-2352eb80cba2` | bayes |
| `254c7035-de7d-4d76-a888-2c09ba594eca` | look ahead |
| `0a7d8b12-c0fa-4aec-a890-368f3735f7a9` | OilRig Planner |
| `6f46e3a4-f20e-49ed-8088-32a3094aad77` | Sandworm Planner |
| `43f4e42d-5c93-43a7-89f4-227071014893` | Wizard Spider Planner |
