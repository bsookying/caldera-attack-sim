---
type: Caldera Concept
title: Adversary
description: An adversary is a named, ordered collection of abilities that represents a threat actor's TTP profile used to drive operations.
tags:
  - caldera
  - adversary
  - threat-actor
  - att&ck
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Adversary

An **adversary** is a named profile that groups [abilities](ability.md) into an ordered sequence representing how a real-world threat actor operates. This instance has **39 adversary profiles**.

## Schema

| Field | Type | Description |
|---|---|---|
| `adversary_id` | UUID | Unique identifier |
| `name` | string | Threat actor or scenario name |
| `description` | string | Profile summary |
| `atomic_ordering` | array[UUID] | Ordered list of ability IDs |
| `objective` | UUID | Optional [objective](objective.md) ID for goal-based termination |
| `tags` | array | Categorization tags |
| `plugin` | string | Plugin that provides this profile |

## Available Adversary Profiles

| Name | Abilities | Source Plugin |
|---|---|---|
| APT29 | 79 | emu |
| Carbanak | 24 | emu |
| FIN6 | 27 | emu |
| FIN7 | 10 | emu |
| OilRig | 44 | emu |
| Sandworm Team (G0034) | 40 | emu |
| Turla - Carbon | 45 | emu |
| Turla - Snake | 53 | emu |
| Wizard Spider | 38 | emu |
| menuPass | 15 | emu |
| Advanced Thief | 3 | stockpile |
| Alice 2.0 | 9 | stockpile |
| Check | 8 | stockpile |
| Collection | 4 | stockpile |
| Defense Evasion | 36 | stockpile |
| Discovery | 12 | stockpile |
| Enumerator | 5 | stockpile |
| Everything Bagel | 2,360 | stockpile |
| Lateral Movement - Certutil | 3 | stockpile |
| Lateral Movement - Esentutl | 3 | stockpile |
| Nosy Neighbor | 7 | stockpile |
| Printer Queue | 2 | stockpile |
| Ransack | 18 | stockpile |
| Service Creation Lateral Movement | 3 | stockpile |
| Signed Binary Proxy Execution | 3 | stockpile |
| Stowaway | 2 | stockpile |
| Super Spy | 15 | stockpile |
| Terminal | 1 | stockpile |
| Thief | 5 | stockpile |
| Undercover | 2 | stockpile |
| Worm | 13 | stockpile |
| Windows Worm #1–3 | 4–5 each | stockpile |
| You Shall (Not) Bypass | 4 | stockpile |

See [adversaries/](../adversaries/index.md) for detailed profiles on named threat actors.

## Creating a Custom Adversary

```json
POST /api/v2/adversaries
{
  "name": "My Custom Adversary",
  "description": "Targeted lateral movement scenario",
  "atomic_ordering": [
    "ba0b398d-91b8-490a-bed2-f959afa8e1aa",
    "8cd933afe764c4159000cadea55f8ca5"
  ]
}
```

## API

```
GET    /api/v2/adversaries            # list all
GET    /api/v2/adversaries/{id}       # get one
POST   /api/v2/adversaries            # create
PATCH  /api/v2/adversaries/{id}       # update
DELETE /api/v2/adversaries/{id}       # delete
```
