# LetsDefend — SOC Fundamentals (Hands-On Summary)

This document summarizes the practical components of the SOC Fundamentals module on LetsDefend.  
While the course includes theoretical content, these entries focus exclusively on the *hands-on investigative actions* performed using SIEM, Log Management, and EDR tooling.

---

## 1. SIEM (Monitoring) — Alert Ownership & Initial Triage

### Actions Performed
- Took ownership of an alert in the **Monitoring** page and moved it into the **Investigation** channel.
- Viewed alert metadata (source IP, hostname, event type).
- Located **closed alerts** using the appropriate SIEM tab to understand the workflow end-to-end.

### Key Skills Demonstrated
- Ability to distinguish between benign and suspicious activity.
- Familiarity with alert lifecycle inside a SOC.
- Understanding of what information SIEM provides vs. what must be pulled from other tools.

---

## 2. Log Management — Searching for IOCs and Event Context

### Actions Performed
- Queried logs for:
  - Specific **source IP addresses** (e.g., `8.8.8.8`)
  - Specific **URLs** (e.g., `https://github.com/apache/flink/compare`)
  - **Port + IP combinations** (e.g., destination port `52567`)

### Observations
- Learned how proxy, firewall, and exchange logs differ in format and visibility.
- Saw how log data reveals lateral movement or multi-host impact.

### Key Skills Demonstrated
- IOC scoping (“Is this event isolated or widespread?”)
- Ability to pivot through log sources efficiently.
- Recognition of normal vs. suspicious traffic patterns.

---

## 3. Endpoint Detection & Response (EDR)

### Actions Performed
- Searched EDR for the hash:  
  `83e0cfc95de1153d405e839e53d408f5`  
  → Identified the **host where the file executed**.
- Retrieved the executed command line for a suspicious script:  
  **Host:** Roberto  
  **File:** Ps1.hta  
  **Action:** Viewed the CMD command used to launch it.
- Explored endpoint metadata: running processes, network connections, browser history.

### Key Skills Demonstrated
- Host-based investigation fundamentals.
- Mapping file execution → process → network behavior.
- Recognizing when containment would be appropriate.

---

## 4. How This Supports SOC & Cloud Security Roles

- Reinforces ability to triage alerts under incomplete information.
- Builds the skill of pivoting between SIEM → Logs → EDR, which is core to incident response.
- Provides early exposure to tools that mirror real SOC stacks (Splunk, QRadar, SentinelOne, Carbon Black).
- Strengthens investigative reasoning rather than button-clicking.

This file reflects my practical SOC development, not theoretical study.
