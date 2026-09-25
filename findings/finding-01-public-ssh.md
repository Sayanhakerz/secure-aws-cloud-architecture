# Finding 01 — Public SSH Exposure

## Risk

The initial EC2 security group allowed inbound SSH access from:

`0.0.0.0/0`

This exposed TCP port 22 to the public Internet.

## Security Impact

Publicly exposed SSH increases the attack surface and can allow unauthorized connection attempts against the instance.

## Remediation

SSH access was removed from the security group.

Administrative access was replaced with AWS Systems Manager Session Manager, eliminating the need for inbound SSH access.

## Validation

The hardened security group no longer permits inbound TCP/22.

AWS Systems Manager Session Manager was successfully used to access the EC2 instance without SSH.

### Evidence

- `04-insecure-security-group.png`
- `06-hardened-security-group.png`
- `09-ssm-session-validation.png`
