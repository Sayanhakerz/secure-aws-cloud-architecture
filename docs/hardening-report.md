# AWS Cloud Security Hardening Report

## Executive Summary

This project demonstrates the assessment and hardening of a small AWS cloud environment.

The lab intentionally introduced common cloud-security risks and then implemented controls to reduce unnecessary exposure, improve access management, protect stored data, and validate the resulting configuration.

## Initial Security Conditions

The initial assessment identified:

1. Public EC2 deployment
2. SSH access from `0.0.0.0/0`
3. Lack of private workload isolation
4. No dedicated SSM-based administrative workflow
5. Need for enforced S3 encryption controls
6. Need for workload-specific least-privilege IAM permissions

## Hardening Actions

### Network Security

- Created a VPC using `10.0.0.0/16`
- Created a public subnet using `10.0.1.0/24`
- Created a private subnet using `10.0.2.0/24`
- Configured separate route tables
- Configured an Internet Gateway
- Configured a NAT Gateway
- Moved the application workload to the private subnet

### EC2 Security

The private application EC2 instance was configured with:

- No public IPv4 address
- Private IP addressing
- No inbound security-group rules
- AWS Systems Manager access
- Encrypted EBS root volume

### Administrative Access

AWS Systems Manager Session Manager replaced direct SSH access for administrative operations.

This removes the requirement to expose TCP/22 to the Internet.

### S3 Security

The S3 bucket was configured with:

- Block Public Access
- Bucket owner enforced
- Versioning
- SSE-KMS encryption
- Customer-managed KMS key
- S3 Bucket Key
- Bucket policy enforcing the approved KMS key

### IAM Security

A dedicated IAM role was attached to the private EC2 instance.

The role was restricted to the S3 and KMS operations required by the lab.

Unnecessary permissions such as `s3:DeleteObject` were intentionally excluded.

## Security Validation

The following controls were validated:

| Control | Validation |
|---|---|
| Private EC2 | No public IPv4 address |
| SSM access | Successful Session Manager shell |
| NAT connectivity | Successful outbound HTTPS request |
| IAM role | `aws sts get-caller-identity` |
| S3 access | S3 listing/upload successful |
| KMS encryption | Object reported SSE-KMS |
| Encryption enforcement | SSE-S3 upload denied |
| Least privilege | Object deletion denied |
| SSH hardening | TCP/22 removed |

## Result

The final environment demonstrates:

- Network segmentation
- Reduced public attack surface
- SSM-based administration
- Controlled outbound Internet access
- Encryption at rest
- KMS key enforcement
- Least-privilege IAM
- Security-control validation

## Evidence

Detailed screenshots are available in the [`evidence/`](../evidence/) directory.

Security findings are documented in the [`findings/`](../findings/) directory.
