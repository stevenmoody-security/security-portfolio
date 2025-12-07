# AMU — Encryption for Confidentiality & Integrity Lab

This lab focused on applying encryption mechanisms to protect data and evaluating their operational impact.

---

## 1. Lab Objectives
- Encrypt data at rest using lab-provided tools.  
- Understand how encryption affects usability and performance.  
- Work with basic key management workflows.  
- Differentiate between confidentiality and integrity mechanisms.

---

## 2. Actions Performed

### Practical Encryption Steps
- Encrypted sample files or volumes.  
- Accessed encrypted data to validate functionality.  
- Observed resource impact during reads/writes.

### Key Handling
- Created and managed encryption keys within the lab environment.
- Saw firsthand how losing or mismanaging keys produces irreversible data loss.
- Compared strong vs. weak key storage approaches.

---

## 3. Insights Gained

- Encryption overhead exists, but is manageable when scoped correctly.
- Key lifecycle (creation, rotation, backup, destruction) matters more than the encrypt/decrypt operations.
- Encryption ≠ integrity:
  - Confidentiality → encryption  
  - Integrity → hashing, signatures, MACs  

These concepts directly support AWS KMS, IAM, and SOC encryption-related investigations.
