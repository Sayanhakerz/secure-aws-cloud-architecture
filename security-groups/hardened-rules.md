# Hardened Security Group Configuration

## EC2 Security Controls

After the initial security assessment, inbound SSH access was removed from the public EC2 security group.

## Final Inbound Rules

| Protocol | Port | Source | Purpose |
|---|---:|---|---|
| TCP | 80 | `0.0.0.0/0` | HTTP |

## SSH Access

Inbound TCP/22 is **not permitted**.

Administrative access is performed through:

**AWS Systems Manager Session Manager**

This removes the need to expose SSH to the Internet.

## Private Application EC2

The private application EC2 uses `Secure-Cloud-Private-SG`.

Final inbound configuration:

**No inbound rules**

The instance is managed through AWS Systems Manager using its attached IAM role.

## Security Improvements

- Removed public SSH access.
- Eliminated the need for inbound TCP/22.
- Used Systems Manager Session Manager for administration.
- Deployed the application workload without a public IPv4 address.
- Used public/private subnet segmentation.
- Restricted the private instance from unsolicited inbound traffic.

## Evidence

- `evidence/06-hardened-security-group.png`
- `evidence/14-private-ec2-ssm-validation.png`
- `evidence/16-private-ec2-nat-connectivity.png`
