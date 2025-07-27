# **Day - 26 : EDR Basics-Detecting SSH Brute Force attack**

###  **What Is SSH?**

**SSH (Secure Shell)** is a secure network protocol used to **remotely access** and manage systems — commonly Linux and network devices — using encrypted communication.

From a SOC’s viewpoint:

>  SSH is both a **legitimate remote access tool** and a **potential attack vector** if misused.

---

###  **Why SOC Analysts Care About SSH**

While SSH is essential for system administration, it’s also widely abused by attackers for:

* **Initial access** (via stolen credentials or brute-force attacks)
* **Persistence** (planting SSH keys)
* **Lateral movement** (pivoting across internal machines)
* **Data exfiltration** (via tunneling or SCP/SFTP)

That’s why SSH activity must be **monitored continuously**, especially on **critical infrastructure**, jump servers, and DMZ systems.

---

###  **What SOC Analysts Monitor in SSH**

| What to Look For                              | Why It Matters                                                  |
| --------------------------------------------- | --------------------------------------------------------------- |
|  Failed SSH logins                          | Could indicate **brute-force attacks** or scanning              |
|  Unusual login locations                    | May show **unauthorized access** or **compromised credentials** |
|  Odd login times                            | Suspicious access **outside working hours**                     |
|  Long-lived SSH sessions                    | May be used for **data theft** or **backdoors**                 |
|  Large file transfers                       | Could signal **SCP/SFTP exfiltration**                          |
|  Use of non-standard SSH ports (e.g., 2222) | Common technique to **bypass default monitoring**               |
|  Use of tools like `sshpass`, `autossh`     | May indicate **scripted lateral movement or persistence**       |

---


###  **Common SSH Attack Scenarios in SOC Investigations**

### 1. **Brute-force Attack**

* **Alert:** 1000+ failed SSH logins in 10 minutes
* **Response:** Block IP, alert threat hunting team, review network logs

### 2. **Stolen SSH Key Used**

* **Alert:** Successful login with SSH key from new location
* **Response:** Disable key, investigate access trail, check for lateral movement

### 3. **SSH Lateral Movement**

* **Alert:** Internal system initiating SSH sessions to many peers
* **Response:** Look for `sshpass` or scripted connections, check for compromised accounts

### 4. **SSH Tunneling / Exfiltration**

* **Alert:** Long-lived SSH session with high data transfer
* **Response:** Inspect connection, isolate host, verify data leakage


###  **SOC Recommendations for SSH Security**

| Action                                     | Why                                  |
| ------------------------------------------ | ------------------------------------ |
| Enforce **key-based auth** (not passwords) | Stronger, less brute-force risk      |
| Disable **root login via SSH**             | Prevent privilege abuse              |
| Use **multi-factor authentication (MFA)**  | Stops credential-only logins         |
| Monitor **authorized\_keys** file changes  | Detects added backdoor access        |
| Restrict **SSH by IP/port** in firewalls   | Reduce attack surface                |
| Use **SSH banners/logging**                | Track access attempts & notify users |
| Deploy **honeypots** on port 22            | Catch opportunistic attackers        |

---

## **Preparation: Configure Wazuh Agent on Windows server Target Machine**  

### **Requirements**  
- **Wazuh Server:** Installed and running.   
- **Windows Server 2025 (Target Machine).**  
- **Wazuh Agent installed on the Windows server.**  
- **SSH enabled on the target machine.**  
- **Admin or sudo access on the target machine.**  


## Attack Simulation & Detection

### Step 1: Simulate an SSH Brute-Force Attack Using Hydra

1. From the attacker machine, install Hydra:

```
sudo apt update && sudo apt install hydra -y
```

2. Run the following command to simulate an SSH brute-force attack:

```
hydra -l administrator -P /usr/share/wordlists/rockyou.txt <target-IP> ssh
```
- Replace `<target-IP>` with the Ubuntu target machine’s IP address.
- `-l administrator` specifies the username to attack.
- `-P /usr/share/wordlists/rockyou.txt` uses a password list for brute-forcing SSH.
3. If a valid password is found, Hydra will display:

```
[22][ssh] host: <target-IP> login: administrator password: <password>
```

### Step 2: Detect SSH Brute Force Attempts in Wazuh
1. Open the Wazuh Dashboard (http://<Wazuh-Server-IP>:5601).
2. Navigate to Security Events and run below query
```
rule.id:(5551 OR 5712)
```
4. Look for logs indicating:
- Multiple failed SSH login attempts from the same IP.
- High volume of authentication failures in a short period.
- Successful logins after repeated failed attempts (possible compromise).

## Conclusion   
- Simulated SSH brute-force attacks using Hydra.   
-  Detected brute-force attempts in Wazuh logs and alerts.   
-  Learned how SOC analysts investigate authentication attacks using SIEM tools.   

