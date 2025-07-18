# **Day 18 - Introduction to Phishing Analysis**

---

##  Objective
Today’s goal is to understand the fundamentals of phishing analysis by examining real-world techniques, attack types, and indicators through structured investigation steps. This includes analyzing email headers, domains, and attachments using open-source tools to identify threats like spoofing, malicious links, and lookalike domains—critical skills for SOC analysts detecting and responding to phishing campaigns.


---

##  **What is Phishing Analysis?**

**Phishing Analysis** is the investigation of suspicious emails to determine if they’re crafted to trick users into revealing sensitive information or executing malware. This involves analyzing metadata, links, files, and content for signs of deception or malicious behavior.

---

##  **Types of Phishing Attacks**

| **Type**           | **Description**                                                        |
|--------------------|------------------------------------------------------------------------|
| **Email Phishing** | Generic emails pretending to be from trusted brands (e.g., Microsoft)  |
| **Spear Phishing** | Targeted phishing emails crafted for a specific person or department   |
| **Whaling**        | Spear phishing aimed at executives (CEO/CFO fraud)                     |
| **Smishing**       | Phishing via SMS messages with malicious links                         |
| **Vishing**        | Voice phishing calls pretending to be from IT/helpdesk                 |
| **Clone Phishing** | Re-sending a legitimate email with a malicious link or file replaced   |
| **Business Email Compromise (BEC)** | Impersonating senior staff to trick finance/HR teams into transferring money or data |

---

##  **Phishing Techniques with Examples**

| **Technique**                  | **Example**                                                                 |
|-------------------------------|------------------------------------------------------------------------------|
| **Display Name Spoofing**     | `From: Microsoft Support <helpdesk@micros0ft.com>`                          |
| **Lookalike Domains**         | `accounts@secure-paypai.com` instead of `paypal.com`                        |
| **URL Redirection**           | `http://bit.ly/securelogin` → redirects to phishing site                    |
| **HTML Form in Email**        | Fake login form embedded directly in the email body                         |
| **Macro-Based Document**      | "Open to enable content" launches PowerShell when macro is enabled          |
| **QR Code Phishing (Quishing)** | QR image in email leads to credential harvesting website                   |
| **Attachment-Only Phishing**  | `.zip` or `.docx` file with a malicious payload but no email text           |

---

##  **Common Tools Used in Phishing Analysis**

| Tool              | Purpose                                     |
|-------------------|---------------------------------------------|
| **Email Header Analyzer** | Decode sender metadata and trace origin        |
| **VirusTotal**     | Scan email links and attachments for malware |
| **Hybrid Analysis / Any.Run** | Sandbox for dynamic file and URL behavior |
| **OLETools**       | Analyze Office macros in `.doc/.xls` files  |
| **CyberChef**      | Decode base64, hex, and obfuscated strings  |
| **Base64 Decoder** | Decode encoded payloads in email or script  |
| **Whois/IP Lookup**| Check sender domain and IP reputation       |

---

##  **Phishing Analysis Process**

| **Step** | **Action**                                                                 |
|---------|------------------------------------------------------------------------------|
| **1. Collect Email Sample** | Export the email as `.eml` or `.msg` format.                     |
| **2. Analyze Email Headers** | Review sender IP, Return-Path, SPF/DKIM/DMARC status.             |
| **3. Examine Body Content** | Check tone, language, branding, and urgency.                      |
| **4. Hover Over URLs** | Reveal if the actual link mismatches the display text.           |
| **5. Scan URLs and Files** | Use VirusTotal or a sandbox to examine behavior.               |
| **6. Decode Obfuscated Data** | Use CyberChef to decode base64, hex, or encoded scripts.         |
| **7. Extract Indicators** | Note malicious domains, IPs, file hashes, and sender addresses. |
| **8. Respond and Report** | Block in firewall/EDR, raise ticket, and alert users.             |

---

##  **Suspicious Email Attachment Indicators**

| **Indicator**                         | **Example**                                  |
|--------------------------------------|----------------------------------------------|
| Executables disguised as documents   | `Invoice.pdf.exe`, `Resume.doc.scr`          |
| Encrypted ZIP files                  | Password-protected zip with `.exe` or `.js`  |
| Macro-enabled Office files           | `.docm`, `.xlsm` launching PowerShell scripts|
| Base64 or hex-encoded payloads       | Hidden in HTML or script content             |
| Unexpected themes (invoice, CV, notice)| Frequently used to grab user attention       |

---



## **Task: Investigating a Lookalike Domain Email**

### Scenario:
Security team received a suspicious email claiming to be from PayPal. The sender address is:  
`noreply@secure-paypai.com`



To present this task report effectively, you want it to be **clear, evidence-based, and structured like a professional security incident report**. Here's how you can organize and write it to highlight the threat posed by the lookalike domain `secure-paypai.com`.

---

##  **Task Report: Investigating a Lookalike Domain Email**

###  **Scenario**

The security team received a **suspicious email** claiming to originate from PayPal, using the following sender address:

```
noreply@secure-paypai.com
```

Due to the visual similarity of `secure-paypai.com` to the legitimate `paypal.com`, an investigation was initiated to determine if this is a **lookalike (typosquatted) domain** potentially used in a **phishing campaign**.

---

### **Key Findings**

#### 1. **Domain Analysis: `secure-paypai.com`**

| Test                    | Result                         | Evidence                                                     |
| ----------------------- | ------------------------------ | ------------------------------------------------------------ |
| **DNS Record**          | ❌ Not Found                    | MxToolbox shows "DNS Record not found"                       |
| **SPF Record**          | ❌ Missing                      | SPF check failed – no SPF record                             |
| **DMARC Record**        | ❌ Missing & Policy Not Enabled | "No DMARC record" and "Quarantine/Reject policy not enabled" |
| **Mail Server (MX)**    | ❌ No Mail Server               | No valid MX records detected                                 |
| **Web Server Response** | ❌ Unresolved                   | Host could not be resolved via HTTP                          |
| **Blacklist Check**     | ✅ Not blacklisted              | Domain is not on major blacklists (as of scan time)          |



### **Conclusion**: This domain is **non-functional** in a legitimate sense — it has no DNS, no email security records, and cannot be resolved. This **strongly indicates it is not used by any legitimate business**, and may be deliberately inactive to avoid detection.

