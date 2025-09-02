# 🕵️‍♂️ CD Challenge – Network Analysis: HawkEye Lab

🔗 **Challenge link:** [cyberdefenders Labs Online](https://cyberdefenders.org/blueteam-ctf-challenges/hawkeye/)  
📂 **Category:** Network Analysis  
🗂️ **File analyzed:** `stealer.pcap`

---

## 🎯 Objective
Reconstruct a HawkEye Keylogger data exfiltration incident by analyzing captured network traffic using Wireshark and CyberChef, with the aim of identifying Indicators of Compromise (IoCs), detecting stolen credentials, and understanding the attacker’s techniques.

---
## 🔍 Analysis Steps

1-Traffic Timeline
**Interpretation:**  
The malicious HawkEye exfiltration activity occurred within a one-hour window. This timeframe is consistent with keylogger behavior, where stolen credentials are collected and exfiltrated shortly after infection. 

- **First Packet:** 2019-04-10 22:37:07  
- **Last Packet:** 2019-04-10 23:40:48  
- **Total Duration:** 1 hour, 3 minutes, 41 seconds  

2-Protocol Analysis

The **Protocol Hierarchy Statistics** from Wireshark shows the following key points:

- **Total Packets:** 4003  
- **Main Transport Protocols:**
  - **TCP:** 93.3% of traffic (3734 packets)  
  - **UDP:** 6.1% of traffic (246 packets)  

- **Application Layer Protocols observed:**
  - **SMTP (Simple Mail Transfer Protocol):** 3.7% of packets → Indicates exfiltration via email.  
  - **SMB / NetBIOS:** 2.9% of packets → Local network activity.  
  - **HTTP:** Small presence, but carries significant payload (2 MB).  
  - **TLS:** Minimal, only 9 packets (encrypted connections).  
  - **DCE/RPC & Kerberos:** Related to Windows services and authentication traffic.  

- **Notable Finding:**  
  The presence of **SMTP traffic** strongly suggests that the HawkEye keylogger attempted to exfiltrate stolen data using email. This aligns with known TTPs (Tactics, Techniques, and Procedures) of HawkEye malware, which often sends credentials via SMTP to attacker-controlled email accounts.

![Protocol Hierarchy](image/hierarchy.PNG)

3- Key Connections Summary

| Source (Victim) | Destination        | Packets | Data Size | Observation                          |
|-----------------|--------------------|---------|-----------|--------------------------------------|
| 10.4.10.132     | 217.182.138.150    | 2947    | 2 MB      | **Suspicious – Possible Data Exfiltration (SMTP C2)** |
| 10.4.10.132     | 23.229.162.69      | 280     | 39 KB     | External communication               |
| 10.4.10.132     | 66.171.248.178     | 63      | 5 KB      | External communication               |
| 10.4.10.132     | 216.58.193.131     | 20      | 8 KB      | Likely Google service (legitimate)   |
| 10.4.10.132     | Local Broadcasts   | ~100    | Small     | Normal LAN traffic                   |

**Conclusion:** The connection with `217.182.138.150` stands out as the most suspicious, indicating potential data exfiltration activity.

