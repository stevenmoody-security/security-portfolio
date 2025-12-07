# AMU — pfSense Firewall Custom Rules Lab

This lab involved configuring and validating firewall rules on a pfSense appliance to enforce network security policies.

---

## 1. Lab Objectives
- Build and apply custom firewall rules.  
- Understand how **rule order** affects real traffic.  
- Test how “default deny” interacts with explicit allows/denies.  
- Troubleshoot connectivity failures based on misordered rules.

---

## 2. Actions Performed

### Firewall Rule Configuration
- Created granular allows and denies for specific TCP/UDP ports.
- Structured the rule set so that:
  - Critical denies appeared **early**,
  - Common allows were **above** uncommon exceptions,
  - The **default deny** rule remained at the bottom.

### Connectivity Validation
- Tested inbound and outbound traffic flows to ensure rules matched as expected.
- Observed which rule was hit by inspecting pfSense logs.

### Troubleshooting
- Identified where rule order caused unintended blocking.
- Corrected ordering to restore valid traffic while keeping security intact.

---

## 3. Key Takeaways for SOC & Cloud Work

- pfSense rule ordering is identical in principle to AWS Security Groups + NACLs logic.
- “First match wins” is a universal firewall concept.
- Misordered rules create silent outages — a common real-world failure.
- Understanding rule logging is essential for SOC triage (“Is the firewall blocking this?”).

This lab demonstrates operational understanding of network security controls used in every enterprise environment.
