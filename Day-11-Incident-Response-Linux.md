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


Certainly! Based on your provided screenshots and lab context, here is a clean and professional **Post-Incident Activity** document for **Day 11: Linux Suspicious Bash Script Execution**:

---

#  **Post-Incident Activity Report**

**Lab Title:** Detecting and Responding to Suspicious Bash Script Execution
**Environment:** Kali Linux (Local)
**Script Name:** `fbackup.sh`
**Location:** `/tmp/fbackup.sh`

---

###  **What Triggered the Alert?**

The alert was triggered when a suspicious file (`fbackup.sh`) was detected in the `/tmp` directory — a known staging location often abused by attackers. The script was also found to be running in the background, which raised further suspicion.

Detected using:

```bash
ps aux | grep fbackup.sh  
find /tmp -name "*.sh"
```

---

###  **What Was the Script Doing?**

The script, when inspected with `cat`, was found to simulate a backup operation:

```bash
#!/bin/bash
echo "[*] Simulating backup operation…"
sleep 60
```

**Behavior Observed:**

* The script echoed a message and then slept for 60 seconds, mimicking long-running or idle activity often used to mask malicious behavior.
* While harmless in this simulation, such behavior is typical of scripts used to:

  * Create distraction
  * Evade quick detection
  * Maintain runtime for potential callback functions

---

###  **Which User Executed It?**

The script was executed by the logged-in user:
 **User:** `apt238`

Confirmed via:

```bash
ps aux | grep fbackup.sh
```

The process list showed `apt238` running the script from a user shell.

---

###  **Recommendations for Prevention:**

*  **Restrict execution** in `/tmp` using `noexec` mount option
*  **Enable File Integrity Monitoring (FIM)** tools like **AIDE**
*  **Educate users** on the risks of executing unknown or unverified scripts
*  **Audit scripting activity** through enhanced logging and real-time monitoring

---

###  **Evidence Collected:**

* Script content (`/tmp/fbackup.sh`)
* Process ID and execution path
* User responsible for execution
* Command history and detection steps


