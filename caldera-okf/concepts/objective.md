---
type: Caldera Concept
title: Objective
description: An objective defines goal conditions — a set of facts that must be collected — that automatically terminate an operation when met.
tags:
  - caldera
  - objective
  - goal
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Objective

An **objective** defines success conditions for a Caldera [operation](operation.md). When the operation collects enough [facts](fact.md) satisfying the objective's goals, the operation terminates automatically. This instance has **1 objective** (the default).

## Schema

| Field | Type | Description |
|---|---|---|
| `id` | UUID | Unique identifier |
| `name` | string | Objective name |
| `description` | string | What success means |
| `goals` | array | List of goal conditions |

## Goal Schema

| Field | Type | Description |
|---|---|---|
| `target` | string | Fact trait to check (e.g., `domain.user.name`) |
| `value` | string | Required value (or `*` for any) |
| `count` | int | How many facts matching this trait must be collected |
| `achieved` | bool | Whether this goal has been met (runtime) |

## Example Objective

```json
{
  "name": "Credential Harvest",
  "description": "Collect at least 3 domain credentials",
  "goals": [
    {
      "target": "domain.user.name",
      "value": "*",
      "count": 3
    }
  ]
}
```

## Usage

Attach an objective to an operation at creation:

```json
POST /api/v2/operations
{
  "name": "Credential Hunt",
  "adversary": {"adversary_id": "..."},
  "objective": {"id": "<objective-uuid>"},
  "auto_close": true
}
```

With `auto_close: true`, the operation stops as soon as all goals are achieved.

## API

```
GET    /api/v2/objectives            # list all objectives
GET    /api/v2/objectives/{id}       # get one
POST   /api/v2/objectives            # create
PATCH  /api/v2/objectives/{id}       # update
DELETE /api/v2/objectives/{id}       # delete
```
