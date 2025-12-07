# Cyber Kill Chain – SOC Analyst Perspective

## 1. Why the Kill Chain Matters for a SOC

The Cyber Kill Chain is not just an academic model; it’s a way to structure how you **think about attacks over time**. For a SOC analyst, it provides:

- A mental checklist of “what comes before and after this alert.”
- A way to prioritize which alerts are closer to **actions on objectives**.
- A shared language with incident responders, threat hunters, and management.

Instead of treating alerts as isolated events, the Kill Chain forces you to place them on a **timeline of an attack**.

---

## 2. Stages and Example Signals

Below is a SOC-focused view of the classic Lockheed Martin Cyber Kill Chain:

1. **Reconnaissance**  
   - Attacker gathers information about targets.  
   - SOC signals: unusual scanning against external perimeter, mass 404/401 patterns, threat intel hits for enumeration tools.

2. **Weaponization**  
   - Attacker builds malicious payloads (e.g., exploit + malware).  
   - SOC signals: often minimal direct visibility; seen indirectly via malware families, builder artifacts, and TTPs in intel feeds.

3. **Delivery**  
   - Payload is delivered (phishing email, web exploit, USB, etc.).  
   - SOC signals: email security logs, web proxy logs, endpoint alerts about dropped files.

4. **Exploitation**  
   - Vulnerability is exploited, code executes.  
   - SOC signals: exploit signatures, unusual process starts, privilege escalation attempts, suspicious command lines.

5. **Installation**  
   - Malware or backdoor is installed to maintain persistence.  
   - SOC signals: new services, scheduled tasks, autoruns, registry modifications, persistence mechanisms on endpoints.

6. **Command & Control (C2)**  
   - Compromised host establishes communication with attacker infrastructure.  
   - SOC signals: outbound connections to unusual domains/IPs, beaconing patterns, DNS anomalies, proxy logs, EDR network telemetry.

7. **Actions on Objectives**  
   - Attacker executes their goal: data theft, encryption (ransomware), lateral movement, destruction.  
   - SOC signals: bulk data transfers, unusual authentication patterns, mass file changes, privilege use anomalies, exfiltration to cloud or external endpoints.

---

## 3. How a SOC Uses the Kill Chain in Practice

From a SOC analyst’s perspective, the Kill Chain helps with:

- **Prioritization**  
  - Alerts at **C2 or Actions on Objectives** are more immediately dangerous than early recon noise.
  - A low-fidelity recon signal might be low priority on its own, but if tied to active C2, it becomes part of a bigger story.

- **Investigation Flow**  
  - When you see one stage, you deliberately ask:
    - “What evidence is there of the **previous stages**?”  
    - “What evidence is there of **later stages**?”  
  - Example: If you see suspicious C2 traffic, you go backwards (delivery/exploitation) and forwards (data access, exfiltration).

- **Coverage Gaps**  
  - If you rarely see alerts corresponding to, say, **exfiltration**, that might be a **visibility gap** (no DLP, poor proxy logging, no S3 access logging).

---

## 4. Practical Takeaways for Me as a Junior Analyst

- I should never treat alerts as isolated; I should always ask:  
  > “Where does this sit on the Kill Chain, and what are the stages before/after it?”
- For each environment, I want to know:
  - Which tools/logs cover each stage (recon, delivery, C2, exfiltration, etc.).  
  - Where the blind spots are (no logs, no sensors, no detections).
- When documenting incidents, I can structure the narrative using the Kill Chain:
  - “Recon → Delivery → Exploitation → C2 → Data exfiltration attempt blocked at X.”

This makes my analysis more structured, easier to brief, and closer to how mature SOCs think about intrusions.

