# Week 1 AWS Notes (Dec 1–Dec 7)

This file summarizes the AWS topics I covered during the week of Dec 1–Dec 7 as part of my AWS Certified Solutions Architect – Associate study path.

---

## 1. EBS Encryption

### What It Is
EBS encryption protects data at rest for EBS volumes using AWS KMS-managed keys. Encryption is handled transparently by AWS between the instance and the EBS storage layer.

### Key Behaviors
- Encrypts:
  - Data at rest on the EBS volume
  - Data in transit between EC2 and EBS
  - All data in snapshots
  - All volumes created from encrypted snapshots
- Uses **KMS CMKs** (AWS-managed or customer-managed).
- Encryption happens at the hypervisor/storage layer; the instance reads/writes plaintext.
- You cannot “unencrypt” an already-encrypted volume; you must create a new unencrypted volume if needed.

### Exam / Security Angles
- Copying snapshots lets you **change the KMS key**.
- Default EBS encryption can be enabled per account/region.
- Key management and access to snapshots are critical for confidentiality.

---

## 2. Network Interfaces, Instance IPs & DNS

### What It Is
Elastic Network Interfaces (ENIs) are virtual NICs for EC2 instances. They carry private IPs, security group attachments, and sometimes public IPs via Elastic IP.

### Key Behaviors
- **Primary ENI**:
  - Created with the instance.
  - Cannot be detached.
- **Secondary ENIs**:
  - Can be created, attached, and moved between instances in the same AZ.
- **Private IP**:
  - Sticks to the ENI’s lifecycle, not the instance.
- **Public IP vs Elastic IP**:
  - Auto-assigned public IP on default VPC is released when the instance stops.
  - Elastic IP remains attached until explicitly disassociated.

### Exam / Security Angles
- Moving an ENI between instances is a common pattern for **failover**.
- DNS names (e.g., `ip-10-0-1-12.ec2.internal`) resolve to private IPs inside the VPC.
- Security groups are attached at the ENI level.

---

## 3. Amazon Machine Images (AMI)

### What It Is
AMIs are templates used to launch EC2 instances with predefined OS, configuration, and software.

### Key Behaviors
- AMIs are **regional**; must be copied to use in other regions.
- Backed by:
  - **EBS snapshots** or
  - **Instance Store** (ephemeral)
- Types:
  - AWS-provided, Marketplace, Community, or custom AMIs.
- Custom AMIs are commonly used with Auto Scaling Groups for consistent fleet configuration.

### Exam / Security Angles
- Using hardened or golden AMIs is a key security and compliance pattern.
- AMI sharing across accounts can be restricted (e.g., for sensitive images).

---

## 4. EC2 Purchase Options – Part 1 (On-Demand & Reserved Instances)

### What It Is
Pricing models for EC2 capacity depending on commitment and flexibility.

### On-Demand
- No commitment.
- Pay per second/hour depending on instance family.
- Best for **spiky**, unpredictable workloads or development/testing.

### Reserved Instances (RIs)
- 1- or 3-year commitment for a specific instance family/region.
- Significant discount vs On-Demand (often cited up to ~72%).
- Variants:
  - Standard
  - Convertible
  - Zonal (with capacity reservation)

### Exam Angles
- “Steady-state, predictable usage” → **Reserved Instances** or **Savings Plans**.
- “Short-term, unpredictable” → **On-Demand**.

---

## 5. EC2 Purchase Options – Part 2 (Spot, Dedicated & Savings Plans)

### Spot Instances
- Spare capacity at steep discounts (up to ~90% vs On-Demand).
- Can be interrupted with a **2-minute warning**.
- Good for:
  - Stateless batch jobs
  - Big data
  - CI/CD
  - Fault-tolerant workloads

### Dedicated Hosts / Instances
- **Dedicated Hosts**:
  - Physical servers dedicated to a single customer.
  - Useful for license-bound workloads and compliance (e.g., BYOL).
- **Dedicated Instances**:
  - Hardware isolated at the host level, but without host-level control.

### Savings Plans
- Commit to a certain **$ per hour** spend for 1–3 years.
- More flexible than RIs:
  - **Compute Savings Plans** apply across instance families, regions, even some managed services.

### Exam Angles
- “Flexible, want discount without instance-family lock-in” → **Savings Plans**.
- “Need physical isolation / special licensing” → **Dedicated Hosts**.
- “Cost-optimize interruptible workloads” → **Spot**.

---

## 6. Reserved Instances – Remaining Concepts

### Types & Behavior
- **Standard RIs**:
  - Biggest discount.
  - Limited flexibility (cannot easily change instance family).
- **Convertible RIs**:
  - Lower discount than Standard.
  - Can switch instance families within certain rules.
- **Zonal RIs**:
  - Reserved at the Availability Zone level.
  - Provide **capacity reservation** plus discount.

### Exam Angles
- “Guarantee capacity in a specific AZ” → **Zonal RI**.
- “Want future flexibility on instance families” → **Convertible RI**.

---

## 7. Instance Status Checks & Auto Recovery

### What It Is
AWS runs two types of health checks on EC2 instances: **System** and **Instance** checks.

### Key Behaviors
- **System Status Check**:
  - Hardware, network, and hypervisor-level issues in AWS.
  - AWS can often recover by moving instance to healthy hardware.
- **Instance Status Check**:
  - OS-level problems (kernel panic, boot failure, exhausted file system, etc.).
  - Usually requires user action (reboot, fix config, restore from backup).

### Auto Recovery
- For some instance types, you can configure **Auto Recovery**:
  - Automatically recovers instance to new hardware on **system-check failure**.
  - Preserves instance ID, private IP, ENI, associations.

