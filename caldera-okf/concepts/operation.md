---
type: Caldera Concept
title: Operation
description: An operation is an execution run that drives an adversary profile against a group of agents using a chosen planner, fact source, and obfuscator.
tags:
  - caldera
  - operation
  - execution
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Operation

An **operation** is the top-level execution container in Caldera. It ties together an [adversary](adversary.md), a group of [agents](agent.md), a [planner](planner.md), a [fact source](fact.md), and an [obfuscator](obfuscator.md) to run an emulation scenario.

## Schema

| Field | Type | Description |
|---|---|---|
| `id` | UUID | Unique operation ID |
| `name` | string | Human-readable name |
| `adversary` | object | The adversary profile to execute |
| `group` | string | Agent group to target |
| `planner` | object | Planner to use for ability scheduling |
| `source` | object | Fact source providing seed facts |
| `obfuscator` | string | Obfuscation to apply to commands |
| `jitter` | string | Jitter range for agent execution timing (e.g., `2/8`) |
| `state` | string | `running`, `finished`, `paused`, `cleanup` |
| `autonomous` | int | `1` = fully automated, `0` = manual approval per link |
| `visibility` | int | Minimum link visibility score to execute (1–100) |
| `auto_close` | bool | Auto-close operation when adversary completes |
| `objective` | object | Optional goal conditions for termination |
| `start` | datetime | When the operation started |
| `finish` | datetime | When the operation finished |
| `chain` | array | All links (ability executions) in this operation |

## Link (Chain Entry) Schema

Each item in `chain` represents one ability execution attempt:

| Field | Description |
|---|---|
| `id` | Link ID |
| `ability` | The ability that was run |
| `paw` | Agent that ran it |
| `command` | Decoded command that was executed |
| `output` | stdout/stderr from execution |
| `status` | `0`=success, `-2`=discarded, `-3`=timeout, `-4`=failed |
| `score` | Visibility score |
| `facts` | Facts produced by this link |
| `collect` | Datetime agent collected the link |
| `finish` | Datetime agent returned results |

## Creating an Operation

```json
POST /api/v2/operations
{
  "name": "Lab Lateral Movement Test",
  "adversary": { "adversary_id": "0f4c3c67-845e-49a0-927e-90ed33c044e0" },
  "group": "red",
  "planner": { "id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a" },
  "source": { "id": "ed32b9c3-9593-4c33-b0db-e2007315096b" },
  "obfuscator": "plain-text",
  "jitter": "2/8",
  "autonomous": 1,
  "auto_close": true,
  "visibility": 51
}
```

## Operation States

```
created → running → (paused) → finished
                 ↘ cleanup → finished
```

- **running**: planner is actively scheduling abilities
- **paused**: operator has paused; no new links sent
- **cleanup**: running cleanup commands from completed abilities
- **finished**: all phases complete or objective met

## Manual vs Autonomous Mode

- `autonomous: 1` — planner runs automatically; abilities execute without operator approval
- `autonomous: 0` — each link requires manual approval via the UI or API before execution

## Controlling a Running Operation

```bash
# Pause
PATCH /api/v2/operations/{id}  {"state": "paused"}

# Resume
PATCH /api/v2/operations/{id}  {"state": "running"}

# Stop
PATCH /api/v2/operations/{id}  {"state": "finished"}

# Add a manual command
POST /api/v2/operations/{id}/potential-links
{
  "paw": "abc123",
  "executor": {"name": "psh", "command": "whoami"}
}
```

## API

```
GET    /api/v2/operations                          # list all
GET    /api/v2/operations/{id}                     # get one
POST   /api/v2/operations                          # create
PATCH  /api/v2/operations/{id}                     # update state
DELETE /api/v2/operations/{id}                     # delete
GET    /api/v2/operations/{id}/links               # get chain
PATCH  /api/v2/operations/{id}/links/{link_id}     # approve/update link
POST   /api/v2/operations/{id}/potential-links     # add manual link
GET    /api/v2/facts/{operation_id}                # get operation facts
```
