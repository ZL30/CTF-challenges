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

### 1-Plugin Contact Form 7 was activated.
* **\[12/Jan/2021 – 15:57:07 UTC]**

  * **Source IP:** `172.21.0.1`
  * **Action:** Request to **activate Contact Form 7 plugin**
  * **Endpoint:** `/wp-admin/plugins.php?action=activate&plugin=contact-form-7...`
  * **Response:** HTTP `302` (redirect)
  * **User-Agent:** `Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0`
  * **Observation:** Plugin activation successfully triggered.