### Exam Angles
- System vs Instance Status is frequently tested.
- “Keep the same instance identity after hardware issue” → **Auto Recovery**.

---

## 8. Horizontal & Vertical Scaling

### What It Is
Two strategies for scaling compute capacity.

### Vertical Scaling
- Change the instance size (e.g., `t3.medium` → `t3.xlarge`).
- Quick but limited; introduces downtime for resizing.
- Single point of failure still exists.

### Horizontal Scaling
- Add/remove **more instances** behind a load balancer.
- Implemented via **Auto Scaling Groups**.
- Improves both scalability and resilience.

### Exam Angles
- Auto Scaling Groups = **horizontal only**.
- “Highly available, scalable web app” → horizontal scaling + multiple AZs.

---

## 9. Instance Metadata

### What It Is
A local HTTP endpoint on every EC2 instance (`http://169.254.169.254`) providing metadata and credentials for the instance’s IAM role.

### IMDSv1 vs IMDSv2
- **IMDSv1**:
  - Simple HTTP GET.
  - Vulnerable to SSRF-style attacks if applications make arbitrary HTTP calls.
- **IMDSv2**:
  - Requires a **session token** (PUT to fetch token, then GET with header).
  - Much harder to exploit via SSRF.

### Exam / Security Angles
- Enforce **IMDSv2** on security-sensitive instances.
- Do not expose instance metadata indirectly through unvalidated user input (`/fetch?url=` patterns).
- Instance credentials are **temporary, auto-rotated** by AWS.

---

## 10. Introduction to Containers

### What It Is
Containers package an application plus its dependencies into a portable image, running on top of a shared OS kernel.

### Key Points
- More lightweight than full VMs; multiple containers share the same OS.
- Built from **layers**, defined in a Dockerfile.
- Pulled from container registries (Docker Hub, ECR, etc.).
- Common pattern: **stateless microservices** running in containers behind a load balancer.

### Exam Angles
- Containers often appear with ECS, EKS, Fargate in exam scenarios.
- Benefit: portability, consistency, efficient resource usage.

---

## 11. ECS – Concepts

### What It Is
Amazon ECS (Elastic Container Service) is AWS’s container orchestration service.

### Key Components
- **Cluster**: logical grouping of container instances or Fargate tasks.
- **Task Definition**: blueprint for running containers (image, CPU, memory, ports, environment).
- **Task**: an instantiation of a task definition.
- **Service**: maintains desired task count and integrates with load balancers.

### Launch Types
- **EC2**:
  - You manage the EC2 instances acting as container hosts.
- **Fargate**:
  - AWS manages underlying compute; you define vCPU/memory and tasks.

### Exam Angles
- “No servers to manage, just run containers” → **Fargate**.
- “Need control over host OS, special agents, or licensing” → **ECS on EC2**.

---

## 12. ECS – Cluster Mode (EC2 Mode)

### What It Is
Cluster Mode refers to ECS using **EC2 instances as container hosts**.

### Key Behaviors
- ECS Agent runs on each EC2 host, registering it with the ECS control plane.
- You are responsible for:
  - AMI selection and hardening
  - Patching/maintenance
  - Auto Scaling of the EC2 capacity
- ECS scheduler places tasks on available hosts based on resource constraints.

### Exam Angles
- “Customer wants to re-use existing EC2 management & tooling” → ECS (EC2 mode).
- “Minimize operational overhead” → ECS (Fargate) or EKS with managed node groups/Fargate.

---

## 13. Elastic Container Registry (ECR)

### What It Is
Amazon ECR is a managed container image registry for storing, scanning, and pulling container images.

### Key Behaviors
- Supports **private** and **public** registries.
- Integrated with **IAM** for controlling who can push/pull images.
- Integrates with **Inspector** for image vulnerability scanning (basic vs enhanced).
- Emits:
  - Metrics to **CloudWatch**
  - Audit logs to **CloudTrail**
  - Events to **EventBridge**
- Supports **cross-region** and **cross-account** replication.

### Exam / Security Angles
- Lock down push access; enforce scanning for critical images.
- Common exam pattern: ECS/EKS pulling images from ECR with IAM role permissions.

---

## 14. Kubernetes 101

### What It Is
Kubernetes is an open-source container orchestration platform; EKS is AWS’s managed Kubernetes control plane.

### High-Level Architecture
- **Cluster**: group of nodes managed as a single logical system.
- **Control Plane**:
  - API Server
  - etcd (state store)
  - Scheduler
  - Controller Manager(s)
  - Optional Cloud Controller
- **Nodes**:
  - Run **kubelet**, **kube-proxy**, and container runtime (e.g., containerd).

### Workload Concepts
- **Pod**: smallest deployable unit; typically one container per pod.
- **Service**: stable abstraction over pods.
- **Ingress**: entry point for external HTTP/HTTPS traffic.
- **Job**: runs pods to completion for batch/one-off work.

### State
- Pods are **ephemeral**.
- Persistent state should use **Persistent Volumes (PVs)**.

---

## 15. Elastic Kubernetes Service (EKS) 101

### What It Is
Amazon EKS is a managed Kubernetes service where AWS operates the control plane and you run worker nodes (or use Fargate).

### Key Behaviors
- Multi-AZ, highly available control plane managed by AWS.
- Worker nodes:
  - EC2 instances you manage (patching, scaling), or
  - Fargate profiles for serverless pods.
- Integrations:
  - **IAM** for authentication
  - **ECR** for images
  - **VPC** networking
  - **Load Balancers** via ingress controllers (e.g., AWS Load Balancer Controller)

### Exam Angles
- “Customer wants upstream Kubernetes without managing control plane” → **EKS**.
- You still own the **worker node lifecycle** and cluster configuration unless you use Fargate.

---
