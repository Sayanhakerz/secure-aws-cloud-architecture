# Finding 02 — Public EC2 Instance Exposure

## Risk

The initial EC2 workload was deployed in a public subnet with a public IPv4 address.

Combined with the initial inbound SSH rule, this made the instance directly reachable from the Internet.

## Security Impact

A publicly reachable workload increases the external attack surface and exposes the instance to unsolicited Internet traffic.

## Remediation

A separate application EC2 instance was deployed in the private subnet:

- Private subnet: `10.0.2.0/24`
- No public IPv4 address
- Security group with no inbound rules
- AWS Systems Manager Session Manager for administration
- NAT Gateway for controlled outbound Internet connectivity

## Validation

The private EC2 instance was successfully accessed through AWS Systems Manager without SSH.

Outbound Internet connectivity was also validated through the NAT Gateway.

### Evidence

- `evidence/05-insecure-ec2-deployment.png`
- `evidence/14-private-ec2-ssm-validation.png`
- `evidence/16-private-ec2-nat-connectivity.png`
