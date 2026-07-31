---
type: Platform Overview
title: MITRE Caldera Overview
description: Caldera is an open-source, automated adversary emulation platform built on the MITRE ATT&CK framework.
tags:
  - caldera
  - mitre
  - att&ck
  - overview
resource: http://192.168.8.121:8888
sources:
  - id: caldera-github
    resource: https://github.com/mitre/caldera
    title: MITRE Caldera GitHub
    author: team:mitre
  - id: caldera-docs
    resource: https://caldera.readthedocs.io
    title: Caldera Documentation
    author: team:mitre
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# MITRE Caldera Overview

Caldera is an open-source adversary emulation platform developed by MITRE. It enables security teams to run automated red-team operations, test defensive controls, and practice incident response — all mapped to the [MITRE ATT&CK](https://attack.mitre.org) framework.[^caldera-github]

This instance runs **Caldera 5.3.0** at `http://192.168.8.121:8888`.

[^caldera-github]: MITRE Caldera GitHub

## Core Workflow

```
Abilities → Adversary → Operation → Agent (Implant) → Results
```

1. **Abilities** are atomic ATT&CK-mapped commands (2,360 in this instance).
2. **Adversaries** are ordered collections of abilities representing a threat actor's TTPs.
3. An **Operation** runs an adversary profile against a group of **Agents**.
4. **Agents** (implants) beacon back to Caldera, execute commands, and return output.
5. **Facts** discovered during execution feed into later ability steps.

## Architecture

| Layer | Component | Purpose |
|---|---|---|
| C2 Server | `caldera-1` (192.168.8.121:8888) | Core platform — REST API, UI, operation scheduling |
| UI | Magma (VueJS) | Browser-based interface served from `/` |
| Implant | Sandcat (Go) | Default cross-platform agent; beacons over HTTP/S |
| Implant | Manx | Shell-access agent for interactive sessions |
| Planning | Planner | Decides which abilities to run each phase |

## Access Levels

Caldera has three built-in user groups:

| Group | Access | Default Creds |
|---|---|---|
| `RED` | Full red-team operator: create/run operations, deploy agents | `admin / admin` |
| `BLUE` | Defensive view: see alerts, monitor operations | `blue / admin` |
| `app.api` | API-only key (headless use) | configured in `local.yml` |

This instance is authenticated as `admin` with `RED` access.

## Plugin Architecture

Caldera is extended through plugins. Each plugin adds abilities, planners, or UI features. Active plugins in this instance:

| Plugin | Purpose |
|---|---|
| `sandcat` | Default Go-based RAT/implant |
| `stockpile` | Core ability and adversary library |
| `atomic` | Red Canary Atomic Red Team ability library |
| `emu` | CTID adversary emulation profiles (APT29, OilRig, etc.) |
| `manx` | Shell-access terminal agent |
| `compass` | ATT&CK Navigator-style visualization |
| `debrief` | Post-operation reporting and timelines |
| `response` | Automated incident response plugin |
| `access` | Initial access modules |
| `training` | Caldera SME certification course |
| `fieldmanual` | In-app documentation |

## C2 Contact Channels

Agents communicate back to Caldera via configurable contact protocols:

| Protocol | Address | Notes |
|---|---|---|
| HTTP | `http://192.168.8.121:8888` | Default; most implants |
| TCP | `0.0.0.0:7010` | Raw TCP socket |
| UDP | `0.0.0.0:7011` | UDP beacon |
| WebSocket | `0.0.0.0:7012` | |
| DNS | `mycaldera.caldera:8853` | DNS tunneling |
| SSH Tunnel | `0.0.0.0:8022` | user: `sandcat`, pass: `s4ndc4t!` |
| FTP | `0.0.0.0:2222` | user: `caldera_user`, pass: `caldera` |

See [contacts](lab/contacts.md) for deployment guidance.
