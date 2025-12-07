# Chapter 7: Containers & ECS / EKS

This chapter covers the fundamentals of containerized workloads, Amazon’s managed container services (ECS, ECR, EKS), and Kubernetes architecture. These concepts represent modern cloud-native design and appear frequently in SAA scenarios focused on portability, scalability, and operational efficiency.

---

## 74. Introduction to Containers

- Containers package application code and dependencies into portable, isolated units using OS-level virtualization — far lighter than VMs.  
- They rely on **container runtimes** (Docker, containerd) and do not include a full OS kernel.  
- Ideal for microservices, stateless workloads, and horizontally scalable architectures.  
- Images are stored in registries (Docker Hub, ECR) and used to launch containers anywhere the runtime exists.  
- Exam tip: “Fast deployment,” “immutable infrastructure,” or “consistent runtime environment” → containers.

---

## 75. Amazon ECS — Concepts

- ECS is a fully managed container orchestration service from AWS; runs containers using **EC2** or **Fargate**.  
- Core components:  
  - **Task Definition** (blueprint: image, CPU/mem, ports, env vars)  
  - **Task** (running instance of a task definition)  
  - **Service** (ensures desired count of tasks, integrates with ALB, handles restarts)  
- ECS integrates tightly with IAM, VPC networking, CloudWatch, and ECR.  
- Launch types:  
  - **EC2 Mode** → you manage hosts  
  - **Fargate Mode** → AWS manages hosts  
- Exam angle: Use Fargate when you want to avoid managing EC2 infrastructure.

---

## 76. ECS — Cluster Mode (EC2 Launch Type)

- ECS Cluster Mode uses EC2 instances as container hosts, each running the **ECS agent**.  
- You manage: instance provisioning, patching, scaling, AMIs, networking, SGs, and capacity.  
- ECS schedules tasks on hosts based on CPU, memory, and placement constraints.  
- Best for workloads that need custom AMIs, GPUs, or specialized networking/storage.  
- Exam tip: “Full control of underlying EC2 hosts” → ECS EC2; “serverless containers” → Fargate.

---

## 77. Elastic Container Registry (ECR)

- Fully managed OCI-compatible container registry for storing, scanning, and versioning container images.  
- Supports **private** and **public** registries.  
- Integrates with IAM for granular push/pull permissions.  
- Offers image vulnerability scanning (basic and Inspector-enhanced scanning).  
- Supports cross-account and cross-region replication — useful for CI/CD pipelines.  
- Exam angle: Use **ECR** when AWS-native CI/CD workflows require secure, integrated image storage.

---

## 78. Kubernetes 101

- Kubernetes is an open-source container orchestration system for deploying, scaling, and managing containerized applications.  
- Architecture:  
  - **Control Plane** → API server, etcd, scheduler, controller manager  
  - **Nodes** → kubelet, kube-proxy, container runtime  
- **Pods** are the smallest deployable units (usually 1 container = 1 pod).  
- **Services** provide stable networking; **Ingress** exposes HTTP/HTTPS externally.  
- Kubernetes favors **stateless**, horizontally scaled architecture.  
- Exam tip: Pods are ephemeral; persistent data uses **Persistent Volumes (PVs)**.

---

## 79. Elastic Kubernetes Service (EKS) 101

- EKS provides a **managed control plane** for Kubernetes — AWS runs the control plane; you manage worker nodes or use Fargate.  
- Multi-AZ, automatically patched, highly available API server.  
- Integrates with IAM for RBAC (`aws-auth ConfigMap`), ECR, ALBs, and VPC networking.  
- Best choice when teams want Kubernetes compatibility or multi-cloud alignment.  
- Exam angle: “Need Kubernetes without managing the control plane” → EKS.

