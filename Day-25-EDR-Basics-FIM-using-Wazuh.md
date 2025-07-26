# **Task 25 - EDR Basics: FIM using Wazuh**

### **Objective:** 
 
Today’s objective was to understand how to use **Wazuh’s File Integrity Monitoring (FIM)** to **detect and investigate unauthorized file changes** on a Windows system. I configured Wazuh to monitor a sensitive directory, simulated an attack by modifying and deleting files, and analyzed the alerts in the Wazuh dashboard. This hands-on exercise helped me grasp how SOC analysts use FIM to catch potential threats, detect malware drops, and maintain compliance through file integrity tracking.

---

###  **What Is File Integrity Monitoring (FIM)?**

**File Integrity Monitoring** is a process of **watching critical system files for unauthorized or unexpected changes** — such as modifications, creations, or deletions.

From a SOC perspective, FIM answers questions like:

>  *“Was this file modified unexpectedly?”*
>  *“Was malware dropped or a config tampered with?”*

--- 
###  **How Wazuh FIM Works**

1. **Agent-based Monitoring**
   Wazuh agents are installed on endpoints or servers. These agents monitor file paths defined in the configuration.

2. **Monitored Attributes:**

   * File path
   * Checksum (e.g., MD5, SHA1)
   * Permissions
   * Owner/Group
   * Timestamp (modified/accessed/created)

3. **How Alerts Are Generated:**
   When a monitored file is changed, Wazuh:

   * Calculates a new checksum
   * Compares it with the baseline
   * If a change is detected, it creates an **alert with rich metadata**


###  **SOC Use Cases for Wazuh FIM**

| Use Case                                  | How Wazuh Helps                                                                                    |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------- |
|  **Detect unauthorized changes**        | Alerts if malware modifies system files (e.g., `/etc/passwd`, `winlogon.exe`)                      |
|  **Monitor sensitive config files** | Tracks changes to web server configs (`httpd.conf`, `nginx.conf`, `web.xml`)                       |
|  **Detect persistence mechanisms**      | Flags changes to autorun entries or scripts (e.g., `.bashrc`, `cronjobs`)                          |
|  **Malware drop detection**             | Detects sudden appearance of new executables or PowerShell scripts                                 |
|  **Compliance (PCI DSS, HIPAA)**        | Ensures file integrity checks are in place for audits                                              |
|  **Chain with other detections**        | Combine FIM with Wazuh's log monitoring to enrich alerts (e.g., who changed the file, from where?) |

---

###  **Example: Linux SOC Use Case**

#### Suppose an attacker uploads a backdoor to `/var/www/html/shell.php` on a web server.

* Wazuh’s FIM detects that a **new file has been added**.
* It checks the file hash and triggers an **alert**.
* The SOC analyst is notified, pivots in the SIEM to see:

  * Who uploaded it
  * What IP made the request
  * Any outbound connections from that file

>  SOC is now able to **respond quickly** — isolate the system, delete the file, and review logs for further compromise.

---

### **Preparation: Configure Wazuh Agent on Windows server Target Machine**  

### **Requirements**  
- **Wazuh Server:** Installed and running. 


### Step 1: Configure File Integrity Monitoring (FIM) in Wazuh
1. Open Wazuh Manager Configuration on the Wazuh Server:
```
sudo nano /var/ossec/etc/ossec.conf
```
2. Add the following configuration under <syscheck> to monitor sensitive files on Windows:
```
<syscheck>
    <frequency>600</frequency>
    <directories check_all="yes">C:\Users\Administrator\Documents</directories>
</syscheck>
```
- This monitors changes in the Public Documents folder.
3. Restart Wazuh Manager to apply changes:
```
sudo systemctl restart wazuh-manager
```

## Attack Simulation & Detection

### Step 1: Simulate an Unauthorized File Change on Windows server
1. Open PowerShell (Run as Administrator) on the Windows machine.
2. Create a test file in the monitored directory:
```
echo "Sensitive data" > C:\Users\Administrator\Documents\important.txt
```
3. Modify the file content to simulate unauthorized changes:
```
echo "Unauthorized modification detected" >> C:\Users\Administrator\Documents\important.txt
```
4. Delete the file to simulate data tampering:
```
Remove-Item C:\Users\Administrator\Documents\important.txt -Force
```

### Step 2: Detect Unauthorized File Changes in Wazuh
1. Open the Wazuh Dashboard (http://<Wazuh-Server-IP>:5601).
2. Navigate to Security Events → File Integrity Monitoring (FIM).
3. Run the following search query in Wazuh to filter FIM logs:
```
rule.group:FIM AND data.win.eventdata.targetFilename:"C:\\Users\\Administrator\\Documents\\important.txt"
```
4. Look for logs indicating:
- File Created
- File Modified
- File Deleted

## Conclusion
-  Successfully configured File Integrity Monitoring (FIM) in Wazuh.    
-  Simulated unauthorized file modifications and deletions on a Windows system.    
-  Detected file changes in Wazuh logs and dashboard alerts.    
-  Learned how SOC analysts investigate unauthorized file changes for threat detection.   

