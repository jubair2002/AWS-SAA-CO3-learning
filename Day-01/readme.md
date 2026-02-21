# Day 01 — Cloud Basics
Last modified: 21 Feb 2026

## What Is Cloud Computing
Cloud computing means renting IT resources (servers, storage, databases, networking, software) over the internet instead of owning physical hardware.

## Why We Use Cloud
- **Lower upfront cost**: Pay as you go instead of buying hardware.
- **Scalability**: Increase or decrease resources quickly.
- **Speed**: Launch services in minutes, not weeks.
- **Global reach**: Deploy in multiple regions worldwide.
- **Reliability**: Built‑in redundancy and high availability.

## Why Not Physical Infrastructure (On-Prem Only)
- **High initial investment (CapEx)**: Servers, storage, networking, racks, cooling, power.
- **Slow procurement**: Buying and installing hardware can take weeks/months.
- **Over/under provisioning risk**:
  - Buy too much: wasted cost.
  - Buy too little: performance issues during demand spikes.
- **Operations burden**: Hardware maintenance, patching, backups, replacements.
- **Disaster recovery complexity**: Need secondary site and replication planning.
- **Limited global expansion**: Opening infrastructure in other countries is costly and slow.

## Challenges with Physical Data Centers
- **Power and cooling failures** can cause outages.
- **Hardware failures** (disk, PSU, network devices) require manual replacement.
- **Security overhead**: Physical access controls, CCTV, guards, audits.
- **Capacity planning is hard**: Future demand is uncertain.
- **Downtime windows** for maintenance and upgrades.
- **Skilled staffing requirement**: 24/7 infra/network/sysadmin support.

## Cloud vs Physical Data Center (Quick Comparison)

<table>
  <thead>
    <tr>
      <th>Area</th>
      <th>Cloud</th>
      <th>Physical Data Center</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Cost model</td>
      <td>OpEx (pay-as-you-go)</td>
      <td>CapEx (large upfront spending)</td>
    </tr>
    <tr>
      <td>Provisioning time</td>
      <td>Minutes</td>
      <td>Weeks to months</td>
    </tr>
    <tr>
      <td>Scalability</td>
      <td>Elastic, on-demand</td>
      <td>Limited by owned hardware</td>
    </tr>
    <tr>
      <td>Availability</td>
      <td>Multi-AZ/Region options</td>
      <td>Need to build redundancy yourself</td>
    </tr>
    <tr>
      <td>Global deployment</td>
      <td>Fast (new region in clicks)</td>
      <td>Expensive and slow expansion</td>
    </tr>
    <tr>
      <td>Maintenance</td>
      <td>Mostly provider-managed</td>
      <td>Fully your responsibility</td>
    </tr>
    <tr>
      <td>Disaster recovery</td>
      <td>Built-in services available</td>
      <td>Complex and costly to design</td>
    </tr>
  </tbody>
</table>

## Core Characteristics of Cloud
These three are essential characteristics of cloud services:
- **Elasticity**: Automatically add/remove resources based on demand.
  - Example: Auto Scaling adds EC2 instances when traffic spikes.
- **Scalability**: Ability to grow resources to meet long‑term demand.
  - Example: Move from a small EC2 instance to a larger one, or add more instances.
- **High Availability**: Services stay up even if a component fails.
  - Example: Deploy instances across multiple AZs in a Region.

## Why Use AWS
- Largest service catalog and global infrastructure.
- Strong ecosystem, documentation, and community.
- Mature security and compliance options.

## Cloud Service Models
- ![Types of Cloud Services](types-of-cloud-service.png)
- **IaaS (Infrastructure as a Service)**: You manage OS, apps, and data; provider manages hardware.
  - Example: AWS EC2.
- **PaaS (Platform as a Service)**: Provider manages OS and runtime; you deploy code.
  - Example: AWS Elastic Beanstalk.
- **SaaS (Software as a Service)**: Fully managed software delivered over the internet.
  - Example: Gmail or Salesforce.

## Market Leaders (Top CSPs)
- **AWS** (Amazon Web Services)
- **Microsoft Azure**
- **Google Cloud Platform (GCP)**

## Cloud Comparison Cheat Sheet
This chart gives a quick side-by-side view of major cloud providers and helps map similar services across platforms.

![Cloud Comparison Cheat Sheet](cloud-comparison-cheat-sheet.png)

## Quick Summary
Cloud lets you rent compute, storage, and networking on demand. AWS is a market leader with broad services and global coverage.
