# Day 05 - EC2 Instance Purchase Options

Last modified: 18 Mar 2026

Amazon EC2 offers a variety of pricing models for different needs. These models allow users to optimize costs based on workload, budget, and usage patterns.

## 1) On-Demand Instances
On-Demand instances are the most flexible pricing option. You only pay for the computing capacity you use, with no long-term commitments. This option is perfect for workloads that are unpredictable.

- Use case: Best for applications with variable workloads and testing environments.
- Key features: No upfront costs, flexible scaling, and easy to adjust as per your requirements.

## 2) Reserved Instances (RIs)
Reserved Instances offer significant savings (up to 75%) over On-Demand pricing if you commit to using EC2 for 1 or 3 years. This model allows you to reserve capacity in specific availability zones and lock in a discounted rate.

- Use case: Best for predictable, steady workloads.
- Key features: Discounted pricing for long-term commitment, capacity reservation.

## 3) Spot Instances
Spot Instances let you use unused EC2 capacity at a lower price, offering savings of up to 90% compared to On-Demand pricing. However, the downside is that AWS can terminate Spot Instances with short notice (usually 2 minutes), so they are best for flexible and fault-tolerant applications.

- Use case: Ideal for cost-sensitive applications that can handle interruptions.
- Key features: Significant cost savings, flexible start/end times.

## 4) Dedicated Hosts
Dedicated Hosts provide physical EC2 servers for your exclusive use, which is useful for customers with specific compliance requirements or those who want to use your existing software licenses. Unlike other EC2 instances, a Dedicated Host gives you more control over the server.

- Use case: Suitable for running workloads that require dedicated resources and compliance with licensing.

## 5) Savings Plans
Savings Plans provide savings beyond On-Demand rates in exchange for a commitment of using a specified amount of compute power (measured per hour) for a one or three year period.

When you sign up for Savings Plans, the prices you'll pay for usage stays the same through the plan term. You can pay for your commitment using All upfront, Partial upfront, or No upfront payment options.

## Reference
- https://docs.aws.amazon.com/ (Official documents of AWS)
