# Caldera Attack Simulation Lab

This repo contains the full knowledge bundle and setup scripts for a Caldera 5.3.0 adversary emulation lab. It supports three production-ready operations designed for SecOps Domain Consultants to demonstrate Palo Alto Networks Cortex XDR and XSIAM detection capabilities to customers.

## Quick Setup (New Caldera Instance)

You can configure a fresh Caldera instance automatically. Use this prompt:

> "Download the repo at https://github.com/bsookying/caldera-attack-sim to C:\Users\lab-user\caldera-attack-sim using PowerShell (no git required — download as zip and extract). Then read the CLAUDE.md and set up Caldera at 192.168.8.121:8888 — create all three adversary profiles via the REST API, then create the three fact sources by writing the YAML files from setup/ directly into the Caldera container via SSH using the key at C:\Users\lab-user\.ssh\id_rsa, then restart the Caldera process. Verify everything loaded and report any missing ability IDs."

Claude will:
1. Download and extract the repo locally
2. Authenticate to the Caldera REST API (admin/admin)
3. Create all three adversary profiles via API
4. Verify all ability IDs exist and report any missing
5. Write the three fact source YAML files into the Caldera container via SSH
6. Restart the Caldera process so sources load
7. Verify all three fact sources appear in the API

## Important: How Fact Sources Are Created

Caldera runs inside a **containerd overlay filesystem** on this lab. The REST API (`POST /api/v2/sources`) returns 500 when inline facts are provided — this is a known Caldera 5.3 limitation. Fact sources must be created by writing YAML files directly into the container and restarting the process.

**SSH access:** `lab-user@192.168.8.121` using key at `~/.ssh/id_rsa`

**Container data path:** Find the Caldera PID with `pgrep -f server.py`, then write to:
```
/proc/[PID]/root/usr/src/app/data/sources/
```

**Restart Caldera:** Kill the process — it auto-restarts via the container runtime.

**YAML source files** are in `setup/` — files prefixed `0*_source_*.yml`.

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

| Operation | Adversary ID | Fact Source ID |
|---|---|---|
| Silent Fog | b63f9720-9203-40fc-9d40-1f8fd58167cd | a1b2c3d4-0001-0001-0001-000000000001 |
| Crimson Tide | a17c9651-4ebb-4883-b67b-d5854eb512c1 | a1b2c3d4-0002-0002-0002-000000000002 |
| Dark Pivot | 003e3455-d435-4353-be68-b5989a2fb371 | a1b2c3d4-0003-0003-0003-000000000003 |

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
- `colleague-setup-prompt.md` — copy-paste prompt to configure a fresh Caldera instance
- `caldera-okf/` — full knowledge bundle (concepts, API reference, plugins, adversary profiles, operation designs, video prompts)
- `setup/` — adversary JSON payloads (API) and fact source YAML files (SSH)
