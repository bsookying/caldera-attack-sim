# Caldera Attack Simulation Lab

Adversary emulation playbook for three production-ready attack scenarios demonstrating Palo Alto Networks Cortex XDR and XSIAM detection capabilities.

## For Your Colleague — Getting Started

Copy and paste this prompt into Claude Code on your machine:

```
Download the repo at https://github.com/bsookying/caldera-attack-sim to
C:\Users\lab-user\caldera-attack-sim using PowerShell (no git required —
download as zip and extract). Then read the CLAUDE.md in the repo root and
set up the Caldera instance at 192.168.8.121:8888 using admin/admin by
creating all three adversary profiles and fact sources from the setup/ folder.
Verify each ability ID exists in the instance before creating adversaries and
report anything missing.
```

That's it. Claude will handle the rest.

## What Gets Configured

| Item | Details |
|---|---|
| Adversaries | Silent Fog (APT29), Crimson Tide (Wizard Spider), Dark Pivot (APT10) |
| Fact Sources | Lab target IPs and credentials seeded for all three operations |
| Verification | All ability IDs checked against the live Caldera instance |

## Operations

| Operation | Threat | Story |
|---|---|---|
| Silent Fog | APT29 — nation-state espionage | Low-and-slow spearphish → domain compromise → NTDS exfil |
| Crimson Tide | Wizard Spider — ransomware | Stolen creds → 43-minute domain-wide encryption |
| Dark Pivot | APT10 — supply chain | Build package → developer station → DNS exfil |

## Repo Structure

```
CLAUDE.md               Auto-loaded by Claude Code — full lab context
README.md               This file
setup/                  API payloads — run these against a fresh Caldera instance
caldera-okf/            Full knowledge bundle
  operations/           Attack designs with kill chains and Caldera configs
  video/                NotebookLM prompts for 3-minute customer-facing videos
  api/                  Caldera REST API reference
  concepts/             Core Caldera building blocks
  lab/                  Host inventory and network topology
  adversaries/          Threat actor profiles
  plugins/              Enabled plugin documentation
```
