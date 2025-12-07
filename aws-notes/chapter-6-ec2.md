# Chapter 6: Elastic Compute Cloud (EC2)

This chapter covers virtualization, architecture, instance families, storage layers, networking, scaling, purchase models, and metadata security. EC2 underpins a huge portion of AWS architectures and is essential for real-world cloud and security roles.

---

## 53. Virtualization 101

- EC2 uses a combination of **Xen**, **Nitro**, and hardware-assisted virtualization.  
- Nitro system offloads networking, storage, and hypervisor functions to dedicated hardware cards.  
- Nitro = near bare-metal performance + improved security isolation.  
- Enables features like ENA networking, NVMe EBS, and IO-optimized instances.  
- Exam tip: Nitro instances do **not** support paravirtual (PV) virtualization — only HVM.

---

## 54. EC2 Architecture and Resilience

- EC2 instances run in a single Availability Zone; resilience requires multi-AZ architecture.  
- Components include: **instance**, **AMI**, **EBS volumes**, **ENIs**, **IAM role**, **security groups**.  
- Underlying host failures trigger **EC2 Auto Recovery** or replacement within ASGs.  
- EBS volumes are replicated within an AZ for durability.  
- Exam angle: Single EC2 ≠ highly available. Must use **ASG + ALB**.

---

## 55. EC2 Instance Types — PART 1

- Instance families optimized for different workloads: **General (M), Compute (C), Memory (R), Storage (I/D), GPU (G/P)**.  
- Naming example: `m5.large` → family (m5), size (large), generation (5).  
- Choose instance types based on bottleneck (CPU, RAM, IO, GPU).  
- Newer generations (M5, C6g) offer better price/performance.  
- Exam tip: If workloads are CPU-bound → choose **C-class**; if memory-bound → **R-class**.

---

## 56. EC2 Instance Types — PART 2

- **Burstable (T-family)**: CPU credit system; good for periodic bursts.  
- **Optimized instances** include network-optimized, storage-optimized (NVMe SSD), HPC instances.  
- **Arm-based Graviton** instances provide up to 40% cost/performance improvement.  
- GPU and FPGA families support ML and specialized compute tasks.  
- Exam note: CPU credit exhaustion → sustained throttling.

---

## 57. Storage Refresher

- EC2 storage options include **EBS volumes**, **instance store volumes**, and **EFS**.  
- EBS = persistent block storage, network-attached; survives stops/starts.  
- Instance Store = physically attached NVMe/SATA; ephemeral; disappears on stop/terminate.  
- EFS = regional NFS file system; shared storage across instances.  
- Exam tip: If data must persist, do **not** use instance store.

---

## 58. Elastic Block Store (EBS) — Service Architecture

- Block-level storage replicated within an AZ for durability.  
- Supports snapshots to S3-based internal storage.  
- Volumes attach over NVMe or network using Nitro hardware acceleration.  
- Multi-AZ replication is not automatic (except Multi-Attach EBS or specific enterprise services).  
- Exam angle: EBS volumes are AZ-scoped; snapshots are **not** tied to AZs.

---

## 59. EBS Volume Types — General Purpose

- **gp3**: baseline 3,000 IOPS & 125 MB/s throughput, with provisionable IOPS/throughput.  
- **gp2**: performance tied to volume size (deprecated gradually).  
- Use cases: general workloads, boot volumes, app servers.  
- gp3 provides cost savings and decoupled performance.  
- Exam tip: Choose **gp3** unless a specific workload requires io2/io1.

---

## 60. EBS Volume Types — Provisioned IOPS

- **io2**: highest durability (99.999%), best for databases, sustained high IOPS.  
- **io1**: older version; supports Multi-Attach.  
- Provision IOPS when latency-sensitive workloads demand consistent performance.  
- Expensive but predictable.  
- Exam angle: RDS or high-write OLTP → **io2**.

---

## 61. EBS Volume Types — HDD-Based

- **st1 (Throughput-optimized HDD)**: big data, streaming, sequential workloads.  
- **sc1 (Cold HDD)**: infrequent access, archival data.  
- Cannot be used for boot volumes.  
- Optimized for throughput, not IOPS.  
- Exam tip: If workload needs *sequential* high-throughput reads → **st1**.

---

## 62. Instance Store Volumes — Architecture

- Ephemeral, physically attached NVMe/SATA SSDs on the host.  
- Extremely high IOPS and low latency.  
- Data lost on stop/terminate; survives restart (only reboot, not stop).  
- Great for caching, scratch data, ephemeral workloads.  
- Exam tip: Never store important data on instance store unless already backed up.

