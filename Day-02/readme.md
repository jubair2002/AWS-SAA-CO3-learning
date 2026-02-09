# Day 02 — AWS SAA-C03 Notes

## Regions and AZs
- **Region**: A geographic area that contains multiple Availability Zones. Choose based on latency, cost, and compliance.
  - Example: `us-east-1` (N. Virginia).
- **Availability Zone (AZ)**: One or more data centers within a Region. AZs are isolated but connected by low‑latency links.
  - Example: `us-east-1a`, `us-east-1b`.

## VPC and CIDR
- **VPC (Virtual Private Cloud)**: A logically isolated network in AWS where you define your IP range and network setup.
  - Example: Create a VPC with CIDR `10.0.0.0/16`.
- **CIDR Range**: IP address block that defines the size of a network.
  - Example: `10.0.0.0/16` gives 65,536 IPs.

## Subnets
- **Subnet**: A smaller network inside a VPC. Each subnet lives in one AZ.
  - Example: Split `10.0.0.0/16` into:
  - Public subnet: `10.0.1.0/24` (AZ-a)
  - Private subnet: `10.0.2.0/24` (AZ-a)
  - Another public subnet: `10.0.3.0/24` (AZ-b)
  - Another private subnet: `10.0.4.0/24` (AZ-b)
- **Public Subnet**: Has a route to the Internet via an Internet Gateway.
- **Private Subnet**: No direct route to the Internet.

## Route Tables
- **Route Table**: Controls where network traffic is directed.
  - Example public route table:
  - `10.0.0.0/16 -> local`
  - `0.0.0.0/0 -> Internet Gateway (IGW)`
  - Example private route table:
  - `10.0.0.0/16 -> local`

## Internet Gateway (IGW)
- **IGW**: Allows internet access for resources in public subnets.
  - Example: Attach IGW to VPC, then add `0.0.0.0/0` to the public route table.

## EC2 and IPs
- **EC2**: Virtual server instance you launch inside a subnet.
- **Private IP**: Internal IP used inside the VPC.
  - Example: `10.0.1.10`
- **Public IP**: Internet‑reachable IP assigned to an instance in a public subnet.
  - Example: `3.88.x.x`
- **Elastic IP (EIP)**: Static public IP you can attach to an instance so it doesn’t change.

## Access and Security
- **Key Pair**: Used to SSH into EC2.
  - Example: `ssh -i my-key.pem ec2-user@3.88.x.x`
- **Security Group**: Virtual firewall attached to instances.
  - **Inbound Rules**: Control incoming traffic.
    - Example: Allow `SSH (TCP 22)` from your IP.
  - **Outbound Rules**: Control outgoing traffic.
    - Example: Allow all outbound.

## Associations (Important)
- **Public subnet → Public route table** (route to IGW).
- **Private subnet → Private route table** (no IGW route).

---
**Author**: Habibullah Jubair  
**Email**: hmjubair12@gmail.com  
**Last Modified**: February 9, 2026
