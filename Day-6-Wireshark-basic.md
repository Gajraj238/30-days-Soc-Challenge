# **Day#6: Introduction to Wireshark – Packet Analysis of ICMP Protocal**

---

## Objective

Today, my goal is to learn how to use **Wireshark** for packet-level analysis by creating display and capture filters for ICMP traffic and observing the **TCP 3-way handshake**. This helps me understand how to detect ping sweeps, connection attempts, and network reconnaissance activities—key skills for any SOC Analyst.


## **Lab Setup**

### **System Requirements**
- **Operating System:** Windows, Linux, or macOS
- **Network Adapter:** Required for packet capture

### **Software Required**
- [Wireshark](https://www.wireshark.org/download.html) (latest stable version)


---

##  **What is Wireshark?**

**Wireshark** is an open-source network protocol analyzer that lets you capture and interactively browse network traffic. It allows analysts to view data packets flowing in and out of a system in real time or from saved PCAP files.

---

## **Wireshark Use Cases for SOC Analysts**

-  **Incident Investigation:** Analyze malicious traffic patterns (e.g., C2 communication, lateral movement)
-  **Malware Analysis:** Extract indicators like domains, IPs, and payloads from suspicious network behavior
-  **Threat Hunting:** Detect anomalies like DNS tunneling, beaconing, or unauthorized FTP/SSH usage
- **Protocol Troubleshooting:** Identify service failures, misconfigurations, or latency issues

---

##  **ICMP Packet Structure and Fields**

ICMP is a Layer 3 protocol used for sending error messages and operational information. The most common ICMP messages include **Echo Request (Type 8)** and **Echo Reply (Type 0)**.

### **Key ICMP Fields:**

| Field Name       | Description                          |
|------------------|--------------------------------------|
| **Type**         | Defines the ICMP message type        |
| **Code**         | Provides further detail for the type |
| **Checksum**     | Error-checking for header            |
| **Identifier**   | Helps match requests and replies     |
| **Sequence No.** | Sequence of the request/reply        |
| **Data**         | Optional payload                     |

---

##  **Most Common ICMP Display Filters**

Use these filters in Wireshark’s **Display Filter** bar:

| Filter | Description |
|--------|-------------|
| `icmp` | Show all ICMP traffic |
| `icmp.type == 8` | Show Echo Requests (ping) |
| `icmp.type == 0` | Show Echo Replies |
| `icmp.code == 3` | Destination unreachable |
| `ip.addr == 192.168.1.10` | ICMP traffic from/to specific host |

---

## Lab Task - Filtering Traffic in Wireshark – ICMP and TCP 3-Way Handshake Analysis

## **Task#1: Create a Display Filter for ICMP Traffic**

### **Instructions:**

1. Launch **Wireshark**.

2. Start a live capture on your active network interface.

3. Open a terminal or Command Prompt and run the command:
   `ping 8.8.8.8`

4. In the **Display Filter bar** in Wireshark, type the following:

   ```wireshark
   icmp && ip.addr == 192.168.1.10

   ```

5. Press **Enter** to apply the filter.

### **Expected Output:**

* Only ICMP packets (ping requests and replies) will be displayed.
* Observe:

  * **Echo Request** (Type 8)
  * **Echo Reply** (Type 0)

### **Optional Filters to Explore:**

| Filter             | Description                  |
| ------------------ | ---------------------------- |
| icmp.type == 8     | Show only Echo Requests      |
| icmp.type == 0     | Show only Echo Replies       |
| ip.addr == 8.8.8.8 | ICMP packets to/from 8.8.8.8 |

---

## **Task#2: Create a Capture Filter for ICMP Traffic**

### **Instructions:**

1. In Wireshark, go to **Capture > Options**.

2. Select your network interface.

3. In the **Capture Filter** field, enter:
 
```wireshark
   icmp and host 192.168.1.10

 ```

4. Click **Start**.

5. In a terminal or CMD window, run:

   ping 8.8.8.8

6. After 4-5 packets, stop the capture.

### **Expected Output:**

* The packet capture will contain **only ICMP traffic**.
* No TCP, UDP, or other traffic will be captured.

---

## **Task#3: Capture Filter for TCP 3-Way Handshake**

### **Understanding the TCP 3-Way Handshake:**

| Step | TCP Flags | Description                 |
| ---- | --------- | --------------------------- |
| 1    | SYN       | Client initiates connection |
| 2    | SYN, ACK  | Server acknowledges         |
| 3    | ACK       | Client confirms             |

### **Instructions:**

1. In Wireshark, go to **Capture > Options**.

2. In the **Capture Filter** field, type:

   tcp\[tcpflags] & tcp-syn != 0

3. Click **Start**.

4. Open a browser and navigate to [https://example.com](https://example.com) or another website.

5. Let a few packets capture, then stop the capture.

### **Expected Output:**

* You’ll only capture TCP packets that **initiate** a connection (SYN flag set).
* Ideal for viewing connection attempts.

---

##  Conclusion
- ICMP is a fundamental protocol for network troubleshooting.
- Wireshark helps visualize ICMP packet flow and structure.
- Understanding ICMP helps detect network scanning, ping sweeps, and unreachable hosts.
**Display Filters** help inspect specific traffic types during analysis.
* **Capture Filters** reduce noise by only collecting what matters.
* Understanding **ICMP** and **TCP 3-way handshakes** is essential for detecting reconnaissance and connection attempts in SOC operations.

