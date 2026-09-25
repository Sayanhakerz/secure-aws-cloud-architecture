# Initial Insecure Security Group Configuration

## Security Group

`Insecure-Web-SG`

## Initial Inbound Rules

| Protocol | Port | Source | Purpose |
|---|---:|---|---|
| TCP | 22 | `0.0.0.0/0` | SSH |
| TCP | 80 | `0.0.0.0/0` | HTTP |

## Security Concern

SSH access from `0.0.0.0/0` allows connection attempts from any IPv4 address on the Internet.

This configuration was intentionally staged as part of the security-hardening exercise to demonstrate identification and remediation of a common cloud security misconfiguration.

## Remediation

The public SSH rule was removed.

Administrative access was replaced with AWS Systems Manager Session Manager, eliminating the requirement for inbound SSH access.

## Evidence

- `evidence/04-insecure-security-group.png`
- `evidence/06-hardened-security-group.png`
- `evidence/09-ssm-session-validation.png`
