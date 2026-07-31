# Colleague Setup Prompt

Paste this into Claude Code on your machine to download the repo and fully configure your Caldera instance automatically.

```
Download the repo at https://github.com/bsookying/caldera-attack-sim to
C:\Users\lab-user\caldera-attack-sim using PowerShell (no git required —
download as zip and extract). Then read the CLAUDE.md in the repo root and
set up Caldera at 192.168.8.121:8888 by doing the following in order:

1. Authenticate to the Caldera REST API using admin/admin.
2. Create all three adversary profiles via POST /api/v2/adversaries using
   the JSON files in setup/ prefixed 04_, 05_, 06_.
3. Verify every ability ID in each adversary exists in the instance.
   Report any that are missing but continue setup.
4. Create the three fact sources by SSHing to 192.168.8.121 as lab-user
   using the key at C:\Users\lab-user\.ssh\id_rsa. Find the Caldera
   process PID (pgrep -f server.py), then write each YAML file from
   setup/ (prefixed 01_, 02_, 03_ and ending in .yml) into:
   /proc/[PID]/root/usr/src/app/data/sources/
5. Restart Caldera by killing the process (it auto-restarts).
6. Wait 15 seconds, then verify all three fact sources appear in
   GET /api/v2/sources and confirm fact counts match.
7. Report a full summary: adversaries created, abilities verified,
   fact sources loaded, anything missing or failed.
```

## What gets configured

| Item | Details |
|---|---|
| Adversary — Silent Fog | APT29 trimmed profile, 22 abilities |
| Adversary — Crimson Tide | Wizard Spider ransomware, 23 abilities |
| Adversary — Dark Pivot | APT10 supply chain, 28 abilities |
| Fact Source — Silent Fog | Targets: windows-the-boss, windows-dc |
| Fact Source — Crimson Tide | Targets: windows-client-11, windows-dc |
| Fact Source — Dark Pivot | Targets: windows-client-11, windows-the-boss, windows-dc |

## Why SSH for fact sources?

Caldera 5.3 runs inside a containerd overlay filesystem on this lab.
The REST API returns 500 when creating sources with inline facts — a known
limitation. Writing YAML files directly into the container is the reliable
workaround. Claude handles this automatically.
