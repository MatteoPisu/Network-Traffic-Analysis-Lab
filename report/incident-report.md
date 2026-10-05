# Incident Investigation Report: Natureforce PCAP Triage

## 1. Executive Summary
A security operations center (SOC) review of network traffic alerts for the `natureforce.com` domain revealed suspicious activity originating from an internal workstation (`DESKTOP-4VGYQX7`). Manual packet capture (PCAP) analysis confirmed that the host initiated an outbound connection to an external server and downloaded an unauthorized Windows executable file (`runner_ilove.exe`). Threat intelligence validation confirmed both the payload and the external infrastructure as malicious. Immediate containment, firewall blocking, and host isolation are recommended to prevent further risk to the Active Directory environment.

---

## 2. Initial Alerts Triggered
The investigation was initiated following two primary security alerts:
1. **Alert 1:** `107.175.82.242:9000` — Windows executable (EXE) file sent from an IP address over an unusual TCP port.
2. **Alert 2:** `195.64.128.106:443` — CNCmachineRMS RAT C2 traffic.

---

## 3. Affected Asset & Victim Scoping
By analyzing the packet capture within the `10.10.1.0/24` subnet, the compromised internal endpoint was identified with the following parameters:
* **IP Address:** `10.10.1.128`
* **MAC Address:** `00:22:fb:ec:7e:e7`
* **Hostname:** `DESKTOP-4VGYQX7`
* **Logged-in User:** `bdiaz`
* **Domain Controller:** `10.10.1.10` (`WIN-LEKBU2OY51N`)

---

## 4. Indicators of Compromise (IoCs)
The following technical indicators were discovered and verified during the investigation:

| Indicator Type | Value / Artifact | Description |
| :--- | :--- | :--- |
| **IP Address** | `10.10.1.128` | Compromised internal workstation (Victim) |
| **MAC Address** | `00:22:fb:ec:7e:e7` | Hardware address of the victim endpoint |
| **Hostname** | `DESKTOP-4VGYQX7` | Workstation machine name |
| **User Account** | `bdiaz` | Active user context during compromise |
| **External IP / Port** | `107.175.82.242:9000` | Malicious external C2 and payload delivery server |
| **File Artifact** | `runner_ilove.exe` | Downloaded malicious Windows executable payload |

---

## 5. Investigation Findings & Analysis
* **Alert 1 Verification (Payload Delivery):** Packet analysis confirmed that internal host `10.10.1.128` established an outbound connection to external IP `107.175.82.242` over port `9000`. Through this stream, a Windows executable payload (`runner_ilove.exe`) was downloaded. Threat intelligence validation (VirusTotal) confirmed the file hash and external IP address as malicious.
* **Alert 2 Verification (C2 Traffic):** Review of the PCAP for traffic involving `195.64.128.106:443` showed no active packet flow, data exchange, or communication streams. It was determined to be background noise/unrelated telemetry rather than an active vector.

---

## 6. MITRE ATT&CK Mapping
To categorize the observed behaviors using standard cybersecurity frameworks, the incident maps to the following MITRE ATT&CK techniques:
* **Ingress Tool Transfer (T1105):** The victim host downloaded the malicious payload (`runner_ilove.exe`) from an external IP address (`107.175.82.242`) over an unconventional port (`9000`).
* **Command and Control / Non-Standard Port (T1571):** Communication established over port `9000` bypasses standard web traffic channels to maintain stealth.

---

## 7. Containment & Remediation Recommendations
To mitigate ongoing risk from this incident, the following actions are recommended:
1. **Host Isolation:** Immediately disconnect `DESKTOP-4VGYQX7` from the network to prevent potential lateral movement across the `natureforce.com` domain.
2. **Perimeter Blocking:** Block outbound communication to external IP `107.175.82.242` at the firewall level.
3. **Endpoint Remediation:** Run a targeted endpoint scan to locate and remove `runner_ilove.exe` and check for persistence mechanisms.
4. **Credential Reset:** Force an immediate password reset for user `bdiaz` to mitigate potential credential theft.
