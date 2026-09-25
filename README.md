# 🛡️ Secure AWS Cloud Architecture & Hardening Lab

### Practical AWS Cloud Security Assessment, Hardening & Validation

A hands-on AWS cloud security project demonstrating how to identify, remediate, and validate common cloud-security risks using **network segmentation, IAM least privilege, AWS Systems Manager, S3/KMS encryption, security groups, and controlled network connectivity**.

The environment was intentionally configured with selected security weaknesses and then hardened using AWS security controls.

---

## 🎯 Project Overview

This project simulates a real-world cloud security hardening exercise.

The environment was designed to demonstrate:

- Network segmentation
- Public vs private workload placement
- Security group hardening
- Removal of unnecessary SSH exposure
- AWS Systems Manager Session Manager administration
- IAM least privilege
- S3 security controls
- AWS KMS encryption
- Encrypted EBS storage
- NAT Gateway controlled outbound connectivity
- Security-policy enforcement
- Security validation using AWS CLI

The project follows a simple security lifecycle:

**Deploy → Assess → Identify → Harden → Validate → Document**

---

## 🏗️ Architecture

### Secure Architecture

![Secure AWS Architecture](architecture/secure-architecture.png)

The final architecture uses:

- AWS VPC
- Public and private subnets
- Internet Gateway
- NAT Gateway
- Private EC2 workload
- AWS Systems Manager Session Manager
- IAM role with least-privilege permissions
- Amazon S3
- AWS KMS customer-managed key
- Encrypted EBS storage
- Security group controls

### Network Design

| Component | CIDR / Configuration |
|---|---|
| VPC | `10.0.0.0/16` |
| Public Subnet | `10.0.1.0/24` |
| Private Subnet | `10.0.2.0/24` |
| Public Route Table | Internet Gateway |
| Private Route Table | NAT Gateway |
| Private EC2 | No public IPv4 |
| Administration | AWS Systems Manager |

---

# 🔍 Initial Security Assessment

The lab intentionally staged common cloud-security weaknesses for assessment.

### Finding 01 — Public SSH Exposure

The initial security group allowed:

```text
TCP 22
Source: 0.0.0.0/0
```

This exposed SSH to Internet-based connection attempts.

### Remediation

The inbound SSH rule was removed.

Administrative access was replaced with:

**AWS Systems Manager Session Manager**

📄 Detailed finding:

[`findings/finding-01-public-ssh.md`](findings/finding-01-public-ssh.md)

---

### Finding 02 — Public EC2 Exposure

The initial workload was deployed in a public subnet with a public IPv4 address.

### Remediation

The application workload was deployed in the private subnet with:

- No public IPv4 address
- No inbound security-group rules
- AWS Systems Manager access
- NAT Gateway for controlled outbound Internet access

📄 Detailed finding:

[`findings/finding-02-public-ec2.md`](findings/finding-02-public-ec2.md)

---

### Finding 03 — S3 Encryption Enforcement

The project implements customer-managed KMS encryption for S3 objects and enforces the approved KMS key through an S3 bucket policy.

### Security Controls

- S3 Block Public Access
- Bucket owner enforced
- Versioning
- SSE-KMS
- Customer-managed KMS key
- S3 Bucket Key
- KMS encryption enforcement

An attempted SSE-S3 (`AES256`) upload was denied by the bucket policy.

📄 Detailed finding:

[`findings/finding-03-s3-encryption.md`](findings/finding-03-s3-encryption.md)

---

### Finding 04 — IAM Least Privilege

The private EC2 workload uses a dedicated IAM role with permissions restricted to the operations required by the lab.

The role does not include unnecessary permissions such as:

```text
s3:DeleteObject
```

An attempted object deletion was denied during validation.

📄 Detailed finding:

[`findings/finding-04-iam-least-privilege.md`](findings/finding-04-iam-least-privilege.md)

---

# 🔐 Security Hardening Implemented

## 1. Network Segmentation

The VPC was divided into:

### Public Subnet

```text
10.0.1.0/24
```

Used for Internet-facing networking components.

### Private Subnet

```text
10.0.2.0/24
```

Used for the application EC2 workload.

The private workload does not have a public IPv4 address.

---

## 2. Internet Gateway

An Internet Gateway provides Internet connectivity for the public subnet.

The public route table contains:

```text
0.0.0.0/0 → Internet Gateway
```

---

## 3. NAT Gateway

The private subnet uses a NAT Gateway for controlled outbound Internet connectivity.

The private EC2 instance was able to reach an external HTTPS endpoint without having a public IPv4 address.

Validation:

```text
curl -I https://example.com
```

Evidence:

`evidence/16-private-ec2-nat-connectivity.png`

---

## 4. Security Group Hardening

The initial security group contained:

```text
TCP 22 → 0.0.0.0/0
TCP 80 → 0.0.0.0/0
```

