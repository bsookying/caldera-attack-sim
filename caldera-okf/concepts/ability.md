---
type: Caldera Concept
title: Ability
description: An ability is an atomic, ATT&CK-mapped command with one or more platform-specific executors that agents run during operations.
tags:
  - caldera
  - ability
  - att&ck
  - atomic
sources:
  - id: caldera-docs
    resource: https://caldera.readthedocs.io/en/latest/Learning-the-terminology.html
    title: Caldera Terminology
    author: team:mitre
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Ability

An **ability** is Caldera's atomic unit of adversary action. Each ability maps to one or more ATT&CK techniques and contains platform-specific executor blocks (commands). This instance has **2,360 abilities**.

## Schema

| Field | Type | Description |
|---|---|---|
| `ability_id` | UUID | Unique identifier |
| `name` | string | Human-readable name |
| `description` | string | What the ability does |
| `tactic` | string | ATT&CK tactic (e.g., `discovery`, `credential-access`) |
| `technique_id` | string | ATT&CK technique ID (e.g., `T1059.001`) |
| `technique_name` | string | ATT&CK technique name |
| `executors` | array | List of executor objects (one per platform/executor combo) |
| `requirements` | array | Fact requirements that must be satisfied before running |
| `privilege` | string | Required privilege level (e.g., `Elevated`) |
| `repeatable` | bool | Whether the ability can run more than once per operation |
| `singleton` | bool | Whether only one agent should run this ability |
| `buckets` | array | Tactic buckets for the buckets planner |
| `plugin` | string | Plugin that provides this ability |

## Executor Schema

Each entry in `executors` specifies how to run the command on a specific platform:

| Field | Description |
|---|---|
| `platform` | `windows`, `linux`, or `darwin` |
| `name` | Executor: `psh` (PowerShell), `sh`, `cmd`, `python`, `manual` |
| `command` | The command string; may contain `#{variable}` placeholders |
| `cleanup` | Optional cleanup command run after the ability completes |
| `parsers` | List of output parsers that extract facts from command output |
| `payloads` | Payloads this executor requires (downloaded to the agent) |
| `timeout` | Max seconds to wait for command completion |

## Tactic Distribution (this instance)

| Tactic | Count |
|---|---|
| discovery | 481 |
| multiple | 407 |
| stealth | 234 |
| credential-access | 218 |
| defense-impairment | 204 |
| execution | 153 |
| command-and-control | 133 |
| collection | 93 |
| impact | 84 |
| persistence | 67 |
| lateral-movement | 65 |
| privilege-escalation | 63 |
| exfiltration | 52 |
| defense-evasion | 49 |
| initial-access | 9 |
| reconnaissance | 3 |

## Platform Distribution

| Platform | Count |
|---|---|
| windows | 1,716 |
| linux | 634 |
| darwin | 307 |

## Variable Substitution

Commands use `#{variable.name}` syntax. Variables are resolved from [facts](fact.md) at runtime. If a required variable is unresolvable, the ability is skipped.

```bash
# Example: ability command using facts
net use \\#{remote.host.ip}\C$ /user:#{domain.user.name} #{domain.user.password}
```

## API

```
GET    /api/v2/abilities              # list all abilities
GET    /api/v2/abilities/{id}         # get one ability
POST   /api/v2/abilities              # create ability
PATCH  /api/v2/abilities/{id}         # update ability
DELETE /api/v2/abilities/{id}         # delete ability
```

See [api/abilities](../api/abilities.md) for full request/response details.
