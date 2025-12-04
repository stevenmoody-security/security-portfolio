# Investigation: EC2 Instance Status Check Failure (app-01)

## Scenario

An EC2 instance (`app-01`) was healthy for weeks. Suddenly, it began failing **Instance Status Checks** while **System Status Checks remained OK**. The instance is behind an ALB, and ALB health checks also started failing at the same time. CPU, network, and disk CloudWatch metrics show no abnormal activity.

This investigation focuses on identifying likely causes and determining the appropriate next steps.

## 1. Plausible Causes

Because **System Status Checks are passing**, AWS hardware and network are healthy.  
This indicates an issue *inside the guest OS*:

- **OS crash or hang** (kernel panic or unresponsive operating system)
- **Boot failure** (failed update, incorrect kernel, or missing boot components)
- **Corrupted filesystem** preventing essential services from starting

## 2. First Investigation Step

Check **EC2 → Get System Log** to view boot messages, kernel panics, or filesystem errors.  
System Log provides immediate insight into OS-level failures even when SSH is unavailable.

## 3. Infra vs OS/Application vs Security

This is primarily an **OS/Application-level issue**, since AWS infrastructure is healthy (System Status Check OK). Instance Status Check failures usually indicate the OS is not responding properly due to crashes, boot issues, or internal networking failures. It could become a security issue only if logs later show unauthorized changes, tampering, or malware activity.

