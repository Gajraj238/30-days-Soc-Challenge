

# Day 17: Splunk Alerts: Detecting Threats 

## Objective 
The objective of this lab is to understand how to create and configure Splunk Alerts to detect potential security threats in real time. Using real-world examples, learners will build two practical alerts based on SSH brute-force attempts and geographical login anomalies using Linux SSH logs. 

##  **What is a Splunk Alert?**

A **Splunk Alert** is a saved search that automatically runs on a schedule and **triggers an action** when its conditions are met. Suppose I tell Splunk:

> “Watch for this behavior. If it happens, alert me immediately.”

Splunk can trigger actions such as:

* Sending an email
* Triggering a webhook
* Creating a ticket in SOAR/ITSM
* Running a custom script

In a SOC, alerts are used to monitor systems **proactively**, rather than reactively.

---

##  **Common SOC Use Cases for Alerts**

| Use Case                  | Description                                                                |
| ------------------------- | -------------------------------------------------------------------------- |
| SSH Brute-Force Detection | Detect attackers trying to guess passwords via repeated login attempts     |
| Geo-Anomaly Detection     | Alert when logins occur from unexpected countries or regions               |
| High-Privilege Logins     | Notify when domain admins log in outside of normal hours                   |
| Large File Transfers      | Alert on unusual data exfiltration over SCP/SFTP                           |
| New SSH Keys Added        | Detect when SSH keys are added to authorized\_keys (potential persistence) |

---

##  **Scenario: SSH Logins Are Being Monitored in Splunk**

### lets assume organization wants to detect brute-force attacks and unauthorized SSH logins. I have access to Splunk, which is already ingesting **Linux SSH logs** from `/var/log/auth.log`.
I will create two alerts:

* One for **SSH brute-force attacks**
* One for **SSH logins from countries other than India or the US**

---

##  **Lab Setup**

### Requirements:

* Splunk Enterprise or Splunk Cloud
* Access to **Search & Reporting**
* Logs from Linux systems containing SSH activity (auth.log or linux\_secure)


---

##  **Lab Task 1: Create SSH Brute Force Detection Alert**

### Objective

Detect when **10 or more failed SSH login attempts** occur from the same IP address within 5 minutes.

---

###  Step-by-Step Execution

#### 1. **Navigate to Search & Reporting**

* From the Splunk home screen, click on:

```
Apps > Search & Reporting
```

#### 2. **Run the Detection Search**

In the search bar:

```spl
index=auth sourcetype=json "Failed password"
| bin _time span=5m
| stats count by _time, src_ip
| where count >= 10
```
- Note - This query finds IPs that failed SSH login 10+ times within 5 minutes.

#### 3. **Save It as an Alert**

* Click on **Save As > Alert**
* Fill in:

  * **Title:** `SSH Brute Force Alert`
  * **Description:** `Detects repeated SSH login failures from a single IP`

#### 4. **Configure Trigger Settings**

* **Alert Type:** Scheduled
* **Time Range:** Last 15 minutes
* **Cron Schedule:** `*/5 * * * *` (every 5 minutes)
* **Trigger Condition:** If number of results > 0
* **Trigger Alert:** Once for each result

#### 5. **Set Alert Action**


```
 SSH Brute Force Detected from $result.src_ip$
```

#### 7. **Click Save**

- **Alert Created!** Splunk will now monitor for brute-force SSH login attempts.

---

##  **Lab Task 2: Create SSH Geo-Anomaly Alert (Login from Unusual Country)**

###  Objective

Detect successful SSH logins coming from **outside India or the US**.

---

#### 1. **Run the Detection Search**

```spl
index=linux sourcetype=linux_secure ("Accepted password" OR "Accepted publickey")
| iplocation src_ip
| search Country!="India" Country!="United States" Country!=""
| stats count by _time, user, src_ip, Country
```
- Note: This search finds successful logins from foreign countries.

#### 2. **Review the Results**

* I check if any users logged in from unexpected countries (e.g., China, Brazil, Russia).
* This may indicate a compromised account or VPN misuse.

#### 3. **Save the Alert**

* Click **Save As > Alert**
* Fill in:

  * **Title:** `Unusual Country SSH Login`
  * **Description:** `Triggers on SSH logins from outside trusted countries`

#### 4. **Set the Alert Conditions**

* **Type:** Scheduled
* **Time Range:** Last 15 minutes
* **Cron Schedule:** `*/15 * * * *` (every 15 minutes)
* **Trigger Condition:** If results > 0

#### 5. **Add Email Notification**

* Choose: **Send Email**
* To: `soc.team@example.com`
* Subject:

```spl
 SSH Login from Suspicious Country: $result.Country$
```

#### 6. **Click Save**

` **Alert Created!** Now I’ll be notified if logins happen from unusual places.



