# Day 15 : Splunk Basics – SSH Log Analysis

### Objective:

Today’s goal is to analyze SSH log activity using Splunk by uploading Zeek-style JSON logs and applying SPL queries to detect failed logins, total connection attempts, and event patterns. This lab builds foundational skills in spotting potential brute-force attacks, abnormal SSH usage, and suspicious remote access—critical for any SOC analyst monitoring Linux and network infrastructure.


## Lab Setup

-  **Splunk**: Already installed and accessible.
-  **Data Source**: JSON-formatted Zeek-style SSH logs.

---

### What is SSH? 
SSH (Secure Shell) is a secure protocol used to remotely access and manage servers, routers, and other networked devices over an encrypted channel. It's commonly used by system admins, developers, and automation scripts to:

- Log into systems 
- Transfer files (via SCP or SFTP)
- Run commands remotely
- SSH typically uses TCP port 22.

### From a SOC Analyst’s Perspective
In a Security Operations Center (SOC), we’re constantly monitoring for abnormal behavior and possible threat actor activity. SSH can be a double-edged sword:
- It enables secure remote access
- It can be abused by attackers for lateral movement or data exfiltration

### Let’s look at the key aspects a SOC analyst focuses on when it comes to Monitoring SSH Traffic:

- Strange SSH connections (from new or foreign IPs)
- Lots of failed logins → possible brute-force attack
- Successful logins at odd times or from unexpected locations
- Port scans on port 22 (default SSH port)
- Use of unusual SSH ports (e.g., port 2222)

##  Steps to Upload SSH Log into Splunk

1. Go to Splunk Web → **Settings > Add Data**.
2. Choose **Upload** and select `synthetic_zeek_ssh.json`.
3. Set Source type: `json` or create a new one `zeek:ssh`.
4. Index: Choose `main` or create a new index like `ssh`.
5. Finish the upload and confirm indexing.

---

##  Lab Tasks

Use SPL queries to complete the following analysis:

### Task 1: List the top 10 endpoints with failed SSH login attempts
```spl
index=ssh sourcetype="json" auth_success=false
| stats count by "id.orig_h"
| sort -count
| head 10
```
### Task 2: Find the number of total SSH connections
```spl
index=ssh sourcetype="json"
| stats count as total_ssh_connections
```
### Task 3: Count all event types (successful, failed, no-auth, multiple-failed) seen in the logs
```spl
index=ssh sourcetype="json"
| stats count by event_type
```

### Conclusion:
This lab demonstrated how Splunk can be used to effectively analyze SSH activity and detect security anomalies such as failed logins and unusual connection patterns. By leveraging SPL queries, I gained hands-on experience in identifying potential brute-force attacks and tracking SSH behavior—an essential skill set for threat detection and incident response in a SOC environment.
