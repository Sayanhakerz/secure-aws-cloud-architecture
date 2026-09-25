# Threat Model

## Project Scope

This lab models a small AWS environment containing:

- VPC
- Public subnet
- Private subnet
- Public EC2 instance
- Private EC2 application instance
- NAT Gateway
- Internet Gateway
- S3 bucket
- AWS KMS
- IAM roles
- AWS Systems Manager Session Manager

## Threat Actors

The primary threat considered is an external Internet-based attacker attempting to:

- Reach publicly exposed workloads
- Exploit unnecessarily open network ports
- Obtain unauthorized access to cloud resources
- Access or modify stored data
- Abuse excessive IAM permissions

## Key Attack Surfaces

### 1. Internet-Facing EC2

A public EC2 instance can receive unsolicited Internet traffic.

**Control:** Remove unnecessary inbound access and move application workloads to a private subnet.

### 2. SSH Exposure

SSH exposed to `0.0.0.0/0` increases the attack surface.

**Control:** Remove inbound TCP/22 and use AWS Systems Manager Session Manager.

### 3. Storage Access

Unauthorized access to S3 objects could expose sensitive data.

**Controls:**

- S3 Block Public Access
- SSE-KMS encryption
- Customer-managed KMS key
- Bucket policy enforcing the approved KMS key

### 4. Excessive IAM Permissions

Overly broad permissions can increase the impact of a compromised workload.

**Control:** Use a dedicated IAM role with permissions limited to the required S3 and KMS operations.

## Security Objectives

The hardened architecture aims to provide:

- Network segmentation
- Reduced Internet exposure
- Controlled outbound connectivity
- Secure administrative access
- Encryption at rest
- Least-privilege authorization
- Security-policy validation

## Validation Approach

Security controls were validated using AWS Console configuration checks and commands executed through AWS Systems Manager Session Manager.

The project includes evidence screenshots demonstrating both the initial security issues and the implemented controls.
