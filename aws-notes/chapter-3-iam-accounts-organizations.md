# Chapter 3: IAM, Accounts, and AWS Organizations

This chapter covers identity design, permission boundaries, cross-account access, governance controls, and AWS-wide logging. These are foundational for enterprise security and appear frequently in real-world architecture and SAA exam scenarios.

---

## 16. IAM Identity Policies

- Identity policies attach to **users**, **groups**, or **roles** and define permissions using JSON documents.
- Evaluation is “**deny overrides allow**”: explicit deny beats all allows.
- Policies support resource-level permissions, condition keys, and wildcards.
- **Managed policies** (AWS or customer-managed) improve reusability; inline policies are tightly coupled to an identity.
- Exam angle: If multiple policies conflict, explicit **Deny** always wins.

---

## 17. IAM Users and ARNs

- IAM users represent long-lived **human identities** inside an AWS account.
- Each resource and identity in AWS has an **Amazon Resource Name (ARN)** for exact, unique identification.
- ARN structure: `arn:partition:service:region:account-id:resource`.
- Users authenticate with passwords (console) and access keys (programmatic).
- Exam tip: Cross-account access **never** uses IAM users — it uses **roles**.

---

## 18. IAM Groups

- Groups are containers for IAM users; used to apply permissions consistently.
- Policies attach to groups → permissions flow to members.
- Groups **cannot** be nested inside other groups (no hierarchy).
- Groups do not represent identities and cannot be assumed.
- Exam angle: Use groups for RBAC-like structures (admin, read-only, billing).

---

## 19. IAM Roles — The Tech

- Roles provide **temporary credentials** via STS and are meant for applications, services, or cross-account access.
- Roles are assumed, not logged into; no password or access keys by default.
- Trust policies define **who can assume a role**; permission policies define **what the role can do**.
- Used by EC2, Lambda, ECS, EKS, and many AWS services.
- Exam tip: “Application needs S3 access without storing credentials” → attach an **IAM role**.

---

## 20. When to Use IAM Roles

- For workloads running on AWS compute services (EC2, Lambda, ECS, etc.).
- For temporary, scoped, auditable permissions (least privilege).
- For cross-account access — role assumption is the recommended pattern.
- For replacing static access keys in automation and pipelines.
- Exam angle: If a question mentions “eliminate long-lived credentials,” the answer is almost always IAM roles.

---

## 21. Service-Linked Roles & PassRole

- **Service-linked roles** are built-in roles required by specific AWS services (e.g., Auto Scaling, Lambda).
- These roles are created and managed automatically; trust relationships are predefined.
- **PassRole** allows a user/service to delegate a role to another AWS service (e.g., “run this EC2 with this role”).
- PassRole is a sensitive permission — restrict it tightly.
- Exam tip: If an operation requires selecting a role (e.g., ECS task execution role), IAM permissions must include **iam:PassRole**.

---

## 22. AWS Organizations

- Supports **multi-account management**, consolidated billing, and central governance.
- Organizational structure: **Management Account → Organizational Units (OUs) → Member Accounts**.
- Enables service restrictions, consistent guardrails, and automated account provisioning.
- Provides consolidated billing: multiple accounts share savings (e.g., RIs, Savings Plans).
- Exam angle: Organizations is the correct answer for enterprise-scale multi-account governance.

---

## 23. Service Control Policies (SCPs)

- SCPs apply at the **OU or account level** and limit maximum available permissions.
- They do **not** grant permissions — they only filter what is *allowed to be allowed*.
- SCPs affect **all identities**, including root user.
- Common use cases: deny creation of IAM users, deny disabling CloudTrail, restrict region usage.
- Exam tip: “Even administrators cannot perform X” → SCP is blocking it.

---

## 24. CloudWatch Logs

- Centralized log storage solution for system, application, and AWS service logs.
- Supports **log groups**, **log streams**, retention policies, and metric filters.
- CloudWatch Logs Insights enables SQL-like querying for troubleshooting.
- Logs can come directly from services or via agents installed on EC2.
- Exam angle: Logs are **regional**, not global.

---

## 25. CloudTrail

- Records API events across AWS accounts and services; core audit and forensic tool.
- Critical for compliance: tracks “who did what, where, and when.”
- Supports multi-region trails, organization-wide trails, and S3 log delivery.
- Integrates with CloudWatch Events/EventBridge for alerting on specific API actions.
- Exam tip: If you need API-level visibility, CloudTrail — not CloudWatch — is the answer.

---

## 26. AWS Control Tower 101

- Automates creation and governance of multi-account AWS environments.
- Builds accounts with guardrails (SCPs), baseline security tools, and preconfigured logging.
- Uses AWS Organizations under the hood.
- Ideal for new enterprises starting AWS adoption.
- Exam tip: If the question says “set up secure multi-account environment quickly,” the answer is Control Tower.

