# Investigation: SSRF Attempt Against EC2 Metadata (web-01)

## Trigger

Suspicious access to 169.254.169.254 (EC2 metadata) from `web-01`.

## Evidence Observed

- Web logs show an external IP (`203.0.113.45`) calling `/fetch?url=…metadata…`, a known SSRF pattern.
- The request returned **200 OK**, meaning the backend made the metadata call.
- VPC Flow Logs confirm traffic from `web-01` to the metadata endpoint was **ACCEPTED**.
- CloudTrail shows **only normal S3 GetObject operations** by the web application’s IAM role.
- No unusual API calls, no unauthorized S3 access, and no signs of credential abuse in the following 2 hours.

## Hypothesis

An attacker attempted to exploit an SSRF vulnerability to access the instance metadata service, but there is no evidence they successfully exfiltrated or used the temporary credentials afterward.

## Decision (True Positive or False Positive) and Why

**True Positive for an SSRF attack attempt**, because the attacker reached the metadata endpoint via `/fetch?url`.  

However, **no successful credential abuse occurred**, as CloudTrail shows only normal application behavior and no anomalous AWS API activity.

## Next Step (Investigation)

Examine a larger window of web server logs around the attacker’s IP to determine whether the `/fetch?url` request was part of a broader probing pattern or multi-step attack.

## Recommended Response (Mitigation)

- Patch or restrict the `/fetch?url` endpoint to block internal IP access, especially metadata.
- Enforce **IMDSv2-only** and disable IMDSv1 across all instances.
- Implement WAF or application-layer rules to block requests containing `169.254.169.254` or similar internal targets.
