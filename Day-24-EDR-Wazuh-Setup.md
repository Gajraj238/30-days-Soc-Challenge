# **Day 24 - Introduction to EDR and Wazuh Installation**

## **Objective:**  

Today, my goal was to understand how Endpoint Detection and Response (EDR) tools empower SOC analysts to detect, investigate, and respond to endpoint threats in real time. I focused on how EDR differs from traditional antivirus, explored key use cases, and completed a hands-on lab by setting up **Wazuh**, an open-source EDR solution. I deployed a Wazuh server on Ubuntu, installed the agent on a Windows machine, and verified connectivity through the dashboard—gaining practical experience with endpoint visibility and monitoring.


##  **Introduction to EDR (Endpoint Detection and Response)**

**EDR** refers to tools that monitor, record, and analyze activities on endpoints (servers, desktops, laptops) to detect malicious behavior, help in incident response, and enable threat hunting.

---

##  **How Does a SOC Analyst Use EDR?**

SOC Analysts rely on EDR tools to:
- Detect suspicious behaviors (e.g., process injection, lateral movement)
- Investigate alerts and correlate activity over time
- Isolate or respond to infected endpoints
- Pull forensic artifacts like logs, memory dumps, and timelines
- Hunt for threat indicators (IOCs)

---

##  **Popular EDR Platforms**

| Platform           | Description                                              |
|--------------------|----------------------------------------------------------|
| **Wazuh**           | Open-source SIEM + EDR, host-based log monitoring, FIM   |
| **Microsoft Defender for Endpoint** | Native EDR for Windows with behavioral analytics   |
| **CrowdStrike Falcon**   | Cloud-based EDR with threat hunting capabilities     |
| **SentinelOne**     | Autonomous endpoint protection and rollback features     |
| **Elastic Endpoint (with ELK)** | Lightweight endpoint monitoring integrated into Elastic SIEM |

---

##  **EDR Use Case in the SOC**


###  Example: Suspicious PowerShell Activity

1. **EDR Alert**: "PowerShell connecting to external IP"
2. **SOC Analyst** investigates:

   * Checks process tree: Who launched PowerShell? Was it Office?
   * Views command line arguments: Was `Invoke-WebRequest`, `Base64`, or `iex` used?
   * Looks at network connection: Where did it go? Suspicious IP?

3. **Response**:

   * Isolates host from the network
   * Kills malicious process
   * Tags the incident for IR team
   * Hunts for similar behavior on other endpoints

---

##  **What EDR can do**

EDR is **not the same as traditional antivirus (AV)**.

| Feature              | Antivirus | EDR |
| -------------------- | --------- | --- |
| Signature-based      | ✅         | ✅   |
| Real-time protection | ✅         | ✅   |
| Deep visibility      | ❌         | ✅   |
| Threat hunting       | ❌         | ✅   |
| Remote response      | ❌         | ✅   |
| Forensics            | ❌         | ✅   |

---


## **Lab Task: Setting up Wazuh EDR**  

### **Requirements**  
- **System 1:** Ubuntu 22.04/20.04 (Wazuh Server)  
- **System 2:** Windows Server 2025 (Agent Machine to be monitored)  
- **Minimum Hardware for Wazuh Server:**  
  - **CPU:** 4 vCPUs  
  - **RAM:** 8GB+  
  - **Storage:** 50GB+  
- **Network Connectivity:** Ensure both systems can communicate over the network.  
- **User Permissions:** Root or sudo privileges on both machines.  

---

### **Step 1: Install Wazuh Server Using Quick Start**
1. Download and run the Wazuh installation assistant.
```
curl -sO https://packages.wazuh.com/4.10/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```
Once the assistant finishes the installation, the output shows the access credentials and a message that confirms that the installation was successful.

```
INFO: --- Summary ---
INFO: You can access the web interface https://<WAZUH_DASHBOARD_IP_ADDRESS>
    User: admin
    Password: <ADMIN_PASSWORD>
INFO: Installation finished.
```
- You now have installed and configured Wazuh.

2. Access the Wazuh web interface with https://<WAZUH_DASHBOARD_IP_ADDRESS> and your credentials:

- Username: admin
- Password: <ADMIN_PASSWORD>

### Step 2: Onboard Windows server Machine as a Wazuh Agent
1. Install the Wazuh Agent on the Windows server to be monitored:

```
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.12.0-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='192.168.57.138' WAZUH_AGENT_NAME='win-server' 
```


### Step 3: Verify Agent Connection in Wazuh Dashboard
1. Open Wazuh Dashboard (http://<Wazuh-Server-IP>:5601).
2. Navigate to "Agents" in the Wazuh UI.
3. Check if the Ubuntu agent is listed as "Active".


## Conclusion
- Successfully installed Wazuh Server using the Quick Start method.    
-  Deployed Wazuh Agent on an Ubuntu machine for monitoring.   
-  Verified agent connection in the Wazuh Dashboard.    
-  Learned how SOC analysts use Wazuh for security monitoring and log analysis.    

