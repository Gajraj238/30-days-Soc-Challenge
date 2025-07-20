# Day 19 - Phishing Analysis: Investigating a Phishing Email

## Objective:
Today’s goal is to strengthen phishing detection skills by analyzing a real-world phishing email claiming to be from a Brazilian bank. Through deep inspection of email headers, sender IP reputation, domain authenticity, and embedded URLs, this lab teaches how to extract IOCs, identify spoofed senders, evaluate SPF/DKIM results, and confirm phishing attempts. These are essential skills for SOC analysts to investigate reported phishing and protect users from credential theft or malware.


##  What is Phishing Email Analysis?

**Phishing email analysis** is the process of examining **suspicious or malicious emails** to determine:

* Whether it’s a phishing attempt
* How the phishing works (techniques used)
* What risks it poses (e.g., credential theft, malware)
* Who was affected or targeted
* What indicators of compromise (IOCs) are present

> It's like performing **digital forensics** on an email that tried to trick someone.

---

##  Why Is Phishing Email Analysis Important in a SOC?

| Why It Matters                 | SOC Benefit                                                                       |
| ------------------------------ | --------------------------------------------------------------------------------- |
|  Common Attack Vector          | Over 90% of breaches begin with phishing                                          |
|  Early Threat Detection        | Allows SOC to catch and contain attacks before they spread                        |
|  IOC Extraction                | Provides indicators (URLs, IPs, hashes) to block across systems                   |
|  Campaign Detection            | Helps identify if one user was targeted or if it’s a widespread phishing wave     |
|  Attachment/Sandbox Analysis   | Detect malware delivery via embedded scripts, macros, or links                    |
|  User Protection               | Prevents future attacks by warning affected users and updating awareness training |

---

##  Key Components of Phishing Email Analysis

As a SOC analyst, I’ll inspect:

| Component      | What I Look For                                                     |
| -------------- | ------------------------------------------------------------------- |
| `From:` field  | Is the sender real or spoofed?                                      |
| `Return-Path:` | Does it differ from the visible sender?                             |
| `Reply-To:`    | Redirecting responses to a different domain?                        |
| Subject line   | Urgency, fear, or reward bait                                       |
| Body content   | Social engineering tactics or fake branding                         |
| URLs           | Do they point to phishing sites? Use shortened or obfuscated links? |
| Attachments    | Suspicious file types (e.g., `.zip`, `.exe`, `.xlsm`)               |
| Headers        | SPF/DKIM/DMARC failures, forged domains, poor formatting            |
| Recipients     | Targeted individual vs. mass campaign?                              |
| Time sent      | Sent during off-hours? CEO impersonation at 3AM?                    |

---

## Tools Used by SOC for Phishing Email Analysis

| Tool                              | Use                                                                  |
| --------------------------------- | -------------------------------------------------------------------- |
| **SIEM (Splunk, Sentinel)**       | Search logs for similar emails, extract IOCs                         |
| **Email Gateway Logs**            | (Mimecast, Proofpoint, Defender) – See verdicts and delivery details |
| **Header Analyzer**               | Decode and trace real origin of email                                |
| **VirusTotal / Hybrid Analysis**  | Check links and attachments                                          |
| **Sandbox (Joe Sandbox, Cuckoo)** | Analyze behavior of attachments or phishing links                    |
| **PhishTool / Triage platforms**  | Structured workflows for phishing investigations                     |
| **Threat Intelligence Feeds**     | Correlate sender domains, IPs, URLs with known threats               |

---

##  SOC Use Cases for Phishing Email Analysis

---
1. Investigating a Reported Phishing Email
2. Detecting a Phishing Campaign
3. Phishing Used as Entry Point (Post-Incident Investigation)
4. Automated Phishing Playbooks (SOAR Integration)

##  Key Indicators in Phishing Emails (Quick Table)

| Indicator            | Example                                                 |
| -------------------- | ------------------------------------------------------- |
| Spoofed domain       | `micros0ft-login.com`                                   |
| Suspicious URL       | `http://secure-login[.]xyz/reset`                       |
| Obfuscated link      | `hxxp://bit[.]ly/secure-update`                         |
| Dangerous attachment | `Invoice_9934.xlsm` (with macro)                        |
| Header mismatch      | `From:` looks internal but `Return-Path:` is external   |
| Failed SPF/DKIM      | Email claims to be from trusted sender but fails checks |
| Unusual `Reply-To:`  | Points to attacker’s Gmail or private domain            |

---

##  **Lab Setup**
  
### Tools Recommended:
  - [MX Toolbox Email Header Analyzer](https://mxtoolbox.com/EmailHeaders.aspx)
  - [EML Analyzer](https://eml-analyzer.herokuapp.com/#/)
  - IP reputation check (e.g., VirusTotal, AbuseIPDB, Cisco Talos)
  - Whois lookup (e.g., whois.domaintools.com)
  - URL and Domain analysis (e.g., urlscan.io, VirusTotal)

---

##  **Lab Task: Analyze the Suspicious Email**

###  Scenario:  
You received an email from **BANCO DO BRADESCO LIVELO** claiming that your card has **92,990 points expiring today**, sent from `banco.bradesco@atendimento.com.br`. 

Investigate and answer the following:

---

###  **Questions:**

1. **What is the full email address of the sender?**  
2. **What domain is used to send this email?** (Check Return-Path or From)
3. **What is the sender’s IP address from the header?**  
4. **Is the sender IP blacklisted?** (Check using AbuseIPDB or VirusTotal – Answer Yes/No)  
5. **What is the result of SPF authentication?** (Pass / Fail / Neutral)  
6. **What is one suspicious URL or link found in the email body?**  



###  **Investigation Findings**

| **Question**                               | **Answer**                                                                                                              |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| **1. Full email address of the sender**    | `banco.bradesco@atendimento.com.br`                                                                                     |
| **2. Domain used to send this email**      | `atendimento.com.br` (as seen in the "From" and Return-Path headers)                                                    |
| **3. Sender’s IP address from the header** | `137.184.34.4`                                                                                                          |
| **4. Is the sender IP blacklisted?**       |  **No** – According to VirusTotal and AbuseIPDB: 0 detections, though AbuseIPDB shows **10 user reports**              |
| **5. SPF Authentication Result**           |  **Fail** – SPF record exists, but alignment fails (see MxToolbox header analysis)                                     |
| **6. Suspicious URL or link in the email** |  https://blog1seguimentmydomaine2bra.me/ |

---

###  **Analysis Summary**

* The sender domain (`atendimento.com.br`) is **not affiliated** with Bradesco.
* The IP address `137.184.34.4` belongs to **DigitalOcean**, often used for hosting malicious scripts or fake landing pages.
* SPF and DMARC authentication **failed**, increasing the likelihood this email was spoofed.
* The embedded phishing link (`secure-paypai.com`) resembles PayPal but is confirmed to have **no valid DNS records**, lacks email security configs, and is flagged as **malicious/phishing** by multiple vendors on VirusTotal.
* AbuseIPDB reports 10 prior abuse complaints for the sending IP, although confidence of abuse is rated low (0%).

---

### **Conclusion**

This email is a **phishing attempt**. It attempts to create urgency (expiring points), impersonates a Brazilian bank, and directs the user to a **fake https://blog1seguimentmydomaine2bra.me domain**. Technical indicators — failed SPF/DMARC, hosted from a cloud provider, and phishing URL — confirm **malicious intent**.


