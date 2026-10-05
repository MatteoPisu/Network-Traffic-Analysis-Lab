# Network-Traffic-Analysis-Lab

## Project Overview
In cybersecurity, when a computer behaves strangely or an alert goes off, security analysts need to figure out *what* happened, *who* was affected, and *where* the threat came from. 

For this project, I acted as a digital detective investigating a simulated security incident called **Natureforce**. Using a professional network analysis tool called **Wireshark**, I looked through a sample **PCAP file** (a recording of network traffic) to track down an infected machine, investigate security alerts, and verify threat indicators.

---

## SOC Analyst Briefing & Scenario Background
Working as an analyst at a security operations center (SOC), the investigation kicked off with two specific network alerts:
* **Alert 1:** `107.175.82.242:9000` — Windows executable (EXE) file sent from an IP address over an unusual TCP port.
* **Alert 2:** `195.64.128.106:443` — CNCmachineRMS RAT C2 traffic.

**Environment Characteristics:**
* **LAN Segment Range:** `10.10.1.0/24` (`10.10.1.0` through `10.10.1.255`)
* **Domain:** `natureforce.com`
* **Active Directory Environment:** `NATUREFORCE`
* **Domain Controller:** `10.10.1.10` (`WIN-LEKBU2OY51N`)
* **Gateway & Broadcast:** Gateway at `10.10.1.1`, Broadcast at `10.10.1.255`

---

## Key Indicators of Compromise (IoCs) Discovered
Through manual traffic analysis, the following core evidence was uncovered:
* **Victim Computer IP:** `10.10.1.128`
* **Victim Hostname:** `DESKTOP-4VGYQX7`
* **Logged-in User:** `bdiaz`
* **Attacker's Server (C2 / Payload):** `107.175.82.242` (Port `9000`)
* **Malicious File:** `runner_ilove.exe`

---

## Step-by-Step Investigation Workflow (Wireshark Actions)

1. **Scoping the Victim (Finding the Infected Computer):**
   * Opened the packet capture in Wireshark to inspect network activity within the `10.10.1.0/24` subnet.
   * Filtered and identified the internal IP address (`10.10.1.128`) generating suspicious traffic, along with its MAC address (`00:22:fb:ec:7e:e7`) and hostname (`DESKTOP-4VGYQX7`).

2. **Identifying the User:**
   * Inspected network packets to find who was actively logged into the endpoint during the incident, pointing to user `bdiaz`.

3. **Investigating Alert 1 (The Executable Transfer):**
   * Verified the first SOC alert by tracking outbound connections to external IP `107.175.82.242` over unusual port `9000`.
   * Discovered that the victim computer downloaded a suspicious executable file named `runner_ilove.exe`.

4. **Investigating Alert 2 (The C2 Traffic):**
   * Checked the PCAP for traffic involving the second alert IP (`195.64.128.106:443`). Finding no active packet flow or data exchanges in the wire capture, it was ruled out as background telemetry/noise rather than an active vector.

5. **Threat Intelligence Verification:**
   * Pulled the unique cryptographic hash of the downloaded payload and checked it against threat intelligence databases (like VirusTotal), confirming it was flagged as malicious malware.

---

## Repository Structure

```text
Network-Traffic-Analysis-Lab/
│
├── README.md               # Main project overview and step-by-step walkthrough
├── report/                 # Full incident report and remediation guide
├── artifacts/              # Supporting evidence (Wireshark screenshots and VirusTotal results)
└── challenges/             # Notes on blockers encountered (like file export permission errors) and solutions
