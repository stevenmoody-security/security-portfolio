# Encryption & Key Management in the Real World

Encryption only works if the keys are protected, because attackers rarely try to “break AES” — they steal or misuse the keys instead. In a cloud environment, this often happens when an attacker compromises an IAM role that can perform KMS `Decrypt` operations, allowing them to read data even though it appears “encrypted at rest`. 

A SOC analyst detects this not by looking at ciphertext, but by identifying unusual KMS API calls, abnormal decrypt volumes, or credential use from unexpected IP ranges or services. Key exposure also manifests when developers embed private keys in repositories, when certificates are not rotated, or when HSM access controls are misconfigured. The business impact is immediate: loss of confidentiality, integrity failures, and potential data exfiltration of assets the organization incorrectly assumed were protected. 

Recovery requires revoking affected credentials, rotating all compromised keys, tightening IAM conditions, and enabling alerting on sensitive KMS/API activity. Effective cryptography isn’t about algorithms — it’s about operational control, auditing, and lifecycle management of the keys themselves.
