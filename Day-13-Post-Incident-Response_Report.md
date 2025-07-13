

# **Post-Incident Response Report – Day 13: Suspicious PowerShell Activity**

### **Incident Type**: Suspicious PowerShell Execution (Windows)

### **Date & Time of Detection**:

**July 12, 2025 – 12:00:06 AM**

---

##  **Mapped to NIST Incident Response Process**

### **1. Preparation**

* PowerShell **script block logging** was enabled through Group Policy:

  * **Module Logging**
  * **Script Block Logging**
  * **Script Execution Logging**

* Windows Firewall and Event Viewer were available for analysis.

---

### **2. Detection and Analysis**

### **Trigger**:
Event Viewer captured a **PowerShell command execution** logged as **Event ID 4103** under:
`Applications and Services Logs → Microsoft → Windows → PowerShell → Operational`

### **Command Logged**:

```powershell
Start-Process "notepad.exe" -ArgumentList "C:\Windows\System32\drivers\etc\hosts"
```

- **User**: `WIN-ADC63S5MS5J\Administrator`
-  **Host**: WIN-ADC63S5MS5J
-  **Shell**: Microsoft.PowerShell
-  **Command Type**: Cmdlet
-  **Host Version**: 5.1.26100.1591

### **Behavior**:

* Opened Notepad to access the `hosts` file — often used in network redirection or spoofing.
* While no malicious intent was found in this specific case, the command behavior mimics **common post-exploitation techniques** used by attackers to modify local name resolution.

---

### **3. Containment, Eradication, and Recovery**

####  Containment:

* Outbound traffic was blocked using a custom **Windows Firewall rule**:

  * Name: `Outbound-traffic-block`
  * Direction: Outbound
  * Action: Block
  * Status: Enabled

#### Eradication:

* Verified `hosts` file integrity.
* No changes were made to its content.
* No suspicious binaries were detected for removal.

#### Recovery:

* If any changes had been made, the system would have used:

```powershell
Copy-Item "C:\Backup\hosts" -Destination "C:\Windows\System32\drivers\etc\hosts" -Force
```

* Re-enabled network access if previously blocked:

```powershell
Set-NetFirewallRule -DisplayName "Block Network Access" -Enabled False
```

---

### **4. Post-Incident Activity**

### **Summary**:

* A PowerShell command resembling suspicious behavior was executed by the Administrator user.
* The command opened Notepad to edit the `hosts` file — a technique often used in redirection attacks.
* No unauthorized modifications or malware were found.

 **Recommendations**:

* Implement stricter PowerShell execution policies (e.g., Constrained Language Mode).
* Continue monitoring for Event IDs: **4103**, **4104**, and **4698**.
* Educate administrators on common indicators of misuse.
* Set up automated alerts for any process accessing `hosts`, `netsh`, or modifying firewall rules.

---

##  **Incident Timeline**

| **Time (UTC)** | **Action**                                       |
| -------------- | ------------------------------------------------ |
| 12:00:06 AM    | PowerShell command executed (Event ID 4103)      |
| 12:01 AM       | Firewall rule `Outbound-traffic-block` applied   |
| 12:05 AM       | Event reviewed and user verified (Administrator) |
| 12:10 AM       | No changes found in hosts file                   |
| 12:15 AM       | System returned to normal operation              |

---


