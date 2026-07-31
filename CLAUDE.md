# Caldera Attack Simulation Lab

This repo contains the full knowledge bundle and setup scripts for a Caldera 5.3.0 adversary emulation lab. It supports three production-ready operations designed for SecOps Domain Consultants to demonstrate Palo Alto Networks Cortex XDR and XSIAM detection capabilities to customers.

## Quick Setup (New Caldera Instance)

You can configure a fresh Caldera instance automatically. Use this prompt:

> "Set up this Caldera lab using the setup/ folder in this repo. Caldera is at [YOUR_IP]:8888, credentials are admin/admin."

Claude will:
1. Authenticate to the Caldera API
2. Create all three adversary profiles with the correct ability ordering
3. Create all three fact sources seeded with lab target IPs and credentials
4. Verify all ability IDs exist in the instance and report any missing

## Lab Topology

| Host | IP | Segment | Role |
|---|---|---|---|
| vmseries-firewall-1 | 192.168.200.200 | Management | Palo Alto VM-Series (inspect/alert) |
| linux-jumpbox | 192.168.200.100 | Management | Jump host |
| broker-vm | 192.168.1.21 | Trusted | Broker |
| linux-engine | 192.168.1.22 | Trusted | Linux engine |
| windows-dc | 192.168.1.31 | Trusted | Domain Controller (administrator / Paloalto1!) |
| windows-dev-station | 192.168.8.31 | Server | Developer workstation (lab-user / Paloalto1!) |
| caldera-1 | 192.168.8.121 | Server | C2 — Silent Fog + Crimson Tide |
| caldera-2 | 192.168.3.21 | Workstation | C2 — Dark Pivot |
| windows-the-boss | 192.168.3.31 | Workstation | Target — privileged user (lab-user / Paloalto1!) |
| windows-client-11 | 192.168.3.32 | Workstation | Target — standard user (lab-user / Paloalto1!) |

C2 assignment is deliberate: agents must beacon across segment boundaries so attack traffic traverses the VM-Series firewall for inspection.

## Operations

| Operation | Threat Model | Entry Point | C2 | Story |
|---|---|---|---|---|
| Silent Fog | APT29 — nation-state espionage | Spearphish → windows-client-11 | caldera-1 | Low-and-slow, full cleanup, NTDS exfil |
| Crimson Tide | Wizard Spider — ransomware | Stolen creds RDP → windows-the-boss | caldera-1 | Fast, loud, domain-wide encryption |
| Dark Pivot | APT10 — supply chain | Build package → windows-dev-station | caldera-2 | Patient, DNS exfiltration |

## Caldera Access

| Instance | URL | Credentials |
|---|---|---|
| caldera-1 | http://192.168.8.121:8888 | admin / admin |
| caldera-2 | http://192.168.3.21:8888 | admin / admin |

## Adversary IDs (caldera-1)

| Operation | Adversary ID |
|---|---|
| Silent Fog | b63f9720-9203-40fc-9d40-1f8fd58167cd |
| Crimson Tide | a17c9651-4ebb-4883-b67b-d5854eb512c1 |
| Dark Pivot | 003e3455-d435-4353-be68-b5989a2fb371 |

## Pre-Operation Checklist

Before deploying any agent, disable host defenses on all target Windows hosts:

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
Set-MpPreference -DisableScriptScanning $true
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

## Repo Structure

- `CLAUDE.md` — this file; loaded automatically by Claude Code
- `caldera-okf/` — full knowledge bundle (concepts, API reference, plugins, adversary profiles, operation designs, video prompts)
- `setup/` — API payloads to configure a fresh Caldera instance
