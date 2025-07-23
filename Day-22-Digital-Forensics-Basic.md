# **Day 22: Introduction to Digital Forensics**

## **Objective**  

Today’s objective is to understand the fundamentals of Digital Forensics and practice memory acquisition from a live Linux system. I aim to learn how to identify, preserve, and collect volatile evidence using Microsoft’s AVML tool, validate the memory dump, and securely transfer it for analysis. This hands-on exercise strengthens my ability to support incident response, investigate cyber threats, and maintain forensic integrity within a SOC environment.


###  **What is Digital Forensics?**

**Digital Forensics** is the science of identifying, preserving, analyzing, and presenting digital evidence in a legally acceptable manner. It involves recovering and examining data from digital devices like computers, servers, networks, and mobile devices — especially after a security breach or criminal incident.

Digital forensics plays a critical role in cybercrime investigations, incident response, insider threat detection, and legal proceedings.

---

###  **Goal of Digital Forensics**

The main goal of digital forensics is to:
- Identify and preserve digital evidence
- Reconstruct timelines of digital activity
- Determine the root cause of incidents or intrusions
- Support legal or administrative actions with valid findings
- Maintain chain of custody and evidence integrity

---

###  **Types of Digital Forensics**

| **Type**               | **Description**                                                                 | **Common Tools**                                  |
|------------------------|----------------------------------------------------------------------------------|---------------------------------------------------|
| **Linux Forensics**     | Investigation of Linux systems, logs, memory, and file integrity                | `log2timeline`, `auditd`, `grep`, `kape`, `KVML`  |
| **Windows Forensics**   | Analysis of registry, Event Logs, prefetch, and file metadata                   | `FTK Imager`, `Registry Explorer`, `KAPE`, `PECmd`|
| **Mobile Forensics**    | Recovery of deleted data, call logs, chats, and app artifacts from smartphones  | `Cellebrite`, `Magnet AXIOM`, `MobSF`             |
| **Network Forensics**   | Capturing and analyzing packets, logs, and intrusion traces                     | `Wireshark`, `tcpdump`, `Zeek`, `Suricata`        |
| **Memory Forensics**    | Live RAM acquisition and analysis of volatile artifacts                         | `Volatility`, `Rekall`, `LiME`, `KVML`            |
| **Cloud Forensics**     | Investigation of cloud logs, APIs, and storage for abnormal activity            | `AWS CloudTrail`, `GCP Logs`, `AZ Sentinel`, `FIR`|
| **IoT Forensics**       | Analyzing embedded devices and sensor data for signs of compromise              | `Autopsy`, `Binwalk`, `Scalpel`, `Firmware-Mod-Kit`|


---

###  **Digital Forensics Process (Simplified 5-Step Approach)**

| Step                | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| **1. Identification** | Detect potential sources of evidence (e.g., compromised system, suspicious process) |
| **2. Preservation**   | Isolate and protect the system from tampering or loss of evidence         |
| **3. Collection**     | Acquire volatile (memory) and non-volatile (disk) data                     |
| **4. Analysis**       | Examine the data to uncover malicious activity, indicators, or artifacts  |
| **5. Reporting**      | Document findings clearly and support with timelines, screenshots, or logs|

---

## **Preparation: Lab Setup**  
### **Requirements**  
- **System 1 (Target Machine):** Ubuntu 22.04/20.04 (Machine from which memory will be acquired).  
- **System 2 (Analysis Machine):** kali machine (Machine where the memory dump will be transferred and analyzed).  
- **User Permissions:** Root or sudo privileges on both machines.  
- **Tools Required:**  
  - **AVML ( by Microsoft) for memory acquisition**  
  - **strings & grep** for quick validation  
  - **scp or rsync** for transferring the memory dump  

---

## **Forensic Acquisition Steps**  

### **Step 1: Download AVML on Target Machine**
1. Open a terminal on the **Target Machine (System 1)**.
2. Download KVML from the official GitHub releases:  
   ```bash
   wget https://github.com/microsoft/avml/releases/latest/download/avml
   ```

### Step 2: Make AVML Executable
1. Grant execute permissions:
```
chmod +x ./avml
```
2. Verify execution by running:
```
./avml --help
```
Step 3: Create a Memory Dump
1. Run the following command to acquire a memory dump and save it as linux_memory.mem:
```
sudo ./avml linux_memory.mem
```
2. Confirm the memory dump file has been created:
```
ls -lh linux_memory.mem
```

### Step 4: Validate the Memory Dump
1. Use `strings` and `grep` to check if known data appears in the dump:
```
strings linux_memory.mem | grep "mozilla"
```
- If strings related to applications, files, or processes appear, the dump is valid.

### Step 5: Transfer the Memory Dump to the Analysis Machine
1. On the Analysis Machine (System 2), create a directory to store the memory dump:

```
mkdir ~/forensics/memory_dumps
```
2. Transfer the memory dump from the Target Machine to the Analysis Machine using scp:

```
scp user@<Target-Machine-IP>:/home/user/linux_memory.mem ~/forensics/memory_dumps/
```
- Replace <Target-Machine-IP> with the actual IP of the Target Machine.
- Enter the password when prompted.

3. Verify the file is successfully transferred:

```
ls -lh ~/forensics/memory_dumps/
```

## Conclusion
- Successfully acquired a Linux memory dump using AVML.   
- Performed basic validation to confirm the memory dump’s integrity.   
- Transferred the memory dump to a forensic workstation for further analysis.   
- Learned how SOC analysts and forensic investigators collect volatile memory for incident response.   

