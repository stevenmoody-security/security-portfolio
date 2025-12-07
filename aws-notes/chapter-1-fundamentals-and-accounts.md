# Chapter 1: Fundamentals and Accounts

This chapter covers foundational AWS concepts around accounts, MFA, and core IAM building blocks. These are critical for identity security, least-privilege design, and understanding how authentication and authorization work at every layer of AWS.

---

## 1. AWS Accounts — The Basics

- An AWS account is the primary security boundary; resources are isolated unless explicitly shared.
- Root user should never be used for daily operations — only for rare account-level tasks.
- Real-world best practice: Organizations always use **multiple accounts** for isolation, billing, and blast-radius reduction.
- Exam tip: Root user → cannot be restricted by IAM policies; only MFA + no access keys.

---

## 2. Multi-Factor Authentication (MFA)

- Adds a second authentication factor on top of username/password — mitigates credential theft.
- Should be enabled on **root user** and all privileged IAM users.
- Common MFA types: virtual TOTP apps, FIDO2 hardware keys, and SMS (least secure).
- IAM role sessions can enforce MFA via conditional policies (`"aws:MultiFactorAuthPresent": true`).
- Exam tip: If the question mentions "enhance login security," the answer is usually MFA.

---

## 3. Identity and Access Management (IAM) Basics

- IAM is AWS’s identity system controlling who can access which resources.
- Primary components: **users, groups, roles, policies**.
- Authorization is handled through **JSON IAM policies** evaluated using “deny overrides allow.”
- IAM is global — no regional variation.
- Exam tip: Policies do not grant permissions unless the identity exists AND authentication succeeds.

---

## 4. IAM Access Keys

- Used for programmatic access: CLI, SDKs, API calls.
- Access keys should not be used for long-lived workloads — roles are preferred.
- Root user access keys should never exist; if found, you must delete them.
- Rotate access keys regularly; use AWS Secrets Manager for secure storage.
- Exam tip: If credentials are leaked or compromised → immediately **deactivate** or **delete** access keys and move workload to IAM roles.

