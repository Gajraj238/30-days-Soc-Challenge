# **Day 11: Incident Response Basics – Linux Suspicious Bash Script Execution**

## **Objective:**  
Today, my goal is to understand how to detect and respond to suspicious bash script execution on a Linux system by following the NIST Incident Response process. This includes identifying malicious activity in `/tmp`, analyzing running processes, containing the threat, and documenting findings—essential skills for SOC analysts investigating Linux-based intrusions.

---

## **What is Incident Response?**

**Incident Response (IR)** is the process of detecting and managing a cybersecurity incident to minimize impact and restore normal operations. This lab introduces a basic Linux scenario where an attacker’s script is executed through user error or misconfiguration.

---

##  **Incident Response Process (NIST SP 800-61 Rev. 2)**

| **Phase**                         | **Description**                                                                 |
|----------------------------------|---------------------------------------------------------------------------------|
| **1. Preparation**               | Ensure logging is enabled, tools are installed, and the system is ready.        |
| **2. Detection and Analysis**    | Identify suspicious activity using logs, running processes, and file checks.    |
| **3. Containment, Eradication, and Recovery** | Isolate the threat, remove the script, and secure the system.                    |
| **4. Post-Incident Activity**    | Document the incident and implement prevention steps.                          |

---

##  **Scenario: Suspicious Script Found in /tmp**

Your monitoring system flagged a suspicious file in `/tmp`. Upon inspection, it's a bash script (`fbackup.sh`) that connects to an unknown IP. You are tasked with investigating this incident.

---

##  **Lab Setup**

### **System Requirements:**
- Ubuntu 20.04/22.04 or Kali Linux
- Terminal access with sudo privileges

### **Simulate the attack:**

1. Create a test directory:
```
mkdir ~/script-lab && cd ~/script-lab
```
2. Create a Bash script:

```
nano fbackup.sh
```
3. Paste the following content:

```
#!/bin/bash
echo "[*] Simulating backup operation..."
sleep 60
```
4. Make the script executable:

```
chmod +x fbackup.sh
```

5.  Simulate the Attack / Execution
Run the script in the background:
```
./fbackup.sh &
```

## Step-by-Step Investigation

### 1. Preparation
- Install curl, lsof, ps, and grep (usually pre-installed).

### 2. Detection and Analysis
- Check running processes:
```
ps aux | grep fbackup.sh
```
- Search for suspicious files:
```
find /tmp -name "*.sh"
```

### 3. Containment, Eradication, and Recovery
- Kill any related processes:
```
pkill curl
```
- Remove the malicious script:
```
rm -f /tmp/fbackup.sh
```
- Clear the crontab if persistence was found:
```
crontab -e
```
- Restart services if needed and log out inactive sessions.
### 4. Post-Incident Activity
- Document:
 -  What triggered the alert?
 -  What was the script doing?
 - Which user executed it?

- Recommendations:
 - Enable file integrity monitoring (e.g., AIDE).
 - Restrict /tmp execution using mount options (noexec).
 - Educate users about unknown script execution.

## Lab Checklist
Simulate Script	Create and execute a suspicious bash script    
Investigate Logs	Use commands to analyze the event    
Kill and Delete	Contain and remove the malicious file   
Document Findings	Note IPs, users, and recommendations    


