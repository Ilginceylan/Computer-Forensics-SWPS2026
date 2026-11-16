# Computer Forensics & Network Security - Lab Reports

This repository contains detailed laboratory investigation reports, packet analyses (Wireshark), and security incident response tasks completed as part of the **Computer Forensics & Operating Systems** coursework.

## Repository Contents

### 1. Bumblebee - Investigation Report (Stored XSS Attack)
* **File:** [`Ilgin Ceylan - Bumblebee – Investigation Report (Stored XSS Attack).pdf`](./Ilgin%20Ceylan%20-%20Bumblebee%20–%20Investigation%20Report%20(Stored%20XSS%20Attack).pdf)
* **Overview:** Investigation of a Stored XSS attack scenario on a phpBB forum. This report covers identifying attacker accounts via SQLite database queries, tracing malicious payload insertion (`post_id` 9), analyzing server access logs, uncovering leaked plaintext LDAP credentials (`phpbb_config`), OSINT techniques via Google Dorks (`site:github.com`), and identifying ransomware indicators of compromise (IoCs like `GonnaCry`).

### 2. Computer Forensics - Lab 1 (Network Scanning & Port Knocking)
* **File:** [`Ilgin Ceylan - 82862 - Computer Forensics - Lab1.pdf`](./Ilgin%20Ceylan%20-%2082862%20-%20Computer%20Forensics%20-Lab1.pdf)
* **Overview:** Comprehensive Wireshark packet analysis focusing on network reconnaissance and stealth mechanisms. Tasks include:
  * Identifying SYN flood/port scanning sources (`tcp.flags.syn == 1 && tcp.flags.ack == 0`).
  * Detecting brute-force password spraying attacks on FTP (`T1110.003`).
  * Manual protocol decoding (`Decode As`) for non-standard ports (`port 24456`).
  * Analyzing **Port Knocking** configurations and iptables defense mechanisms.

### 3. Computer Forensics - Lab 4 (Splunk Setup & Windows Event Log Forensics)
- **File (Part A):** [Ilgin Ceylan - 82862 - Computer Forensics - Lab4a Splunk.pdf](Ilgin%20Ceylan%20-%2082862%20-%20Computer%20Forensics%20-%20Lab4a%20Splunk.pdf)
- **File (Part B):** [Ilgin Ceylan - 82862 - Computer Forensics - Lab4b.pdf](Ilgin%20Ceylan%20-%2082862%20-%20Computer%20Forensics%20-%20Lab4b.pdf)
- **Overview:** Splunk-based forensic investigation of a multi-stage attack on a Windows machine (Logjammer scenario). Part A covers building the analysis environment; Part B reconstructs the attacker's kill chain from Windows event logs. Tasks include:
  - Installing Splunk Enterprise on Linux Mint, converting binary `.evtx` logs to JSON Lines with Python (`python3-evtx`) and indexing 4,252 events (`htb-logjammer`).
  - Extracting fields from raw event XML with SPL (`rex`) and correlating UTC timestamps (`SystemTime`) across five log sources.
  - Identifying the attacker's first logon (`4624`), a malicious outbound firewall rule "Metasploit C2 Bypass" (`2004`) and an audit policy change (`4719`).
  - Detecting persistence via a scheduled task (`4698`, `\HTB-AUTOMATION`) and a PowerShell command in script block logs (`4104`).
  - Analysing Defender detections of SharpHound reconnaissance (`1116`, `1117`, quarantined) and log clearing used to hide traces (`104`, `1102`).

## Tools & Technologies Used
* **Wireshark** (Packet Analysis, TCP Stream Following, Protocol Decoding)
* **SQLite / Command Line** (Database Forensics, Log Analysis)
* **Bash / Linux Terminal** (`grep`, `strings`, log parsing)
* **OSINT & Threat Intelligence** (Google Dorking, IoC Analysis)

---
*Prepared by Ilgin Ceylan | Computer Science Student*
