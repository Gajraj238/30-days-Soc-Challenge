# **Day 20 - Threat Intelligence Basics**


## Objective:
The goal of today’s lab is to understand the fundamentals of **Threat Intelligence (TI)** and how it supports SOC operations. Through real-world scenarios and hands-on enrichment of suspicious indicators (IP, domain, and file hash), I practiced how to gather threat context using platforms like **VirusTotal, URLScan, AbuseIPDB, and AlienVault OTX**. This exercise builds core skills for identifying, validating, and acting on IOCs—critical for alert triage, threat hunting, and incident response in a modern SOC environment.


##  **What is Threat Intelligence?**

**Threat Intelligence (TI)** is information about threats, threat actors, and their tactics. It helps SOC analysts investigate alerts faster, make informed decisions, and respond to incidents more effectively.


##  Three Types of Threat Intelligence

| Type            | Description                                                                                    | Example                                                          |
| --------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| **Strategic**   | High-level trends, attacker motivations, risk assessments                                      | “APT groups are targeting healthcare sector in 2025”             |
| **Operational** | Information about specific threat actors, campaigns, or TTPs (tactics, techniques, procedures) | “Lazarus Group uses spear-phishing with macro-enabled documents” |
| **Tactical**    | Technical indicators used in detection (IOCs: IPs, URLs, hashes, etc.)                         | `malicious.com`, SHA256 of a known backdoor, C2 IPs              |

---

##  Why Is Threat Intelligence Important in a SOC?

| Why It Matters                     | SOC Benefit                                                             |
| ---------------------------------- | ----------------------------------------------------------------------- |
|  Enriches alerts                 | Helps analysts understand **whether an alert is relevant or dangerous** |
|  Speeds up investigations        | Matches IPs, domains, hashes to known threats instantly                 |
|  Helps threat hunting            | Tells analysts **what to look for** in logs and telemetry               |
|  Blocks known threats proactively | Feeds into firewalls, email gateways, EDR, SIEM                         |
|  Informs response                | Tells the SOC what the attacker might do next (based on past campaigns) |

---

##  SOC Use Cases for Threat Intelligence

---

### 1. **IOC Enrichment in Alerts**

**Scenario:** Splunk triggers an alert for an outbound connection to `185.203.119.8`.

**SOC Action:**

* Enrich the IP with threat intel (VirusTotal, Anomali, MISP, ThreatConnect)
* Discover it’s a known C2 server for RedLine Stealer
* Escalate the alert and isolate the endpoint

---

### 2. **Threat Hunting with TI**

**Scenario:** Cyber threat intelligence reports a new phishing campaign using URLs with `login-microsoft365-update.com`.

**SOC Action:**

* Search across email logs and proxy logs in Splunk:

```spl
index=email_logs OR index=proxy url="*login-microsoft365-update.com*"
```

* Find impacted users
* Contain and educate

---

### 3. **Incident Response Support**

**Scenario:** A system shows signs of compromise.

**SOC Action:**

* Extract hashes of suspicious files and cross-reference with TI
* Determine malware family, related IOCs, and lateral movement tactics
* Prepare a response plan with informed knowledge

---

### 4. **Automated IOC Blocking**

**Scenario:** TI platform ingests fresh IOC feeds (e.g., from Abuse.ch, AlienVault OTX, MISP).

**SOC Action:**

* Auto-feed these into:

  * SIEM watchlists
  * Firewall/IDS rules
  * Email gateway blocklists
* Block bad IPs, domains, or file hashes before they reach users

---

### 5. **Campaign Tracking**

**Scenario:** TI reveals a nation-state group is targeting your industry.

**SOC Action:**

* Use reported TTPs (MITRE ATT\&CK) to build detection logic
* Search for:

  * Specific PowerShell patterns
  * Named malware
  * Infrastructure overlap (same C2 servers used)

---

##  Threat Intelligence Sources SOCs Use