---

## 63. Choosing Between Instance Store and EBS

- Use **instance store** for performance and ephemeral compute.  
- Use **EBS** for durability, persistence, snapshots, and DR support.  
- EBS supports encryption, resizing, and migration between instances.  
- Some instance families (e.g., C6g) do not offer instance store.  
- Exam angle: “Data must persist” → EBS; “fast scratch space” → instance store.

---

## 64. Snapshots, Restore & Fast Snapshot Restore (FSR)

- Snapshots are stored in S3-managed storage and are incremental.  
- Used to create new volumes, AMIs, cross-region copies.  
- Fast Snapshot Restore (FSR) pre-warms snapshots → near-instant full performance.  
- FSR is configured per-AZ and incurs additional cost.  
- Exam tip: Without FSR, freshly restored volumes may have cold-start latency.

---

## 65. EBS Encryption

- Uses **AES-256** and AWS KMS (CMKs).  
- Encryption applies to data at rest, in transit between EC2 and EBS, and snapshots.  
- Snapshots of encrypted volumes remain encrypted.  
- Copying snapshots allows changing the encryption key.  
- Exam tip: You cannot *unencrypt* an encrypted volume.

---

## 66. Network Interfaces, Instance IPs, and DNS

- **Primary ENI**: created with the instance; cannot be detached.  
- **Secondary ENIs**: can be attached/moved between instances.  
- Private IP always remains with the ENI; public IP may change unless Elastic IP used.  
- Internal DNS resolves to private IP; external DNS resolves to public IP.  
- Exam angle: High-availability failover often uses **moving ENIs** between instances.

---

## 67. Amazon Machine Images (AMI)

- AMIs define the OS, root volume, apps, and configuration.  
- Custom AMIs allow consistent, fast deployments.  
- AMIs are region-scoped but can be copied cross-region.  
- Creating AMIs also creates root volume snapshots.  
- Exam tip: For scaling fleets, always use **launch templates** referencing AMIs.

---

## 68. EC2 Purchase Options — PART 1

- **On-Demand**: pay-as-you-go; highest flexibility; no commitment.  
- **Reserved Instances (RI)**: up to 72% discount for 1–3 year terms; predictable workloads.  
- **Convertible RI**: flexible between instance families; slightly lower discount.  
- Zonal RIs provide **capacity reservation**.  
- Exam angle: Steady state → RI; unpredictable/bursty → On-Demand.

---

## 69. EC2 Purchase Options — PART 2

- **Spot Instances**: up to 90% discount; interruptible; best for batch/CI/CD/analytics.  
- Spot interruption gives a **2-minute warning**.  
- **Savings Plans**: commit to hourly spend; flexible across instances, regions, compute types.  
- Compute Savings Plans apply to EC2, Lambda, Fargate.  
- Exam tip: If workloads can tolerate interruption → Spot.

---

## 70. Reserved Instances — The Rest

- Standard RIs: highest savings, lowest flexibility.  
- Convertible RIs: moderate savings, can change instance families.  
- Scheduled RIs: legacy, rarely used.  
- RIs reduce compute cost but do **not** auto-scale; ASG still needed.  
- Exam angle: Only **Zonal RIs guarantee capacity**.

---

## 71. Instance Status Checks & Auto Recovery

- **System Status Check Failure** → AWS hardware/host issue.  
- **Instance Status Check Failure** → OS/boot/kernel issue inside the instance.  
- Auto Recovery recreates the instance on new hardware without changing: Instance ID, private IP, EBS, ENIs.  
- Good resilience mechanism for single-instance deployments.  
- Exam tip: Auto Recovery ≠ Auto Scaling; different features entirely.

---

## 72. Horizontal & Vertical Scaling

- **Vertical scaling** = increasing instance size (bigger box).  
- **Horizontal scaling** = more instances (distributed load).  
- Auto Scaling Groups provide elasticity, health checks, and multi-AZ resilience.  
- ELB + ASG is the standard HA pattern for stateless applications.  
- Exam angle: AWS prefers horizontal scaling unless explicitly stated otherwise.

---

## 73. Instance Metadata

- Metadata endpoint: `http://169.254.169.254` → instance info + IAM role credentials.  
- IMDSv1: vulnerable to SSRF; tokenless requests allowed.  
- IMDSv2: requires session token, mitigates SSRF vectors.  
- Applications use metadata to fetch **temporary credentials** from STS.  
- Exam tip: “Prevent credential theft via SSRF” → enforce **IMDSv2 only**.

