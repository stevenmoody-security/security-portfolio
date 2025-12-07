# Chapter 2: AWS Fundamentals

This chapter covers foundational cloud concepts: global infrastructure, core compute/storage/networking services, DNS, HA/FT/DR principles, and basic operational tooling. These topics appear everywhere in AWS architecture and are critical for later chapters.

---

## 5. AWS Public vs Private Services

- **Public services** (e.g., S3, DynamoDB) are accessed over the internet by default unless configured with VPC endpoints.  
- **Private services** (e.g., EC2, RDS instances) reside inside VPC networks and require routing, SG/NACL rules.  
- Many public services can be made private via **Interface or Gateway VPC Endpoints**.  
- Exam angle: S3 “private access” requires **S3 VPC Endpoints**, not placing S3 inside your VPC.  
- Principle: "Public" refers to the service endpoint, *not* resource visibility.

---

## 6. AWS Global Infrastructure

- Hierarchy: **Regions → Availability Zones (AZs) → Data Centers**.  
- Regions are isolated for sovereignty; AZs are isolated for fault tolerance.  
- Multi-AZ = high availability; Multi-Region = disaster recovery.  
- Services may be **global** (IAM, Route 53, CloudFront), **regional** (EC2, RDS), or **edge distributed** (CloudFront).  
- Exam tip: When you see resiliency across AZs, the correct architecture uses **ALB + Auto Scaling**.

---

## 7. AWS Default Virtual Private Cloud (VPC)

- Every new AWS account gets a **default VPC** with public subnets, route to IGW, and default security groups/NACLs.  
- Default VPC allows launching EC2 instances with immediate internet access.  
- Contains **1 subnet per AZ**, already configured as public.  
- Security group = stateful allow rules; NACL = stateless allow/deny rules.  
- Exam angle: If resources unexpectedly have internet access → likely running inside **default VPC** public subnet.

---

## 8. Elastic Compute Cloud (EC2) — Basics

- EC2 provides resizable compute capacity using VMs on AWS-managed hardware.  
- Instances are launched from **AMIs** and can attach ENIs, EBS volumes, and IAM roles.  
- Public IP assigned at launch unless using EIP.  
- Instance lifecycle: Pending → Running → Stopping/Stopped → Terminated.  
- Exam tip: For ephemeral workloads or horizontal scaling, choose **Auto Scaling Groups**, not manual EC2 sizing.

---

## 9. Simple Storage Service (S3) — Basics

- Object storage for unstructured data with **11 nines durability** (99.999999999%).  
- Stores objects in **buckets** which exist at the region level.  
- Global namespace: bucket names must be globally unique.  
- Strong read-after-write consistency for new objects.  
- Exam angle: S3 is **not a file system**; does not support POSIX locking or random writes.

---

## 10. CloudFormation (CFN) — Basics

- Infrastructure as Code (IaC) service for provisioning AWS resources via templates.  
- Supports **drift detection**, automatic rollback, and parameterization.  
- Reusable components via **nested stacks** or **modules**.  
- CloudFormation is regional, but templates can deploy resources across AZs.  
- Exam tip: If the problem mentions “automated repeatable environment setup,” the answer is CloudFormation.

---

## 11. CloudWatch (CW) — Basics

- CloudWatch is AWS’s monitoring and observability platform.  
- Provides **metrics**, **logs**, and **alarms** for EC2, Lambda, RDS, and custom apps.  
- CloudWatch Logs store log data; Logs Insights provides query engine.  
- CloudWatch Alarms can trigger **SNS notifications**, Auto Scaling actions, or custom workflows.  
- Exam angle: CPU, network, and disk metrics for EC2 come from CloudWatch; memory usage requires CloudWatch agent.

---

## 12. Shared Responsibility Model (SRM)

- AWS: security **of** the cloud (hardware, AZs, hypervisor, managed services).  
- Customer: security **in** the cloud (IAM, OS patches on EC2, S3 permissions, encryption settings).  
- Boundary shifts: RDS → AWS manages OS/patches; Lambda → AWS manages everything except code.  
- Exam angle: If the resource is a *managed service*, AWS handles underlying infrastructure security.  
- Core mindset: Misconfigurations are almost always the customer’s responsibility.

---

## 13. High Availability vs Fault Tolerance vs Disaster Recovery

- **High Availability (HA)**: redundant systems across AZs; minimizes downtime but may allow brief impact.  
- **Fault Tolerance (FT)**: system continues operating even if components fail (e.g., multi-node replication).  
- **Disaster Recovery (DR)**: ability to recover from region-level failure using backups or replication.  
- DR strategies: Backup/Restore → Pilot Light → Warm Standby → Multi-Region Active-Active.  
- Exam tip: “Zero downtime and no data loss” = Active-Active or synchronous replication.

---

## 14. Route 53 (R53) Fundamentals

- AWS’s highly available DNS service with 100% SLA.  
- Supports record types such as A, AAAA, CNAME, Alias, MX, TXT.  
- Alias records integrate directly with AWS services (ELB, S3 static hosting, CloudFront).  
- Routing policies: Simple, Weighted, Latency, Geolocation, Failover, Multi-Value Answer.  
- Exam angle: Alias records are free, compatible with zone apex, and preferred over CNAME for AWS targets.

---

## 15. DNS Record Types

- **A**: maps hostname → IPv4  
- **AAAA**: maps hostname → IPv6  
- **CNAME**: maps hostname → another hostname  
- **Alias**: AWS-specific pointer to resources like ALB, CloudFront, S3  
- **MX**: mail routing  
- **TXT**: arbitrary metadata (e.g., SPF, domain verification)  
- Exam tip: Zone apex (root domain) cannot use CNAME but can use Alias.

