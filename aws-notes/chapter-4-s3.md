# Chapter 4: Amazon Simple Storage Service (S3)

This chapter covers the security model, performance characteristics, storage classes, encryption mechanics, and advanced S3 features. S3 is foundational for the SAA exam and appears in nearly every real-world AWS architecture.

---

## 27. S3 Security

- Access is controlled by **bucket policies**, **IAM policies**, **ACLs** (legacy), and **Block Public Access** settings.
- Block Public Access overrides all other permissions and prevents accidental exposure.
- S3 is a **global service**, but bucket contents reside in a specific region.
- Access Points can simplify security design for large multi-team environments.
- Exam angle: If a bucket suddenly becomes public, check **Block Public Access** first.

---

## 28. S3 Static Hosting

- Allows an S3 bucket to serve static websites using **index** and **error** documents.
- Requires public access for objects (or use CloudFront with Origin Access Control).
- Does not support HTTPS directly on bucket endpoints; CloudFront is required for HTTPS + custom domains.
- No server-side processing — static hosting only.
- Exam tip: S3 + CloudFront is the correct architecture for secure static websites.

---

## 29. S3 Performance Optimization

- S3 automatically scales for throughput and request rate; no need for manual sharding.
- Multipart upload recommended for files > 100MB; required for > 5GB.
- **Byte-range requests** allow parallel downloads of large objects.
- Prefix-based performance limits are outdated — S3 now has unlimited horizontal scaling.
- Exam tip: High throughput workloads → multipart uploads + parallelization.

---

## 30. Key Management Service (KMS)

- AWS KMS manages encryption keys used in S3 SSE-KMS operations.
- Keys have policies that determine who can use and administer them.
- KMS usage generates CloudTrail logs for auditing (Encrypt/Decrypt calls).
- Can enforce encryption with `"s3:x-amz-server-side-encryption"` conditions in bucket policies.
- Exam angle: SSE-KMS provides additional auditability compared to SSE-S3.

---

## 31. S3 Object Encryption — CSE vs SSE

- **SSE-S3**: S3 manages the keys; easiest/lowest overhead.  
- **SSE-KMS**: AWS KMS manages keys; offers fine-grained auditing and key policies.  
- **SSE-C**: Customer provides encryption keys with each request; AWS never stores them.  
- **CSE (Client-Side Encryption)**: Encryption done before sending objects to S3.  
- Exam tip: If an auditor requires “customer-controlled keys,” choose **SSE-KMS with CMKs** or **CSE**.

---

## 32. S3 Bucket Keys

- Reduce the volume of KMS requests for SSE-KMS encrypted objects.
- Provide cost savings and improved performance at scale.
- Bucket keys introduce a hierarchical key structure for large buckets.
- Seamless to applications — no code changes required.
- Exam angle: Mentioned when dealing with “high request rate” and “KMS cost concerns.”

---

## 33. S3 Object Storage Classes — PART 1

- **Standard**: General-purpose, highest durability and availability.  
- **Standard-IA**: Lower-cost for infrequently accessed data; retrieval fees apply.  
- **One Zone-IA**: Stores in one AZ only; cheaper, but no multi-AZ durability.  
- Designed for balancing retrieval frequency vs cost.
- Exam tip: One Zone-IA = cost-sensitive but non-critical data.

---

## 34. S3 Object Storage Classes — PART 2

- **Glacier Instant Retrieval**: Millisecond retrieval; cheaper than IA.  
- **Glacier Flexible Retrieval**: Minutes to hours; suitable for backups.  
- **Glacier Deep Archive**: Retrieval in hours; lowest-cost archival.  
- Lifecycle rules can automate transitions.
- Exam angle: “Retain 7 years but rarely accessed” → **Deep Archive**.

---

## 35. S3 Lifecycle Configuration

- Automates transition of objects between storage classes.  
- Policy components: **transition rules** and **expiration rules**.  
- Can move objects to IA, One Zone-IA, Glacier tiers.  
- Can automatically delete old versions and expired objects.  
- Exam tip: Lifecycle rules run once per day — not real-time.

---

## 36. S3 Replication (CRR & SRR)

- **CRR (Cross-Region Replication)**: Copy objects to a different region.  
- **SRR (Same-Region Replication)**: Copy objects within the same region.  
- Requires versioning on both source and destination buckets.  
- Replication does not copy objects retroactively unless requested (replication batch).  
- Exam angle: Use CRR for compliance and DR; SRR for multi-account architectures.

---

## 37. S3 Pre-Signed URLs

- URLs that grant temporary, scoped access to private S3 objects.  
- Generated using IAM credentials or roles.  
- Ideal for secure downloads/uploads without exposing keys.  
- Expiration time typically varies from minutes to hours.  
- Exam tip: If requirements say “temporary secure access,” use pre-signed URLs.

---

## 38. S3 Select and Glacier Select

- Allows retrieving **specific portions** of an object using SQL-like queries.  
- Reduces data transfer and improves performance for analytics workloads.  
- Works with CSV, JSON, Parquet objects.  
- Glacier Select performs similar operations on archived data.  
- Exam angle: Used when processing large datasets but only a subset is needed.

---

## 39. S3 Events

- Trigger events when object-level actions occur (PUT, DELETE, REPLICATION).  
- Can invoke Lambda, SNS, SQS as event destinations.  
- Used for serverless pipelines, image processing, and ETL automation.  
- Event notifications are “at-least-once,” not exactly-once.  
- Exam tip: Replace SNS or Lambda triggers with S3 Event Notifications for automated workflows.

---

## 40. S3 Access Logs

- Capture access requests to S3 buckets for auditing.  
- Delivered to another S3 bucket (destination must not cause recursive logs).  
- Commonly used for security reviews and usage analytics.  
- Different from CloudTrail logs — Access Logs are at the **request level**.  
- Exam angle: If you need to analyze **who accessed which object**, use access logs.

---

## 41. S3 Object Lock

- Provides **WORM (Write Once Read Many)** protection.  
- Prevents objects from being modified or deleted during retention period.  
- Two retention modes: **Governance** (limited bypass) and **Compliance** (no bypass).  
- Used for legal holds, auditing, and immutable backups.  
- Exam tip: Backup vendors commonly integrate with Object Lock for ransomware protection.

---

## 42. S3 Access Points

- Simplify account-wide S3 permissions by assigning **unique hostnames** and **policies** per access point.  
- Reduce complexity in multi-team data lake environments.  
- Can restrict access by VPC or IAM conditions.  
- Support S3 on Outposts for hybrid architectures.  
- Exam angle: If bucket policy becomes unmanageable for many applications, use **Access Points**.

