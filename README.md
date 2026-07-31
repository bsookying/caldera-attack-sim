# Caldera Attack Simulation Lab

Adversary emulation playbook for three production-ready attack scenarios demonstrating Palo Alto Networks Cortex XDR and XSIAM detection capabilities.

## For Your Colleague — Getting Started

Copy and paste this prompt into Claude Code on your machine:

```
Download the repo at https://github.com/bsookying/caldera-attack-sim to
C:\Users\lab-user\caldera-attack-sim using PowerShell (no git required —
download as zip and extract). Then read the CLAUDE.md in the repo root and
set up both Caldera instances (192.168.8.121:8888 and 192.168.3.21:8888)
using admin/admin by doing the following in order:

1. On caldera-1 (192.168.8.121): create all three adversary profiles via
   POST /api/v2/adversaries using the JSON files in setup/ prefixed 04_, 05_, 06_.
   Verify every ability ID exists and report anything missing.
2. On caldera-1: create the three fact sources by SSHing to 192.168.8.121
   as lab-user using the key at C:\Users\lab-user\.ssh\id_rsa, finding the
   Caldera PID (pgrep -f server.py), and writing each YAML file from setup/
   (prefixed 01_, 02_, 03_ ending in .yml) to:
   /proc/[PID]/root/usr/src/app/data/sources/
3. Enable the human and gameboard plugins on caldera-1 by sending:
     PATCH http://192.168.8.121:8888/api/v2/config/main
     {"prop": "plugin", "value": "human"}   (then repeat for "gameboard")
   Then restart the caldera-1 container:
     ssh lab-user@192.168.8.121 sudo docker restart $(sudo docker ps --format '{{.Names}}' | head -1)
4. Enable the human and gameboard plugins on caldera-2 the same way,
   targeting http://192.168.3.21:8888, then restart its container via SSH.
5. Wait 20 seconds after each restart, then verify fact sources and plugins
   loaded correctly on both instances.
6. Report a full summary: adversaries created, abilities verified, fact
   sources loaded, plugins enabled on both instances, anything failed.
```

That's it. Claude will handle the rest.

## What Gets Configured

| Item | Details |
|---|---|
| Adversaries | Silent Fog (APT29), Crimson Tide (Wizard Spider), Dark Pivot (APT10) |
| Fact Sources | Lab target IPs and credentials seeded for all three operations |
| Plugins | human + gameboard enabled on caldera-1 and caldera-2 |
| Verification | Ability IDs, fact sources, and plugin status all checked |

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