| Source Type               | Examples                                                      |
| ------------------------- | ------------------------------------------------------------- |
| **Public Feeds**          | Abuse.ch, OTX, Feodo Tracker, Cisco Talos                     |
| **Commercial Feeds**      | Recorded Future, Mandiant, ThreatConnect, Anomali             |
| **ISACs**                 | Industry-specific threat sharing (e.g., FS-ISAC for finance)  |
| **Government Sharing**    | CISA, US-CERT, ENISA                                          |
| **Internal Threat Intel** | Derived from past incidents, honeypots, and malware sandboxes |

---

##  Integration Points in the SOC

| Where TI Is Used                   | Role                                                     |
| ---------------------------------- | -------------------------------------------------------- |
|  SIEM (Splunk, Sentinel, QRadar) | IOC correlation, alert enrichment                        |
|  SOAR (XSOAR, Phantom)           | Automated enrichment & blocking                          |
|  Firewalls / NIDS                | Blocking bad IPs or domains                              |
|  Email Gateways                  | URL and attachment reputation checks                     |
|  EDR (CrowdStrike, Defender)  | Hash and behavior-based detection                        |
|  Threat Hunting                  | Build hypothesis based on latest campaigns or actor TTPs |

---


##  **Lab Setup**

-  **Download Email Sample:** [sample-1.eml](sandbox:/mnt/data/sample-1.eml)  
-  **Tools You Will Use:**
  - [VirusTotal](https://www.virustotal.com)
  - [AbuseIPDB](https://abuseipdb.com)
  - [URLScan.io](https://urlscan.io)
  - [AlienVault OTX](https://otx.alienvault.com/)
  - [ThreatFox](https://threatfox.abuse.ch/)
  - [MXToolbox Header Analyzer](https://mxtoolbox.com/EmailHeaders.aspx)


##  **Scenario:**

While triaging a phishing alert, you discovered three suspicious indicators:

- IP Address: 18.188.148.80
- Domain: aaronthompson.ug
- File Hash(SHA256): d45a079c59c2860f9cf4578a8fc9f5fe8009cff8aaa83c572474d6bfe15ba95b


### Key Findings Report

#### **Suspicious Indicators Identified:**

| Type       | Value                                                              | Detection Summary                              | Threat Label / Vendors Flagged                                                                      |
| ---------- | ------------------------------------------------------------------ | ---------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **IP**     | `18.188.148.80`                                                    | 1/94 security vendors flagged it as malicious  | Hosted on AWS (AS16509), flagged by 1 vendor                                                        |
| **Domain** | `aaronthompson.ug`                                                 | 7/94 security vendors flagged it as malicious  | Flagged as **Malicious**/**Malware** by: alphaMountain, CRDF, BitDefender, CyRadar, SOC Radar, etc. |
| **File**   | `d45a079c59c2860f9cf4578a8fc9f5fe8009cff8aaa83c572474d6bfe15ba95b` | 40/63 security vendors flagged it as malicious | Detected as **Trojan** (family: badjoke/abobus) by multiple AVs                                     |

---

###  **Summary of Findings**

* The domain **`aaronthompson.ug`** is considered **high-risk**, flagged as **malicious or malware-related** by 7 major vendors.
* The IP **`18.188.148.80`** is **hosted in AWS** and flagged by only 1 vendor, indicating **low-confidence** suspicion—could be compromised or shared hosting.
* The file (SHA256 hash) is highly suspicious: **flagged by 40 out of 63** vendors, including detections as **Trojan.BadJoke / Abobus / Agent / Win32.Generic**, etc.

---

###  **Conclusion (Recommended Action)**

* **Block domain**: `aaronthompson.ug` (confirmed malicious).
* **Isolate IP**: Monitor or block `18.188.148.80` depending on internal use/visibility.
* **Quarantine file**: If found on endpoints, treat as **highly malicious**, initiate IR process.
* **Check user/device communication** with these indicators in internal logs (SIEM, proxy, DNS, EDR).




