# Finding 03 — S3 Encryption Enforcement

## Risk

Object storage requires strong encryption-at-rest controls to protect stored data.

The lab therefore implements customer-managed AWS KMS encryption for the S3 bucket and explicitly enforces the approved KMS key through a bucket policy.

## Security Controls Implemented

The S3 bucket was configured with:

- S3 Block Public Access enabled
- Bucket owner enforced
- Versioning enabled
- Server-Side Encryption with AWS KMS (SSE-KMS)
- Customer-managed KMS key
- S3 Bucket Key enabled
- Bucket policy enforcing the approved KMS key

## Validation

A normal object upload was validated and returned:

`ServerSideEncryption: aws:kms`

The KMS key ID and S3 Bucket Key were also confirmed.

An attempted upload using SSE-S3 (`AES256`) was denied by the bucket policy, demonstrating that the required KMS encryption control is enforced.

## Security Impact

These controls help protect data at rest and prevent objects from being uploaded using an encryption method that violates the lab's defined storage-security requirement.

## Evidence

- `evidence/10-kms-key.png`
- `evidence/11-s3-kms-encryption.png`
- `evidence/12-s3-block-public-access.png`
- `evidence/13-encrypted-s3-object.png`
- `evidence/15-s3-kms-bucket-policy.png`
- `evidence/18-s3-kms-encryption-validation.png`
- `evidence/19-s3-encryption-policy-denied.png`