The unnecessary SSH exposure was removed.

The private application security group contains:

```text
No inbound rules
```

Administrative access is performed through AWS Systems Manager.

📄 Configuration documentation:

- [`security-groups/insecure-rules.md`](security-groups/insecure-rules.md)
- [`security-groups/hardened-rules.md`](security-groups/hardened-rules.md)

---

# 🖥️ AWS Systems Manager Session Manager

SSH was removed as the administrative access mechanism for the hardened workload.

AWS Systems Manager Session Manager provides shell access without requiring:

- Public SSH exposure
- Port 22 inbound access
- SSH key distribution

The EC2 instance uses an IAM role containing:

```text
AmazonSSMManagedInstanceCore
```

Session Manager was successfully validated.

Evidence:

`evidence/09-ssm-session-validation.png`

---

# 🔑 IAM Least Privilege

The private EC2 workload uses:

```text
Secure-Cloud-App-S3-Role
```

The role provides only the permissions required for the demonstrated workload.

### S3 Permissions

```text
s3:ListBucket
s3:GetObject
s3:PutObject
```

### KMS Permissions

```text
kms:Decrypt
kms:GenerateDataKey
```

Permissions are scoped to the specific S3 bucket, objects, and customer-managed KMS key.

📄 Policy:

[`iam/least-privilege-policy.json`](iam/least-privilege-policy.json)

---

# 🗄️ S3 & AWS KMS Security

The S3 bucket was configured with multiple security controls.

### S3 Configuration

- Block Public Access enabled
- Bucket owner enforced
- Versioning enabled
- SSE-KMS encryption
- Customer-managed KMS key
- S3 Bucket Key enabled

### Encryption Enforcement

The bucket policy requires the approved KMS key for object uploads.

An attempted upload using:

```text
AES256 / SSE-S3
```

was denied with `AccessDenied`.

This demonstrates active policy enforcement rather than relying only on default encryption settings.

📄 Bucket policy:

[`s3/bucket-policy.json`](s3/bucket-policy.json)

---

# 💽 EBS Encryption

The private EC2 root EBS volume was configured with encryption using an AWS KMS customer-managed key.

This provides encryption at rest for the EC2 root volume.

---

# 🧪 Security Validation

The implemented controls were tested using AWS Console configuration checks and AWS CLI commands through Systems Manager Session Manager.

### Validation Results

| Security Control | Validation |
|---|---|
| VPC network segmentation | ✅ Validated |
| Public/private subnet separation | ✅ Validated |
| Private EC2 deployment | ✅ Validated |
| No public IPv4 on private EC2 | ✅ Validated |
| SSM-based administration | ✅ Validated |
| NAT outbound connectivity | ✅ Validated |
| SSH exposure removed | ✅ Validated |
| S3 SSE-KMS encryption | ✅ Validated |
| KMS encryption enforcement | ✅ Validated |
| S3 Block Public Access | ✅ Validated |
| IAM role usage | ✅ Validated |
| IAM least privilege | ✅ Validated |
| Unauthorized object deletion denied | ✅ Validated |

📄 Full validation documentation:

[`docs/validation.md`](docs/validation.md)

---

# 📸 Evidence

The project contains **20 evidence screenshots** documenting the implementation and validation process.

| Evidence | Description |
|---|---|
| `01` | Private route table |
| `02` | Public route table |
| `03` | VPC resource map |
| `04` | Initial insecure security group |
| `05` | Initial EC2 deployment |
| `06` | Hardened security group |
| `07` | EC2 SSM IAM role |
| `08` | IAM role attached to EC2 |
| `09` | SSM session validation |
| `10` | KMS key |
| `11` | S3 KMS encryption |
| `12` | S3 Block Public Access |
| `13` | Encrypted S3 object |
| `14` | Private EC2 SSM validation |
| `15` | S3 KMS bucket policy |
| `16` | Private EC2 NAT connectivity |
| `17` | IAM role validation |
| `18` | S3 KMS encryption validation |
| `19` | S3 encryption policy denial |
| `20` | IAM least-privilege validation |

📁 All evidence:

[`evidence/`](evidence/)

---

# 🧠 Threat Model

The project considers an external attacker attempting to:

- Reach publicly exposed workloads
- Exploit unnecessarily open network ports
- Obtain unauthorized access
- Access stored data
- Abuse excessive IAM permissions

Primary controls include:

```text
Network Segmentation
        ↓
Reduced Public Exposure
        ↓
SSM-Based Administration
        ↓
Least-Privilege IAM
        ↓
KMS Encryption
        ↓
Policy Enforcement
        ↓
Security Validation
```

📄 Full threat model:

[`docs/threat-model.md`](docs/threat-model.md)

---

# 📋 Hardening Report

The complete security hardening process is documented in:

[`docs/hardening-report.md`](docs/hardening-report.md)

