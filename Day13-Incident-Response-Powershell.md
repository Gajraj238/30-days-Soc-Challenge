# **Day 13: Detecting Suspicious PowerShell Activity – Windows Incident Response**

---

## **Objective:**  
Today, my goal is to detect and respond to suspicious PowerShell activity on a Windows system by analyzing command execution through Windows Event Viewer. This lab focuses on identifying potentially malicious PowerShell behavior using event logs like ID 4103 and 4104, following the NIST Incident Response process to contain, eradicate, and recover from script-based threats—key skills for Windows-focused SOC analysts.


---

## **Why PowerShell Matters in Incident Response**

**PowerShell** is a powerful tool for system administration — but it’s also commonly used by attackers to download malware, move laterally, and execute hidden scripts. Proper logging and monitoring can help detect abuse.

---

## **Incident Response Process (NIST SP 800-61 Rev. 2)**

| **Phase**                         | **Description**                                                                 |
|----------------------------------|---------------------------------------------------------------------------------|
| **1. Preparation**               | Enable PowerShell logging and ensure security auditing is in place.            |
| **2. Detection and Analysis**    | Identify and investigate PowerShell misuse in logs.                            |
| **3. Containment, Eradication, and Recovery** | Kill malicious processes, remove scripts, and secure PowerShell usage.         |
| **4. Post-Incident Activity**    | Document findings and improve PowerShell restrictions and monitoring.          |

---

##  **Scenario: Suspicious PowerShell Command Executed**

A user accidentally ran a PowerShell command that simulates suspicious behavior — like contacting a remote server and writing data to disk. You must detect and analyze this command through system logs.

---

## **Lab Setup**
### **Requirements:**
- **System:** Windows 10/11 or Windows Server 2019/2022/2025
- **Tools:**
  - **Windows Event Viewer** (pre-installed)
  - **PowerShell (Pre-installed on Windows)**
  - **Administrative Privileges** (required for enabling logs)

---

## **Preparation:**
Before proceeding, make sure PowerShell script block logging is enabled on your system:

1. Press `Win + R`, type `gpedit.msc`, and press Enter to open the **Group Policy Editor**.
2. Navigate to:
`Computer Configuration > Administrative Templates > Windows Components > Windows PowerShell`
3. Turn on **Module Logging**, **Script Block Logging**, and **Script Execution**.
4. Apply the settings and close the Group Policy Editor.

---

## **What are Windows PowerShell Logs?**
PowerShell logs contain information about PowerShell script executions, including details about the commands that were run, the processes that invoked them, and the user who executed them. These logs can be used to detect potential misuse of PowerShell, including post-exploitation techniques often used by attackers.

### **Key PowerShell Logs to Monitor:**
- **Event ID 4104**: Script block logging, capturing the PowerShell commands executed.
- **Event ID 4103**: Command invocation with parameter binding (detailed command execution).
- **Event ID 4698**: PowerShell Module Logging for the execution of specific modules.
- **Event ID 4101**: Execution of PowerShell commands through command-line arguments.

---

## **Lab Task: Explore and Analyze Windows PowerShell Logs**


### **Step 1: Generate PowerShell Logs**
1. Open **PowerShell** as Administrator.
2. Run the following PowerShell command to generate a log entry:
```powershell
Start-Process "notepad.exe" -ArgumentList "C:\Windows\System32\drivers\etc\hosts"
```
This command
-  Starts a new process using the Start-Process cmdlet.
-  Specifies "notepad.exe" as the program to launch.
-  Passes "C:\Windows\System32\drivers\etc\hosts" as an argument to Notepad.
-  As a result, Notepad opens the hosts file directly.


### **Step 2: Visualize the events**

1. After running the command, go back to Event Viewer and navigate to:

`Applications and Services Logs → Microsoft → Windows → PowerShell → Operational`

4. Look for Event ID 4103 in the logs (this will show script block logging for the PowerShell command you executed).
5. Take a screenshot of the event details, including:
 - PowerShell command that was executed
 - User who ran the command
 - Timestamp of the execution

### **Step 3: Incident Response**

1. Check the file and it content

`C:\Windows\System32\drivers\etc\hosts`

2. Containment:
Isolate the system: If you suspect malicious activity, you can block network connections:
Note: Usually this is doen from EDR tool.
```
New-NetFirewallRule -DisplayName "Block Network Access" -Direction Outbound -Action Block -Enable
```
3. Eradication:
Restore the Hosts File: If modifications to the hosts file were made without authorization, restore it from a backup:

```
Copy-Item "C:\Backup\hosts" -Destination "C:\Windows\System32\drivers\etc\hosts" -Force
```
Remove Suspicious Files: If you find any suspicious files related to the incident, you can remove them:

```
Remove-Item "C:\Path\To\SuspiciousFile.exe" -Force
```

4. Recovery:
Restore from Backup (if necessary): Restore the system to a clean state from backups:

```
Restore-Computer -RestorePoint 1  # Restores to the first available restore point
```
Re-enable Network Access: After securing the system, re-enable network access by disabling the firewall rule:

```
Set-NetFirewallRule -DisplayName "Block Network Access" -Enabled False
```

5. Reporting
- Write incident response report with timeline, command etc

### Conclusion 

This lab demonstrated how attackers can abuse PowerShell for stealthy operations and how defenders can detect such activity using Windows event logs. By enabling logging, analyzing key event IDs, and following the IR process, we practiced essential skills for identifying and mitigating PowerShell-based threats in real-world SOC environments.


