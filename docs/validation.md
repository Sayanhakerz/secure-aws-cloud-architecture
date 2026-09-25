# Security Validation

This document records the validation performed after implementing the AWS cloud-security controls.

## 1. IAM Role Validation

The private EC2 instance was accessed through AWS Systems Manager Session Manager.

The active AWS identity was verified using:

`aws sts get-caller-identity`

The output confirmed that the EC2 workload was operating under the dedicated IAM role rather than using static credentials.

**Evidence:** `evidence/17-iam-role-validation.png`

---

## 2. S3/KMS Encryption Validation

An object was uploaded to the S3 bucket and inspected using the AWS CLI.

The response confirmed:

- `ServerSideEncryption: aws:kms`
- Customer-managed KMS key
- S3 Bucket Key enabled

**Evidence:** `evidence/18-s3-kms-encryption-validation.png`

---

## 3. Encryption Policy Enforcement

An upload using SSE-S3 (`AES256`) was intentionally attempted.

The request was denied with `AccessDenied` because the S3 bucket policy requires the configured KMS key.

This demonstrates that the encryption requirement is actively enforced rather than simply configured as a default.

**Evidence:** `evidence/19-s3-encryption-policy-denied.png`

---

## 4. IAM Least-Privilege Validation

The EC2 IAM role was tested against S3 operations.

Required operations such as object listing and upload were permitted.

An attempted object deletion was denied because the role does not contain:

`s3:DeleteObject`

This demonstrates that permissions were restricted to the operations required by the workload.

**Evidence:** `evidence/20-iam-least-privilege-validation.png`

---

## 5. Private EC2 Validation

The application EC2 instance was deployed without a public IPv4 address.

AWS Systems Manager Session Manager was successfully used for administrative access.

**Evidence:** `evidence/14-private-ec2-ssm-validation.png`

---

## 6. NAT Gateway Validation

The private EC2 instance successfully reached an external HTTPS endpoint using:

`curl -I https://example.com`

The successful HTTP response demonstrates outbound Internet connectivity through the configured NAT Gateway without assigning a public IPv4 address to the private instance.

**Evidence:** `evidence/16-private-ec2-nat-connectivity.png`

---

## 7. Security Group Validation

The initial public SSH rule was removed.

The final private application security-group configuration contains no inbound rules.

Administrative access is performed through Systems Manager rather than inbound SSH.

**Evidence:**

- `evidence/04-insecure-security-group.png`
- `evidence/06-hardened-security-group.png`
- `evidence/09-ssm-session-validation.png`

---

## Validation Summary

| Security Control | Result |
|---|---|
| Network segmentation | Validated |
| Private EC2 deployment | Validated |
| SSM-based administration | Validated |
| NAT outbound connectivity | Validated |
| S3 SSE-KMS encryption | Validated |
| KMS encryption enforcement | Validated |
| IAM role usage | Validated |
| IAM least privilege | Validated |
| Public SSH removal | Validated |

## Conclusion

The implemented AWS security controls were validated through AWS Console configuration checks and AWS CLI commands executed through Systems Manager Session Manager.

The validation confirms that the hardened environment provides:

- Reduced public attack surface
- Private workload isolation
- SSM-based administration
- Controlled outbound connectivity
- KMS-backed encryption
- Enforced storage encryption
- Least-privilege IAM permissions

All validation results are supported by the evidence screenshots included in this repository.
