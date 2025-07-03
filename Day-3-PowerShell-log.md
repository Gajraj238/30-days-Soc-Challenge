# **Day#3: Log Analysis Basics: Windows PowerShell Logs**

---

##  Objective

Today, my goal is to understand Windows PowerShell logs and how they capture executed commands for security analysis. I’ll explore key Event IDs like 4103 and simulate a command using PowerShell, then analyze the logs in Event Viewer to learn how SOC analysts detect malicious activities like LOLBAS and post-exploitation behavior.

---

## **Lab Setup**
### **Requirements:**
- **System:** Windows Server 2025
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
  

### **Step 3: Why Is This Important for Blue Teams?**
This log can be used to detect malicious PowerShell usage, such as:
- LOLBAS (Living Off The Land Binaries) like using Start-Process or Invoke-WebRequest
- Loading payloads or obfuscated PowerShell commands
- Persistence via PowerShell commands in startup or tasks

Example of LOLBAS Tools
These are legitimate Windows tools that attackers often abuse for stealthy malicious actions.

| 🛠️ Tool             | 📌 Path                                             | 🚩 Abuse Technique                                       |
|---------------------|-----------------------------------------------------|----------------------------------------------------------|
| **powershell.exe**  | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` | Execute payloads, download malware, bypass AV |
| **certutil.exe**    | `C:\Windows\System32\certutil.exe`                  | Download files using: `certutil -urlcache -f`            |
| **mshta.exe**       | `C:\Windows\System32\mshta.exe`                     | Execute malicious HTML apps or remote scripts            |
| **regsvr32.exe**    | `C:\Windows\System32\regsvr32.exe`                  | Load and execute remote/local DLLs                       |
| **rundll32.exe**    | `C:\Windows\System32\rundll32.exe`                  | Execute DLLs or scripts to evade detection               |
| **wmic.exe**        | `C:\Windows\System32\wbem\wmic.exe`                 | Execute commands, gather system info                     |
| **bitsadmin.exe**   | `C:\Windows\System32\bitsadmin.exe`                 | Download/upload files silently                           |
| **msbuild.exe**     | `C:\Windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe` | Execute malicious C# code in project files  |
| **installutil.exe** | `C:\Windows\Microsoft.NET\Framework\v4.0.30319\installutil.exe` | Run code during .NET assembly install   |
| **schtasks.exe**    | `C:\Windows\System32\schtasks.exe`                  | Create scheduled tasks for persistence                   |

>  For more: [lolbas-project.github.io](https://lolbas-project.github.io)

## Conclusion:
- PowerShell Logs: Key to spotting malicious command usage.
- SOC Analyst Role: Review logs to detect post-exploitation actions.
- Threat Detection: Flags abnormal activity for faster response.