It covers:

- Initial security conditions
- Network hardening
- EC2 hardening
- SSM administration
- S3 security
- KMS encryption
- IAM least privilege
- Security validation

---

# 🛠️ AWS Services Used

| AWS Service | Purpose |
|---|---|
| Amazon VPC | Network isolation |
| Amazon EC2 | Compute workloads |
| IAM | Identity and access management |
| IAM Roles | Workload permissions |
| AWS Systems Manager | Secure administration |
| Amazon S3 | Object storage |
| AWS KMS | Encryption key management |
| NAT Gateway | Private subnet outbound connectivity |
| Internet Gateway | Public subnet Internet connectivity |
| Route Tables | Network traffic routing |
| Security Groups | Instance-level network filtering |
| Amazon EBS | EC2 block storage |

---

# 🧰 Skills Demonstrated

### Cloud Security

- AWS security architecture
- Cloud hardening
- Attack-surface reduction
- Network segmentation
- Security validation
- Threat modeling

### AWS Networking

- VPC
- CIDR addressing
- Public/private subnets
- Route tables
- Internet Gateway
- NAT Gateway
- Security Groups

### Identity & Access Management

- IAM roles
- Least privilege
- Resource-scoped permissions
- IAM policy design
- Workload identity

### Data Security

- S3 security
- KMS
- SSE-KMS
- Encryption at rest
- Bucket policies
- S3 Block Public Access

### Security Operations

- Security assessment
- Misconfiguration identification
- Hardening
- Control validation
- Evidence collection
- Security documentation

---

# 📁 Project Structure

```text
secure-aws-cloud-architecture/
│
├── architecture/
│   └── secure-architecture.png
│
├── evidence/
│   ├── 01-private-route-table.png
│   ├── 02-public-route-table.png
│   ├── 03-vpc-resource-map.png
│   ├── 04-insecure-security-group.png
│   ├── 05-insecure-ec2-deployment.png
│   ├── 06-hardened-security-group.png
│   ├── 07-ec2-ssm-iam-role.png
│   ├── 08-ec2-iam-role-attached.png
│   ├── 09-ssm-session-validation.png
│   ├── 10-kms-key.png
│   ├── 11-s3-kms-encryption.png
│   ├── 12-s3-block-public-access.png
│   ├── 13-encrypted-s3-object.png
│   ├── 14-private-ec2-ssm-validation.png
│   ├── 15-s3-kms-bucket-policy.png
│   ├── 16-private-ec2-nat-connectivity.png
│   ├── 17-iam-role-validation.png
│   ├── 18-s3-kms-encryption-validation.png
│   ├── 19-s3-encryption-policy-denied.png
│   └── 20-iam-least-privilege-validation.png
│
├── findings/
│   ├── finding-01-public-ssh.md
│   ├── finding-02-public-ec2.md
│   ├── finding-03-s3-encryption.md
│   └── finding-04-iam-least-privilege.md
│
├── iam/
│   └── least-privilege-policy.json
│
├── s3/
│   └── bucket-policy.json
│
├── security-groups/
│   ├── insecure-rules.md
│   └── hardened-rules.md
│
├── docs/
│   ├── threat-model.md
│   ├── hardening-report.md
│   └── validation.md
│
└── README.md
```

---

# 🎓 Key Security Lessons

This project demonstrates several practical cloud-security principles:

### 1. Minimize Internet Exposure

Workloads that do not require direct Internet access should not receive public IP addresses unnecessarily.

### 2. Avoid Public SSH

Administrative access can be performed through AWS Systems Manager instead of exposing SSH to the Internet.

### 3. Segment Networks

Separating public and private workloads reduces unnecessary network exposure.

### 4. Use Least Privilege

IAM permissions should be limited to the resources and actions actually required.

### 5. Encrypt Data at Rest

S3 objects and EBS volumes should use appropriate encryption controls.

### 6. Enforce Security Policies

Security controls should be enforced through policies where possible rather than relying only on configuration defaults.

### 7. Validate Security Controls

A security control is more useful when its effectiveness can be demonstrated through testing and evidence.

---

# 🚀 Project Outcome

The final environment demonstrates a hardened AWS cloud architecture with:

- Public/private network segmentation
- Private EC2 workload
- No public SSH access to the hardened workload
- AWS Systems Manager administration
- NAT-based outbound connectivity
- S3 Block Public Access
- KMS-backed S3 encryption
- Encrypted EBS storage
- Least-privilege IAM
- Security-policy enforcement
- Documented validation evidence

The project focuses on **practical implementation rather than theoretical configuration**, with each major security control supported by evidence.

---

## 📌 Disclaimer

This project was created as a controlled educational security lab in an AWS environment owned and operated for testing purposes.

The intentionally insecure configurations were created only for security assessment and hardening demonstrations.
