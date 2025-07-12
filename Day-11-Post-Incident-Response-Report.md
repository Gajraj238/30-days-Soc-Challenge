#  Day 11 - Post-Incident Activity Report

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


