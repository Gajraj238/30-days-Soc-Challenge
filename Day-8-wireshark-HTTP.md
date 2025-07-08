# **Day#8 Wireshark Basics – HTTP Protocol Analysis** 

## Objective:

Today, my goal is to learn how to use *Wireshark* for analyzing **HTTP protocol traffic** by examining HTTP packet fields and applying display filters. This helps me identify sensitive data exposure, detect malware communication, and investigate suspicious web activity—critical skills for web traffic monitoring in a SOC environment.


---


##  **Lab Setup**

### **System Requirements**
- **Operating System:** Windows 10/11 (or Linux/macOS)
- **Software:** Wireshark (latest version)

---

##  **HTTP Packet Structure and Fields**

HTTP is an **application-layer protocol** used for communication between clients (browsers) and web servers. It typically runs over TCP port **80**.

### **Key HTTP Fields:**

| Field Name         | Description                              |
|--------------------|------------------------------------------|
| **Request Method** | GET, POST, HEAD, etc.                    |
| **Host**           | The website being accessed               |
| **User-Agent**     | Information about the client/browser     |
| **URI**            | Resource path on the server              |
| **Status Code**    | Server's response status (e.g., 200 OK)  |
| **Content-Type**   | MIME type of the response (e.g., text/html) |
| **Cookie/Header**  | Session or tracking information          |

---

## **Most Common HTTP Display Filters**

Use these filters in Wireshark’s **Display Filter** bar:

| Filter                    | Description                              |
|---------------------------|------------------------------------------|
| `http`                   | Show all HTTP traffic                    |
| `tcp.port == 80`         | HTTP traffic by default port             |
| `http.request.method == "GET"` | Show all GET requests             |
| `http.request.uri`       | View requested resources                 |
| `http.set_cookie`        | Show cookies in HTTP responses           |
| `ip.addr == 192.168.1.10`| HTTP traffic to/from specific host       |

---

Absolutely! Here's a clean and well-structured **Lab Task Document** for analyzing HTTP traffic in Wireshark:

---

#  **Lab Task: HTTP Traffic Analysis in Wireshark**

###  **Task 1: Show All HTTP Traffic**

**Steps:**

1. Launch **Wireshark**.
2. Start a live capture on your active network interface (or open a `.pcap` file).
3. In the **Display Filter** bar, type the following:

```wireshark
http
```

4. Press **Enter** to apply the filter.

**Expected Output:**
All packets using the HTTP protocol will be displayed.

---

###  **Task 2: Show All GET Requests**

**Steps:**

1. In the **Display Filter** bar, enter:

```wireshark
http.request.method == "GET"
```

2. Press **Enter**.

**Expected Output:**
You will see only HTTP GET requests, which are typically used to retrieve resources from a web server.

---

###  **Task 3: View Requested Resources (URIs)**

**Steps:**

1. In the **Display Filter** bar, enter:

```wireshark
http.request.uri
```

2. Press **Enter**.
3. Click on any filtered packet and expand the **Hypertext Transfer Protocol** section to view the **Request URI**.

**Expected Output:**
You’ll be able to see the specific resource paths (URIs) requested by the client, such as `/index.html`, `/login`, or `/api/data`.

---

##  **Notes**

* You can right-click any HTTP packet and select **"Follow TCP Stream"** to view the full request/response conversation.
* HTTP analysis helps identify what web content is being accessed, by whom, and whether sensitive data is being exposed.

---

##  Conclusion
- HTTP traffic is readable and easy to analyze in Wireshark.
- Analyzing HTTP helps detect:
 - Sensitive data exposure in URLs or headers
 - Malware beaconing to C2 servers
 - Suspicious file downloads or unauthorized access

