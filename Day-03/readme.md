# Day 03 — AWS SAA-C03 Notes
Last modified: 18 Feb 2026

## Topics Covered
- NAT Gateway (NAT GTW)
- Accessing a private EC2 from a public EC2
- Associating private route table with NAT Gateway
- Security Group updates for SSH access

## Architecture (What I Built)
- **Public subnet**:
  - Public EC2 (has public IP)
  - NAT Gateway (with Elastic IP)
- **Private subnet**:
  - Private EC2 (no public IP)
- **Routing**:
  - Public route table: `0.0.0.0/0 -> Internet Gateway (IGW)`
  - Private route table: `0.0.0.0/0 -> NAT Gateway`

## Why NAT Gateway
- Private EC2 should not be publicly reachable.
- Private EC2 still needs outbound internet for updates/packages.
- NAT Gateway gives **outbound internet** to private subnet without exposing inbound access.

## NAT Gateway Setup Steps
1. Create/confirm an **Internet Gateway** and attach it to VPC.
2. Ensure public subnet route table has:
   - `VPC-CIDR -> local`
   - `0.0.0.0/0 -> IGW`
3. Create an **Elastic IP (EIP)**.
4. Create **NAT Gateway** in the **public subnet** and attach EIP.
5. In private route table, add:
   - `0.0.0.0/0 -> NAT Gateway`
6. Associate this private route table with private subnet.

![NAT Gateway in public subnet](./nat-gateway.png)

![Private subnet association with private route table](./subbnet-association-rt.png)

## Access Private Server from Public Server
### Security Group Rules
- **Public SG (public EC2)**:
  - Inbound SSH `22` from **your laptop public IP**.
- **Private EC2 SG**:
  - Inbound SSH `22` from **Public SG** (recommended), or public EC2 private IP.

![Security Group rules for EC2 access](./sg-for-ec2.png)

### Key File Copy + SSH Flow
From your laptop, copy private instance key to public EC2:

```bash
scp -i public-ec2-key.pem private-key.pem ubuntu@<public-ec2-public-ip>:/home/ubuntu/
```

SSH into public EC2:

```bash
ssh -i public-ec2-key.pem ubuntu@<public-ec2-public-ip>
```

Inside public EC2, set permission and connect private EC2:

```bash
chmod 400 private-key.pem
ssh -i private-key.pem ubuntu@<private-ec2-private-ip>
```

## Validate NAT from Private EC2
After logging into private EC2, test outbound internet:

```bash
sudo apt update && sudo apt upgrade -y
```

If updates work, private route table + NAT association is correct.

## Common Issues Checklist
- NAT Gateway must have an **Elastic IP**.
- Private subnet must be associated with **private route table**.
- Private route table must have default route to **NAT Gateway**.
- Security Group of private EC2 must allow SSH from public EC2 SG.
- Key file permission should be `400`.
