# 🕵️‍♂️ LetsDefend Challenge – Malicious Doc : RTF Document

🔗 **Challenge link:** [letsdefend Labs Online](https://app.letsdefend.io/challenge/malicious-doic)  
📂 **Category:** Log Analysis  
🗂️ **File analyzed:** `factura.doc`

---

## 🎯 Objective
The objective of our analysis was to uncover and understand the operational mechanism of the malicious file (factura.doc) and to identify the final payload it attempts to download and execute.

---
## 🔍 Analysis Steps

 1. Static Analysis: Structure and Vulnerability Identification

The initial phase focused on dissecting the file's structure to identify the embedded threat.

| Command Executed                                      | Purpose                                             | Primary Finding                                                                 |
|------------------------------------------------------|-----------------------------------------------------|----------------------------------------------------------------------------------|
| ` python3 rtfdump.py ~/factura.doc` | Analyze the RTF document structure.                 | Confirmed an RTF file containing a nested embedded object (`\object`), flagging it as suspicious. |
| ` rtfobj factura.doc`         | Identify the specific type of the embedded OLE object. | Identified as Microsoft Equation 3.0, known to exploit critical vulnerabilities: CVE-2017-11882 or CVE-2018-0802. |
| ` file factura.doc_object_00000CB6.bin` | Determine the file type of the extracted object data. | Confirmed the object is a Composite Document File V2 Document (an OLE Binary format), consistent with an Office exploit component. |
| Signature: `D0 CF 11 E0 A1 B1 1A E1`                  | Analyze the file signature (Magic Bytes).           | Confirms the OLE Compound File V2 format, often used in older Office files to conceal malicious data. |


---

 2. Dynamic Analysis: Behavior and Payload Extraction

The file was executed in a controlled environment (sandbox/debugger) to trace its malicious function calls and determine its ultimate goal.

| Recorded API Calls                                      | Purpose                                              | Behavioral Conclusion                                                                 |
|---------------------------------------------------------|------------------------------------------------------|---------------------------------------------------------------------------------------|
| `ExpandEnvironmentStringsW`                             | Determine the destination path for the final payload. | Sets the save location to `%APPDATA%\aro.exe`.                                        |
| `LoadLibraryW(UrlMon), GetProcAddress(URLDownloadToFileW)` | Load and locate the function needed for file download. | Prepares for network communication.                                                   |
| `URLDownloadToFileW`                                    | Execute the download.                                 | Downloads the final payload from `http://seed-bc.com/.../jan2.exe` and saves it as `aro.exe`. |
| `LoadLibraryW(shell32), ShellExecuteW`                  | Load and execute the newly saved file.                | Executes the downloaded malware (`aro.exe`) to complete the infection.               |


---

# Final Conclusion: Threat Summary

- **Risk level:** High-Risk Exploit Dropper.  
- **Exploitation:** Leverages the Microsoft Equation Editor RCE vulnerability (`CVE-2017-11882`).  
- **Mechanism:** Opening the document triggers code that forces the system to connect to a command-and-control server (`seed-bc.com`).  
- **Payload objective:** Download and execute a secondary malware (`jan2.exe` saved/executed as `aro.exe`) to achieve persistence and full system infection (RCE).

