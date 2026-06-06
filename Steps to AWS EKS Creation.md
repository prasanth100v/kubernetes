 * AWS CLI command lists all EC2 instance types that AWS currently marks as Free Tier eligible in your account/region.
```hcl
aws ec2 describe-instance-types \
  --filters Name=free-tier-eligible,Values=true \
  --query "InstanceTypes[*].InstanceType"
```
 * Typical output:
```hcl
    "c7i-flex.large",
    "t4g.small",
    "t3.micro",
    "t4g.micro",
    "t3.small",
    "m7i-flex.large"
```
### Important for EKS
 * Not all "free-tier-eligible" instances are suitable for EKS:

| Instance Type  | Architecture | EKS Worker Node |
| -------------- | ------------ | --------------- |
| t3.micro       | x86          | ❌ Too small     |
| t3.small       | x86          | ❌ Too small     |
| t4g.micro      | ARM          | ❌ Too small     |
| t4g.small      | ARM          | ❌ Too small     |
| c7i-flex.large | x86          | ✅ Good          |
| m7i-flex.large | x86          | ✅ Good          |

