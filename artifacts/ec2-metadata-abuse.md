# Detecting EC2 Metadata Credential Abuse

When a web application exposes a server-side fetch feature that accepts user-controlled URLs, an attacker can attempt SSRF by directing the backend to the EC2 metadata service at `169.254.169.254`. If the instance still supports IMDSv1, the attacker can retrieve temporary IAM credentials and use them to call AWS APIs with the instance’s privileges. 

A SOC analyst detects this by correlating metadata access logs, VPC flow logs, and CloudTrail activity—particularly unusual AssumeRole patterns, S3 access spikes, or API calls from unexpected services. The presence of metadata access alone is not a compromise; the key question is whether credentials were exfiltrated and then used. 

Hardening includes enforcing IMDSv2-only, restricting outbound requests from the application, validating URLs to prevent internal access, and adding detections for any access to the metadata IP range. These controls turn a high-risk SSRF vector into a contained, low-impact event.
