# Day 16: Splunk Basics – HTTP Log Analysis

###  Objective:
Today’s goal is to analyze HTTP logs in Splunk by uploading Zeek-style JSON data and using SPL queries to detect suspicious web activity. This includes identifying high-traffic endpoints, server errors, scripted attacks, and large data transfers—equipping SOC analysts with essential skills to monitor HTTP traffic for signs of phishing, malware, C2 communication, and data exfiltration.



##  What is HTTP?

**HTTP (HyperText Transfer Protocol)** is the **foundation of web communication**. Every time a user visits a website, their browser makes HTTP requests to web servers — to fetch HTML, CSS, JavaScript, images, and more.

* **HTTP uses port 80**
* **HTTPS** (secure version) uses port 443

For a SOC analyst, HTTP traffic is a **goldmine** of information. Why?

> Because **attackers also use HTTP** — for phishing, malware delivery, C2 (command and control) communications, data exfiltration, and vulnerability scanning.

---

##  SOC Perspective: Why Monitor HTTP?

SOC analysts monitor HTTP traffic and logs to:

| Purpose                     | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
|  Detect intrusions        | Spot suspicious URLs, payloads, or behavior patterns       |
|  Find malware activity    | Identify downloads of malicious scripts or files           |
|  Track beaconing/C2       | Detect devices reaching out to attacker servers repeatedly |
|  Identify web attacks    | Watch for SQLi, XSS, LFI, RFI, etc. in URLs or payloads    |
|  Detect data leaks        | Catch exfiltration of data via GET/POST requests           |
|  Identify insider threats | See what employees are accessing or uploading externally   |

---

##  Sources of HTTP Logs

SOC teams rely on logs from various places:

| Source                                | Log Type                                |
| ------------------------------------- | --------------------------------------- |
| Web Server (Apache/Nginx/IIS)         | Access logs, error logs                 |
| Web Proxy (Squid, Blue Coat, Zscaler) | User browsing logs                      |
| Firewall/UTM                          | Allowed/blocked HTTP requests           |
| IDS/IPS (Snort, Suricata)             | HTTP rule alerts                        |
| Endpoint EDR                          | URL requests from the machine           |
| SIEM                                  | Normalized HTTP activity from all above |

---

##  Common HTTP Log Fields to Monitor

| Field                 | Why it Matters                                                     |
| --------------------- | ------------------------------------------------------------------ |
| `src_ip`              | Who made the request?                                              |
| `url` or `uri`        | Which site or path was accessed?                                   |
| `method`              | `GET`, `POST`, `PUT`, etc. — can hint at uploads                   |
| `status_code`         | 200 OK, 404 Not Found, 500 Server Error                            |
| `user_agent`          | What software is making the request? Legit browser or malware?     |
| `bytes_sent/received` | Size of request and response — look for large uploads or downloads |
| `referer`             | Page that led to this request — useful for tracing infection chain |

---



##  Lab Setup

-  **Splunk**: Already installed and accessible.
-  **Data Source**: JSON-formatted Zeek-style HTTP logs.
-  **Log File**: Download and upload to Splunk using the steps below.


---

##  Steps to Upload HTTP Log into Splunk

1. Go to Splunk Web → **Settings > Add Data**.
2. Choose **Upload** and select `synthetic_zeek_http.json`.
3. Set Source type: `json` or create a new one `zeek:http`.
4. Index: Choose `main` or create a new index like `http_lab`.
5. Finish the upload and confirm indexing.

---

##  Lab Tasks

Use SPL queries to complete the following analysis:

###  Task 1: Find the top 10 endpoints generating web traffic
```spl
index=http sourcetype="json"
| stats count by "id.orig_h"
| sort -count
| head 10
```

### Task 2: Count the number of server errors (5xx) observed
```spl
index=http sourcetype="json" status_code>=500 status_code<600
| stats count as server_errors
```
### Task 3: Identify User-Agents associated with possible scripted attacks
```spl
index=http sourcetype="json" user_agent IN ("sqlmap/1.5.1", "curl/7.68.0", "python-requests/2.25.1", "botnet-checker/1.0")
| stats count by user_agent
```
### Task 4: Find large file transfers (greater than 500 KB)
```spl
index=http sourcetype="json" resp_body_len>500000
| table ts "id.orig_h" "id.resp_h" uri resp_body_len
| sort -resp_body_len
```


