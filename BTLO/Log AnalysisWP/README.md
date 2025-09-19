# 🕵️‍♂️ CD Challenge – Log Analysis – Compromised WordPress

🔗 **Challenge link:** [blueteamlabs Labs Online](https://blueteamlabs.online/home/challenge/log-analysis-compromised-wordpress-ce000f5b59)  
📂 **Category:** Log Analysis  
🗂️ **File analyzed:** `access.log`

---

## 🎯 Objective
Conduct log analysis of WordPress access.log to identify indicators of compromise.

---
## 🔍 Analysis Steps
### 1- IP Analysis from Access Log

To identify the most active IP addresses in the web server log file, the following command was executed:
IP Frequency Analysis (Wide Format)

**Command Used:**

```bash
cat access.log | cut -d ' ' -f 1 | sort | uniq -c | sort -nr
```

**Results:**

| Count | IP Address     | Count | IP Address    | Count | IP Address    | Count | IP Address   |
| ----- | -------------- | ----- | ------------- | ----- | ------------- | ----- | ------------ |
| 1035  | 172.21.0.1     | 49    | 197.23.128.35 | 9     | 132.52.56.77  | 1     | 197.13.28.71 |
| 249   | 119.241.22.121 | 39    | 197.13.28.35  | 9     | 112.33.245.11 | 1     | 197.13.28.61 |
| 168   | 168.22.54.119  | 29    | 127.0.0.1     | 7     | 121.39.211.39 | 1     | 197.13.28.51 |
| 141   | 116.23.212.69  | 16    | 103.212.94.19 | 4     | 216.24.26.193 | 1     | 197.13.28.41 |
| 71    | 156.32.113.25  | 14    | 172.21.0.3    | 4     | 176.33.245.11 | 1     | 197.13.28.31 |
| 70    | 103.69.55.212  | 3     | 107.32.221.97 | 4     | 172.21.0.4    | 1     | 197.13.28.21 |
| 59    | 110.29.54.120  | 2     | 197.13.28.25  |       |               | 1     | 197.13.28.11 |

### Timeline of Events (Ordered)

1-Plugin Simple File List was activated.

* **\[12/Jan/2021 – 15:56:41 UTC]**

  * **Source IP:** `172.21.0.1`
  * **Action:** Request to **activate Simple File List plugin**
  * **Endpoint:** `/wp-admin/plugins.php?action=activate&plugin=simple-file-list%2Fee-simple-file-list.php&_wpnonce=18b9b6c3cf`
  * **Response:** HTTP `302` (redirect)
  * **User-Agent:** `Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0`
  * **Observation:** Plugin activation successfully triggered.

2-Plugin Contact Form 7 was activated.

* **\[12/Jan/2021 – 15:57:07 UTC]**

  * **Source IP:** `172.21.0.1`
  * **Action:** Request to **activate Contact Form 7 plugin**
  * **Endpoint:** `/wp-admin/plugins.php?action=activate&plugin=contact-form-7...`
  * **Response:** HTTP `302` (redirect)
  * **User-Agent:** `Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0`
  * **Observation:** Plugin activation successfully triggered.

3-Observation: Both plugins in use are affected by known Remote Code Execution (RCE) vulnerabilities.

* **Contact Form 7**

  * **Vulnerable versions:** `<= 5.3.1`
  * **Impact:** Allows attackers to exploit file upload misconfigurations leading to arbitrary code execution.

* **Simple File List**

  * **Vulnerable versions:** `<= 4.2.2`
  * **Impact:** Permits unauthorized file upload and remote execution of malicious scripts.

* **Security Implication:**
  Exploiting these vulnerabilities enables attackers to upload and execute arbitrary PHP code on the target server, leading to full compromise.

4-First external IP activity observed.

* **\[14/Jan/2021 – 05:42:34 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Activity:** First external IP interaction detected.
  * **Observation:** IP interacting with both vulnerable plugins (`Contact Form 7` and `Simple File List`).
  * **Behavior:** Crawling file paths on internal host `172.21.0.3`.

5-Attempted authentication bypass.

* **\[14/Jan/2021 – 05:54:14 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Activity:** Attempted authentication bypass.
  * **Observation:** Token identified – `adminlogin`.
  * **Endpoint:** `wp-login.php?itsec-hb-token=adminlogin`
  * **Implication:** Possible brute force or token abuse attempt to gain admin access.
  * 
6-WPScan activity detected.

* **\[14/Jan/2021 – 06:01:41 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Activity:** Used tool **WPScan** (WordPress Scanner).

7-Crawling activity from Taiwan.

* **\[14/Jan/2021 – 06:08:31 UTC]**

  * **Source IP:** `103.69.55.212` (Taiwan)
  * **Activity:** Crawling installed plugins.
  * **Target:** Internal host `172.21.0.3`.

8-Exploitation of vulnerable plugin.

* **\[14/Jan/2021 – 06:26:53 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Exploited Plugin:** `simple-file-list`
  * **Uploaded File:** `fr34k.png`

### Timeline of Events (Extended)

6-WPScan activity detected.

* **\[14/Jan/2021 – 06:01:41 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Activity:** Used tool **WPScan** (WordPress Scanner).

7-Crawling activity from Taiwan.

* **\[14/Jan/2021 – 06:08:31 UTC]**

  * **Source IP:** `103.69.55.212` (Taiwan)
  * **Activity:** Crawling installed plugins.
  * **Target:** Internal host `172.21.0.3`.

8-Exploitation of vulnerable plugin.

* **\[14/Jan/2021 – 06:26:53 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Exploited Plugin:** `simple-file-list`
  * **Uploaded File:** `fr34k.png`

9-GET request to uploaded file.

* **\[14/Jan/2021 – 06:30:11 UTC]**

  * **Source IP:** `103.69.55.212` (Taiwan)
  * **Action:** Sent GET request towards `fr34k.php`.

10-Last observed activities.

* **\[14/Jan/2021 – 06:26:53 UTC]**

  * **Source IP:** `119.241.22.121` (Japan)
  * **Action:** Interaction with vulnerable plugin.

* **\[14/Jan/2021 – 06:30:11 UTC]**

  * **Source IP:** `103.69.55.212` (Taiwan)
  * **Action:** Webshell accessed successfully.

**Total Duration:** 47 minutes & 37 seconds

## 🔗 MITRE ATT\&CK Mapping

* **Initial Access (T1190):** Exploitation of vulnerable WordPress plugins (Simple File List, Contact Form 7).
* **Execution (T1203):** Activation and use of vulnerable plugins enabled remote code execution.
* **Reconnaissance (T1595):** External actors performed WPScan and crawling (Japan, Taiwan) to enumerate plugins and file paths.
* **Credential Access (T1110.003):** Attempted authentication bypass using token `adminlogin`.
* **Execution (T1059):** Upload of malicious file (`fr34k.png`) through Simple File List plugin.
* **Command and Control (T1071.001):** Webshell (`fr34k.php`) accessed successfully from Taiwan IP, providing remote interaction.

## 🛡️ Recommendations
1. **Patch Management:**
   * Update WordPress plugins (Simple File List, Contact Form 7) to the latest secure versions.
   * Regularly review and remove unused/vulnerable plugins.
2. **Access Control:**
   * Implement strong authentication (MFA) for WordPress admin access.
   * Restrict login attempts and monitor authentication bypass attempts.
3. **Monitoring & Detection:**
   * Deploy Web Application Firewall (WAF) to detect and block exploitation attempts.
   * Enable logging and alerting for suspicious file uploads or webshell activity.
4. **Incident Response:**
   * Review uploaded files (`fr34k.png`, `fr34k.php`) and remove any malicious content.
   * Investigate internal host `172.21.0.3` for persistence or further compromise.
5. **Hardening:**
   * Disable direct PHP execution in upload directories.
   * Apply principle of least privilege for file and plugin management.
   * 
## ⚠️ Disclaimer
This report is part of a **CTF / educational challenge**.  
All information (usernames, emails, passwords, domains) is **fictional or simulated**.  
Any resemblance to real data is purely coincidental.



