# Colleague Setup Prompt

Paste this into Claude Code on your machine to download the repo and fully configure both Caldera instances automatically.

```
Download the repo at https://github.com/bsookying/caldera-attack-sim to
C:\Users\lab-user\caldera-attack-sim using PowerShell (no git required —
download as zip and extract). Then read the CLAUDE.md in the repo root and
set up both Caldera instances as follows:

--- CALDERA-1 (192.168.8.121:8888) ---

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
5. Enable the human and gameboard plugins on caldera-1 by sending two
   PATCH requests to http://192.168.8.121:8888/api/v2/config/main
   (authenticated with admin/admin cookie), one for each plugin:
     {"prop": "plugin", "value": "human"}
     {"prop": "plugin", "value": "gameboard"}
6. Restart the caldera-1 Docker container via SSH:
     sudo docker restart $(sudo docker ps --format "{{.Names}}" | head -1)
7. Wait 20 seconds, then verify:
   - All three fact sources appear in GET /api/v2/sources
   - human and gameboard show enabled: true in GET /api/v2/plugins

--- CALDERA-2 (192.168.3.21:8888) ---

8. Authenticate to caldera-2 REST API using admin/admin.
9. Enable the human and gameboard plugins on caldera-2 using the same
   PATCH /api/v2/config/main approach as step 5 above, targeting
   http://192.168.3.21:8888.
10. Restart the caldera-2 Docker container via SSH to 192.168.3.21:
      sudo docker restart $(sudo docker ps --format "{{.Names}}" | head -1)
11. Wait 20 seconds, then verify human and gameboard show enabled: true
    in GET /api/v2/plugins on caldera-2.

--- FINAL REPORT ---

12. Report a full summary: adversaries created, abilities verified,
    fact sources loaded, plugins enabled on both instances, anything
    missing or failed.
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
| Plugin — human | Enabled on caldera-1 and caldera-2 |
| Plugin — gameboard | Enabled on caldera-1 and caldera-2 |

## Why SSH for fact sources?

Caldera 5.3 runs inside a Docker container on this lab. The REST API
returns 500 when creating sources with inline facts — a known limitation.
Writing YAML files directly into the container via /proc/[PID]/root/ is
the reliable workaround. Claude handles this automatically.

## Why PATCH config then restart for plugins?

Caldera's config persistence works as follows: on shutdown (SIGTERM),
Caldera writes its in-memory config back to conf/local.yml. So the correct
sequence is to update the live in-memory config via the API, then restart —
the shutdown writes the updated config (with the plugin) to local.yml, and
the next startup reads it and loads the plugin. Editing local.yml directly
while Caldera is running and then restarting will lose the change.
