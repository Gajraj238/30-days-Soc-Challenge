# **Day#8: Wireshark Basics – TCP  Protocol Analysis**

## Objective

my goal is to learn how to use *Wireshark* for TCP protocol analysis by examining packet structures and applying display filters to identify all TCP packets, as well as SYN and FIN flags. This helps me understand how to track connection states, detect network scans, and analyze abnormal TCP behavior—key skills for any SOC Analyst or cybersecurity professional.


##  **Lab Setup**

### **System Requirements**
- **Operating System:** Windows 10/11 (or Linux/macOS)
- **Software:** Wireshark (latest version)

---

##  **TCP Packet Structure and Fields**

TCP is a **Layer 4 (Transport Layer)** protocol that ensures reliable, ordered, and error-checked delivery of data between applications.

### **Key TCP Fields:**

| Field Name         | Description                                  |
|--------------------|----------------------------------------------|
| **Source Port**     | Sender’s port number                         |
| **Destination Port**| Receiver’s port number                       |
| **Sequence Number** | Number of the first byte in the segment      |
| **Acknowledgment No** | Confirms received data                    |
| **Flags**           | Control bits (SYN, ACK, FIN, RST, PSH, URG) |
| **Window Size**     | Buffer size available                        |
| **Checksum**        | Error-checking field                         |

---

##  **Most Common TCP Display Filters**

Use these filters in Wireshark’s **Display Filter** bar:

| Filter                  | Description                              |
|--------------------------|------------------------------------------|
| `tcp`                   | Show all TCP packets                     |
| `tcp.flags.syn == 1`    | Show SYN packets (start of connection)   |
| `tcp.flags.fin == 1`    | Show FIN packets (end of connection)     |
| `tcp.port == 80`        | Show TCP packets on port 80              |
| `ip.addr == 192.168.1.1`| TCP traffic to/from a specific host      |

---

## **Lab Task: TCP Packet Analysis in Wireshark**

###  **Task 1: Show All TCP Packets**

**Steps:**

1. Open Wireshark.
2. Start a packet capture or open a saved capture file.
3. In the **Display Filter** bar, enter the following filter:

```wireshark
tcp
```

4. Press **Enter** to apply the filter.

**Expected Outcome:**
Wireshark displays all packets using the TCP protocol.

---

###  **Task 2: Show SYN Packets (Start of Connection)**

**Steps:**

1. In the **Display Filter** bar, enter:

```wireshark
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

2. Press **Enter**.

**Expected Outcome:**
You will see only the initial SYN packets used to begin the TCP 3-way handshake.

---

### **Task 3: Show FIN Packets (End of Connection)**

**Steps:**

1. Enter the following filter:

```
tcp.flags.fin == 1
```

2. Press **Enter**.

**Expected Outcome:**
Wireshark will display packets with the FIN flag set, indicating attempts to gracefully terminate TCP connections.

---

##  **Notes**

* You can right-click any packet and select **"Follow TCP Stream"** to view the full conversation.
* Use these filters in real-time captures or on pre-recorded `.pcap` files for practice.

---

##  Conclusion
- TCP ensures reliable and ordered data delivery through its 3-way handshake and flow control.
- Understanding TCP flags is essential for:
- Connection state tracking
- Troubleshooting dropped or reset connections
- Detecting scanning and abnormal behavior (e.g., RST floods)


