# **Day 10: Introduction to Incident Response**

## **Objective:**
Today, my goal is to understand the fundamentals of **Incident Response (IR)** by exploring the NIST IR lifecycle, identifying common types of security incidents across platforms, and practicing hands-on detection and response to a simulated RDP brute-force attack. This helps build essential skills in identifying, containing, and responding to real-world threats—a core responsibility for any SOC Analyst.


---

## **What is Incident Response?**

**Incident Response (IR)** is the structured approach to handle and manage the aftermath of a **security breach or cyberattack**, also known as an incident. It includes steps to:
- Detect the incident
- Contain the damage
- Investigate the root cause
- Recover normal operations
- Report and document findings

---

## **Incident Response Process (NIST SP 800-61 Rev. 2)**

The NIST Incident Response Lifecycle includes **4 main phases**:

| **Phase**                         | **Description**                                                                 |
|----------------------------------|---------------------------------------------------------------------------------|
| **1. Preparation**               | Establish policies, train team, and set up tools and logging mechanisms.       |
| **2. Detection and Analysis**    | Identify potential incidents using logs, alerts, and anomaly detection systems.|
| **3. Containment, Eradication, and Recovery** | Isolate threats, remove malware/artifacts, and restore systems securely.       |
| **4. Post-Incident Activity**    | Conduct lessons learned, create reports, and improve incident response plans.  |

---



## **Common Types of Incidents Across Platforms**

| **Platform** | **Incident Type**                             | **Description**                                                                 |
|--------------|-----------------------------------------------|---------------------------------------------------------------------------------|
| **Windows**  | Unauthorized Login Attempts                   | Multiple failed logins or brute-force via RDP or local access.                  |
|              | PowerShell-based Attacks                      | Obfuscated or encoded PowerShell commands for exploitation or lateral movement. |
|              | Malware/Ransomware Execution                  | Malicious EXE/DLL files executed, causing encryption or data theft.             |
|              | Credential Dumping                            | Use of tools like Mimikatz to extract credentials from LSASS memory.            |
|              | Lateral Movement via WMI or RDP               | Internal system traversal using Windows services and accounts.                  |
| **Linux**    | SSH Brute Force or Unauthorized Access        | Repeated login attempts or access from unknown IPs via SSH.                     |
|              | Malicious Cron Jobs for Persistence           | Scheduled tasks created by attackers to maintain access.                        |
|              | Sudo Abuse / Privilege Escalation             | Exploiting weak sudo rules or known vulnerabilities to gain root privileges.    |
|              | Web Shell Upload                              | Attackers upload backdoors via vulnerable web applications.                     |
|              | Crypto Miner Installation                     | Hidden mining software running under compromised user accounts.                 |
| **AWS**      | IAM Credential Misuse                         | Leaked API keys used from unknown geographies.                                  |
|              | Root User Console Access                      | Suspicious login or activity from the AWS root account.                         |
|              | Unusual S3 Bucket Access                      | Public or unauthorized access to private storage.                               |
|              | CloudTrail or Logging Disabled                | Logging service turned off to hide attacker activity.                           |
|              | EC2 Abuse for Command and Control             | EC2 instances being used as a staging point or C2 server.                       |
| **Network**  | DDoS Attack                                   | Flood of traffic targeting public-facing services.                              |
|              | Port Scanning / Reconnaissance                | Continuous scanning of open ports across network segments.                      |
|              | ARP Spoofing / Man-in-the-Middle (MitM)       | Tampering with traffic flows for interception.                                  |
|              | DNS Tunneling                                 | Covert data exfiltration using DNS queries.                                     |
|              | Unauthorized VPN Access                       | Compromised VPN credentials used for internal access.                           |
| **Email**    | Phishing / Credential Harvesting              | Fake emails with links to steal user credentials.                               |
|              | Malware via Attachments                       | Office or ZIP files with embedded malware.                                      |
|              | Business Email Compromise (BEC)               | Attacker impersonates an executive to trick employees.                          |
|              | Spoofed Email Domains                         | Sending emails from domains that look like legitimate ones.                     |
|              | Internal Account Compromise                   | Compromised internal mailbox used to spread phishing internally.                |


---

## **How SOC Analysts Respond to Incidents:**
- Monitor logs and alerts (via SIEM)
- Investigate suspicious behavior
- Contain and isolate infected systems
- Coordinate with other teams
- Document and report the incident

---

## Lab Task: Detect and Respond: Windows Server RDP Brute Force Attack

---

## Lab Setup and Requirements

### Machines Required:
- **Windows Server 2019 or 2022 or 2025**
  - RDP enabled
  - Event Viewer access
  - One local user account with known username
- **Kali Linux VM**
  - Hydra pre-installed
  - Connected to same LAN or Virtual Network

### Network:
- Ensure **both machines are on the same network**
- Verify **RDP (TCP/3389)** is open on Windows Server

---

## Preparation Steps

### On **Windows Server**:
1. **Enable RDP**:  
   `System Properties → Remote → Enable Remote Desktop`

2. **Allow RDP in Firewall**:  
   `Windows Defender Firewall → Advanced Settings → Inbound Rules → Remote Desktop (TCP-In) → Enable`

3. **Create Test User**:
   ```powershell
   net user <username> Password123 /add
   ```
Open Event Viewer:
Windows Logs → Security
Filter for Event ID 4625 (Failed Logon)



### Simulate the Attack

1. On Kali Linux:
Install Hydra (if not installed):

```
sudo apt update && sudo apt install hydra
```
Prepare Wordlist: Use the existing list or create a custom one, e.g.: /usr/share/wordlists/rockyou.txt
2. Run this command on Kali Linux:
```
hydra -t 4 -V -f -l <username> -P /usr/share/wordlists/rockyou.txt rdp://<Windows_Server_IP>
```
Replace <Windows_Server_IP> with actual IP of Windows Server

### Visualize the Alert in Event Viewer
1. On Windows Server:
Open `Event Viewer → Windows Logs → Security`

2. Look for Event ID 4625 with:

Logon Type: 10 (RemoteInteractive / RDP)

Failure Reason: "Unknown user name or bad password"

Caller IP Address: IP of Kali machine



### Incident Response Steps
1. Identify Repeated Failed Logons:
- Spot Event ID 4625 from same IP
2. Correlate IP Address:
- Confirm repeated failures from attacker’s IP
3. Lock the User Account (Optional):
```
net user attackerlab /active:no
```
4. Block Attacker IP:

```
New-NetFirewallRule -DisplayName "Block Attacker" -Direction Inbound -RemoteAddress <Kali_IP> -Action Block
```
5. Collect Evidence:
- Export relevant Event Logs
6. Report Incident:
- Create a brief report with findings and actions taken



## Conclusion
This lab demonstrated how to:
- Simulate an RDP brute-force attack using Hydra
- Detect suspicious logons via Windows Event Viewer
- Respond with basic IR steps without using Sysmon
