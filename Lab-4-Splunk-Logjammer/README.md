# Lab 4 – Splunk Setup & Windows Event Log Forensics (Logjammer)

Computer Forensics, SWPS University – Lab 4 (4a + 4b)

This folder contains my own lab reports for a two-part exercise: building a Splunk
environment on Linux Mint (Lab 4a) and using it to reconstruct a multi-stage attack on a
Windows machine from its event logs (Lab 4b).

> The instructor's lab handout and the original log files (`.evtx`) are **not** included
> in this repository. Only my own reports and write-up are published here.

## Scenario

Using the `CyberJunkie` account, an attacker prepared command-and-control (C2)
communication by changing the firewall, set up persistence with a scheduled task, tried
network reconnaissance with SharpHound (blocked by Windows Defender) and finally cleared
the firewall event log to hide the traces (a Kill Chain–style attack).

## Tools

- Linux Mint (VirtualBox), Firefox
- Splunk Enterprise 10.6.1.1 (`.deb` package)
- Python 3 + `python3-evtx` (EVTX → JSON Lines conversion)
- Windows event logs: Security, System, PowerShell-Operational,
  Windows Defender-Operational, Windows Firewall

## Lab 4a – Environment setup (summary)

| Task | What I did |
|------|-----------|
| 1–2 | Downloaded and installed Splunk Enterprise with `apt install` |
| 3 | Started Splunk as the `splunk` user, created the admin account, created `/opt/splunk/lab-4` |
| 4 | Logged in to Splunk Web at `http://localhost:8000` |
| – | Extracted the Logjammer archive and copied the five `.evtx` files to `/opt/splunk/lab-4/Event-Logs` |
| 5 | Installed the EVTX parser (`python3-evtx`) |
| 6 | Converted the `.evtx` files to JSON Lines with a Python script (578 + 115 + 2186 + 444 + 929 = **4,252 events**) |
| 7 | Uploaded the `.jsonl` files with the `_json` source type into the new index `htb-logjammer` |
| 8 | Verified the import: 4,252 events in Splunk, matching the conversion totals |

Issue I ran into: the first run of the conversion script failed with a `SyntaxError`
because the `mkdir`/`cat` lines had been pasted into the Python file. Removing them with
`sed -i '1,2d'` fixed it.

## Lab 4b – Forensic analysis (answers)

The data was imported as raw event XML, so most fields had to be extracted with `rex`.
Splunk's `_time` was shifted by 2 hours, so I used the XML `SystemTime` value as the
reliable UTC time.

| # | Question | Event ID(s) | Answer |
|---|----------|-------------|--------|
| 1 | When did CyberJunkie first log on successfully (UTC)? | 4624 | 2023-03-27 14:37:09 UTC |
| 2 | Name of the firewall rule the attacker added | 2004 (Firewall log) | Metasploit C2 Bypass |
| 3 | Direction of the firewall rule | 2004 | Outbound |
| 4 | Subcategory of the changed audit policy | 4719 | Other Object Access Events (category: Object Access) |
| 5 | Name of the scheduled task created by CyberJunkie | 4698 | \HTB-AUTOMATION |
| 6 | Full path of the file the task runs | 4698 | C:\Users\CyberJunkie\Desktop\Automation-HTB.ps1 |
| 7 | Command-line arguments of the task | 4698 | -A cyberjunkie@hackthebox.eu |
| 8 | Tool the antivirus flagged as malicious | 1116 | SharpHound (HackTool:PowerShell/SharpHound.B) |
| 9 | Full path of the detected file | 1116 | C:\Users\CyberJunkie\Downloads\SharpHound-v1.1.0.zip |
| 10 | Action taken by the antivirus | 1117 | Quarantine |
| 11 | Command executed in PowerShell | 4104 | Get-FileHash -Algorithm md5 .\Desktop\Automation-HTB.ps1 |
| 12 | Which event log file was cleared? | 104 (System) | Microsoft-Windows-Windows Firewall With Advanced Security/Firewall |

## Attack timeline (all 27 March 2023, UTC)

| Time | Event |
|------|-------|
| 14:37:09 | First successful logon of CyberJunkie (4624) |
| 14:42:34 | Defender detects SharpHound (1116) |
| 14:42:48 | Defender quarantines it (1117) |
| 14:44:43 | Outbound firewall rule "Metasploit C2 Bypass" added (2004) |
| 14:50:03 | Audit policy changed – Other Object Access Events enabled (4719) |
| 14:51:21 | Scheduled task `\HTB-AUTOMATION` created (4698) |
| 14:58:33 | `Get-FileHash` run on `Automation-HTB.ps1` (4104) |
| 15:01:56 | Firewall log cleared (104) |

## What I learned

- How to install and run Splunk Enterprise on Linux and index non-native data (EVTX → JSON).
- Which Windows Event IDs matter for investigations (4624, 4698, 4719, 4946/2004, 1116/1117, 4104, 1102/104).
- How to correlate events from several log sources into one attack timeline.
- Why time zones matter in forensics (Splunk `_time` vs. UTC `SystemTime`).

## Files

- `Lab-4a_Splunk_setup.pdf` – my report for the environment setup
- `Lab-4b_forensic_analysis.pdf` – my report for the forensic questions (with screenshots)
