# Day 08 - S3 Basics

Last modified: 07 Mar 2026

## What is Amazon S3?
Amazon S3 (Simple Storage Service) is an object storage service from AWS.
It is used to store and retrieve any amount of data from anywhere on the internet.

You store data in:
- **Buckets** (containers)
- **Objects** (files + metadata)

S3 is highly scalable, durable, and commonly used for backups, static website hosting, logs, media files, and data lakes.

## S3 Storage Classes
S3 offers a range of storage classes designed for different access patterns and costs.

| Storage Class | Access Frequency | Availability Zones | Use Case |
|---|---|---|---|
| S3 Standard | Frequent | >= 3 | General-purpose storage, static websites, cloud apps. |
| S3 Intelligent-Tiering | Unknown/Changing | >= 3 | Data with unpredictable access patterns. Automatically moves data between tiers to save costs. |
| S3 Standard-IA | Infrequent | >= 3 | Long-lived data accessed less than once a month (e.g., backups, DR). |
| S3 One Zone-IA | Infrequent | 1 | Non-critical, reproducible data (e.g., secondary backups). Cheaper but less durable. |
| S3 Glacier Instant Retrieval | Rare (Quarterly) | >= 3 | Archives that need millisecond access (e.g., medical records). |
| S3 Glacier Flexible Retrieval | Rare (Yearly) | >= 3 | Archives where retrieval time of minutes/hours is acceptable. |
| S3 Glacier Deep Archive | Very Rare | >= 3 | Long-term retention (7-10 years) for compliance. Lowest cost. |

## References
- https://www.geeksforgeeks.org/devops/introduction-to-aws-simple-storage-service-aws-s3/ (GeeksforGeeks S3 documents)
- https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html (Official AWS documentation)
