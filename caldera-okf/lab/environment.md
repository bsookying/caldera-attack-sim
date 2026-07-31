---
type: Lab Environment
title: GCP Sandbox Environment
description: Full host inventory and network topology for the GCP adversary emulation lab, including IPs, credentials, and network segments.
tags:
  - lab
  - environment
  - network
  - gcp
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
stale_after: 2026-10-30
---

# GCP Sandbox Environment

This lab is hosted on GCP and segmented into four network zones. Two Caldera instances are deployed — C2 assignment is chosen per operation to ensure beacon traffic traverses the firewall:

| Caldera | IP | Segment | Used by |
|---|---|---|---|
| caldera-1 | 192.168.8.121 | Server | Silent Fog, Crimson Tide (agents in Workstation segment) |
| caldera-2 | 192.168.3.21 | Workstation | Dark Pivot (agent in Server segment) |

## Network Segments

### Jump / Management (192.168.200.x)

Entry points for lab access. Not emulation targets.

| Host | IP | User | Auth |
|---|---|---|---|
| windows-jumpbox | 192.168.200.x (DHCP) | lab-user | — |
| linux-jumpbox | 192.168.200.100 | lab-user | [BLANK password] |
| vmseries-firewall-1 | 192.168.200.200 | admin | User-configured |

### Trusted Segment (192.168.1.x)

High-value targets: domain controller and infrastructure.

| Host | IP | User | Auth |
|---|---|---|---|
| broker-vm | 192.168.1.21 | — | `!nitialPassw0rd` |
| linux-engine | 192.168.1.22 | lab-user | Public key |
| windows-dc | 192.168.1.31 | administrator | `Paloalto1!` |

### Server Segment (192.168.8.x)

Caldera C2 and supporting infrastructure.

| Host | IP | User | Auth |
|---|---|---|---|
| windows-dev-station | 192.168.8.31 | lab-user | `Paloalto1!` |
| **caldera-1** | **192.168.8.121** | lab-user | Public key |
| threat-emulator | 192.168.8.122 | lab-user | Public key |
| torque-k8s | 192.168.8.x (DHCP) | — | — |

### Workstation Segment (192.168.3.x)

Primary emulation targets — Windows workstations and caldera-2.

| Host | IP | User | Auth |
|---|---|---|---|
| caldera-2 | 192.168.3.21 | lab-user | Public key |
| **windows-the-boss** | **192.168.3.31** | lab-user | `Paloalto1!` |
| **windows-client-11** | **192.168.3.32** | lab-user | `Paloalto1!` |

## Recommended Target Priority

| Priority | Host | Rationale |
|---|---|---|
| 1 | windows-client-11 (192.168.3.32) | Standard Windows workstation — ideal for most ability testing |
| 2 | windows-the-boss (192.168.3.31) | Windows workstation with elevated user — privilege escalation target |
| 3 | windows-dc (192.168.1.31) | Domain controller — lateral movement and domain compromise endpoint |
| 4 | windows-dev-station (192.168.8.31) | Same segment as Caldera — convenient for agent deployment testing |

## ATT&CK Kill Chain in This Lab

```
Caldera C2 (192.168.8.121)
    │
    ▼ deploy agent
windows-client-11 (192.168.3.32)   ← initial access / execution
    │
    ▼ lateral movement
windows-the-boss (192.168.3.31)     ← privilege escalation
    │
    ▼ lateral movement
windows-dc (192.168.1.31)           ← domain compromise
```
