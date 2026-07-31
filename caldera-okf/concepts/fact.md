---
type: Caldera Concept
title: Fact & Fact Source
description: Facts are key-value pairs discovered or seeded during operations; fact sources are named collections of seed facts that initialize operations.
tags:
  - caldera
  - fact
  - fact-source
  - variable
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Fact & Fact Source

**Facts** are structured key-value pairs that flow through a Caldera [operation](operation.md). They serve two roles:
1. **Seed data** — pre-loaded values (credentials, IPs, paths) that abilities consume via `#{variable}` placeholders.
2. **Discovered data** — values extracted from ability output by parsers, used by later abilities.

A **Fact Source** is a named container of seed facts attached to an operation. This instance has **13 fact sources**.

## Fact Schema

| Field | Type | Description |
|---|---|---|
| `trait` | string | Dot-notation key (e.g., `domain.user.name`) |
| `value` | string | The fact's value |
| `score` | int | Confidence/priority score (higher = preferred) |
| `collected_by` | UUID | Agent PAW that produced this fact (or blank for seeded) |
| `created` | datetime | When this fact was recorded |
| `links_collected` | array | Link IDs that produced this fact |
| `relationships` | array | Related fact edges |

## Fact Source Schema

| Field | Type | Description |
|---|---|---|
| `id` | UUID | Unique ID |
| `name` | string | Human-readable name |
| `facts` | array | List of seed fact objects |
| `rules` | array | Allow/deny rules for fact collection |
| `adjustments` | array | Score adjustments by trait |
| `plugin` | string | Plugin that provides this source |

## Available Fact Sources

| Name | Facts | Purpose |
|---|---|---|
| basic | 4 | Minimal seed for simple operations |
| Exfil Operation | 15 | Exfiltration scenario seeds |
| APT29 (Emu) | 18 | APT29 emulation profile seeds |
| OilRig (Emu) | 13 | OilRig emulation profile seeds |
| Sandworm Team (Emu) | 9 | Sandworm emulation seeds |
| Carbanak (Emu) | 11 | Carbanak emulation seeds |
| FIN6 (Emu) | 30 | FIN6 emulation seeds |
| FIN7 (Emu) | 4 | FIN7 emulation seeds |
| menuPass (Emu) | 45 | menuPass emulation seeds |
| Turla - Carbon (Emu) | 21 | Turla Carbon seeds |
| Turla - Snake (Emu) | 21 | Turla Snake seeds |
| Wizard Spider (Emu) | 11 | Wizard Spider seeds |
| Alice Filters | 0 | Filter-only source |

## Common Fact Traits

| Trait | Example Value | Used By |
|---|---|---|
| `remote.host.ip` | `192.168.3.32` | Lateral movement abilities |
| `remote.host.fqdn` | `windows-client-11.lab` | Network discovery |
| `domain.user.name` | `lab-user` | Credential-use abilities |
| `domain.user.password` | `Paloalto1!` | Credential-use abilities |
| `file.sensitive.extension` | `.docx` | Collection abilities |
| `host.user.name` | `administrator` | Local credential abilities |
| `host.user.password` | `Paloalto1!` | Local credential abilities |
| `domain.user.ntlm` | `<hash>` | Pass-the-hash abilities |

## Parsers

Parsers extract facts from ability output. They are defined per-executor and use regex or JSON path patterns:

```yaml
parsers:
  - module: caldera.app.parsers.re
    parserconfigs:
      - source: remote.host.ip
        edge: has_ip
        target: remote.host.ip
        custom_parser_vals:
          regex: \d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
```

## Seeding Facts for the Lab

To seed target facts before running an operation against lab workstations:

```json
POST /api/v2/sources
{
  "name": "Lab Workstation Targets",
  "facts": [
    {"trait": "remote.host.ip", "value": "192.168.3.32", "score": 1},
    {"trait": "remote.host.ip", "value": "192.168.3.31", "score": 1},
    {"trait": "domain.user.name", "value": "lab-user", "score": 1},
    {"trait": "domain.user.password", "value": "Paloalto1!", "score": 1}
  ]
}
```

## API

```
GET    /api/v2/sources              # list fact sources
GET    /api/v2/sources/{id}         # get one
POST   /api/v2/sources              # create
PATCH  /api/v2/sources/{id}         # update
DELETE /api/v2/sources/{id}         # delete
GET    /api/v2/facts/{operation_id} # get facts from an operation
POST   /api/v2/facts                # add facts to an operation
DELETE /api/v2/facts                # remove facts
```
