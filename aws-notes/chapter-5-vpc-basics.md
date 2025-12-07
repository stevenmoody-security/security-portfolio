# Chapter 5: Virtual Private Cloud (VPC) Basics

This chapter covers the core networking constructs used across nearly all AWS architectures. VPC design impacts security, routing, availability, and cost — making it one of the most heavily tested areas in the SAA exam and a core competency for cloud security roles.

---

## 43. VPC Sizing and Structure – PART 1

- A VPC is a logically isolated virtual network in AWS defined by a **CIDR block** (e.g., 10.0.0.0/16).  
- CIDR size determines the total available IP addresses for all subnets.  
- Reserved IPs: AWS reserves 5 addresses per subnet (network, broadcast, +3 internal).  
- Use **/16 or /20** ranges for most production VPCs to avoid IP exhaustion later.  
- Exam angle: VPCs cannot overlap in peering or Transit Gateway scenarios unless using specific advanced workarounds (rare).

---

## 44. VPC Sizing and Structure – PART 2

- Good practice: divide VPC IP space by **AZ and function** (public/private/database).  
- Subnets cannot span Availability Zones — each subnet lives in exactly one AZ.  
- Plan subnets generously; renumbering later is painful and disruptive.  
- Consider future components (EKS clusters, NAT Gateways, load balancers) when sizing.  
- Exam tip: If a resource needs *public* IP access, it must be in a **public subnet** with route to an IGW.

---

## 45. Custom VPCs — PART 1

- Custom VPC = full control over subnets, route tables, NACLs, SGs, and gateways.  
- Unlike default VPCs, custom VPCs start with **no internet routing** until explicitly added.  
- Configure routing manually: IGW for outbound internet, NAT for private outbound, VGW/TGW for hybrid.  
- Required foundational components: VPC → Subnet → Route Table → IGW/NAT/TGW → SG/NACL.  
- Exam angle: If an instance in a private subnet cannot reach the internet → missing NAT Gateway **or** missing route.

---

## 46. VPC Subnets

- Subnets are logical slices of the VPC’s CIDR block and are **AZ-scoped**.  
- **Public subnet**: route table includes a route to Internet Gateway (IGW).  
- **Private subnet**: no direct IGW route; internet access via NAT Gateway (if allowed).  
- Database subnet groups (RDS, Redshift) typically use **private** subnets only.  
- Exam tip: ELBs (ALB/NLB) can be public-facing or internal depending on subnet placement.

---

## 47. VPC Routing, Internet Gateway & Bastion Hosts

- **Route tables** determine traffic flow; each subnet must be explicitly associated with a route table.  
- **Internet Gateway (IGW)** enables internet access for public subnets.  
- **NAT Gateway** or NAT instance provides outbound internet for private subnets.  
- **Bastion hosts** provide secure SSH/RDP access to private subnets (typically via public subnet).  
- Exam angle: Route priority → most specific route wins.

---

## 48. Stateful vs Stateless Firewalls

- **Security Groups (SG)** are stateful: Return traffic is automatically allowed.  
- **Network ACLs (NACLs)** are stateless: Must allow both inbound and outbound traffic explicitly.  
- SGs apply at the **instance/ENI** level; NACLs apply at the **subnet** level.  
- SGs are preferred; NACLs are used mainly for coarse-grained blocking.  
- Exam tip: If response traffic is being blocked → suspect NACL.

---

## 49. Network Access Control Lists (NACLs)

- Stateless packet filtering applied to subnets.  
- Rules are evaluated in number order (lowest → highest) until a match.  
- Default NACL allows all inbound/outbound; custom NACL denies all until configured.  
- Common use case: block specific IP ranges at subnet level.  
- Exam tip: If you need to explicitly **deny** something, NACL is the correct answer.

---

## 50. Security Groups (SG)

- Virtual firewalls for instances/ENIs; stateful and simpler than NACLs.  
- Only allow rules; no explicit deny.  
- SGs reference **security group IDs**, enabling controlled instance-to-instance communication.  
- Default SG allows inbound from itself and outbound to anywhere.  
- Exam tip: For least privilege, restrict inbound SG rules to specific ports and SGs, not CIDR ranges.

---

## 51. Network Address Translation (NAT) & NAT Gateway — PART 1

- NAT Gateway provides outbound internet for private subnets while blocking inbound initiation.  
- Lives in a **public subnet** with route to IGW.  
- Private subnets point default routes (`0.0.0.0/0`) to the NAT Gateway.  
- Highly available **within** an AZ — for multi-AZ, deploy multiple NAT Gateways.  
- Exam angle: If instances in private subnet cannot download packages → missing NAT or route table entry.

---

## 52. NAT Gateway — PART 2

- NAT Gateways scale automatically and have predictable performance.  
- Cheaper alternatives: **NAT Instances** (obsolete except in special cases).  
- NAT cannot accept inbound connections; it only forwards outbound traffic from private subnets.  
- Data processing charges apply per GB through the NAT.  
- Exam tip: For cost optimization in large-scale architectures, consider VPC endpoints to avoid NAT data charges.

