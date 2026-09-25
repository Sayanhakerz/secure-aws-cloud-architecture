# Finding 04 — IAM Least Privilege

## Risk

Excessive IAM permissions can increase the impact of a compromised workload or stolen credentials.

The lab therefore uses a dedicated IAM role for the private EC2 application with only the permissions required for its demonstrated tasks.

## IAM Role

Role:

`Secure-Cloud-App-S3-Role`

The role is attached to the private EC2 instance and is used through AWS Systems Manager Session Manager.

## Permissions Granted

The role permits:

- `s3:ListBucket` on the lab S3 bucket
- `s3:GetObject` on objects in the lab bucket
- `s3:PutObject` on objects in the lab bucket
- `kms:Decrypt` on the specific customer-managed KMS key
- `kms:GenerateDataKey` on the specific customer-managed KMS key

Permissions are scoped to the required bucket, objects, and KMS key rather than using broad administrative access.

## Validation

The IAM role was validated from the private EC2 instance using:

`aws sts get-caller-identity`

S3 access was successfully tested.

An attempted object deletion was denied because the role does not contain:

` s3:DeleteObject `

This demonstrates that unnecessary permissions are not available to the workload.

## Security Impact

Least-privilege IAM reduces the permissions available to a compromised workload and limits the potential impact of unauthorized access.

## Evidence

- `evidence/17-iam-role-validation.png`
- `evidence/20-iam-least-privilege-validation.png`
