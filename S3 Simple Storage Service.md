# Day 08 - S3 (Simple Storage Service)

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

## S3 Object Lock
S3 Object Lock helps prevent objects from being deleted or overwritten for a fixed retention period.
It is commonly used to meet regulatory or legal hold requirements.

Key points:
- Object Lock works only with **versioning enabled** on the bucket.
- You can enforce retention at the **bucket** level or **object** level.
- Two retention modes are available:

### Governance Mode
- Users with special permissions can delete or alter locked objects.
- Useful when you want strong protection but still need admin override.

### Compliance Mode
- No user (including the root user) can delete or alter locked objects until retention expires.
- Used for strict regulatory requirements where data must be immutable.

## S3 Lifecycle Management
Lifecycle rules automatically transition objects between storage classes or expire them.

Common actions:
- **Transition** objects (e.g., Standard -> Standard-IA -> Glacier Deep Archive).
- **Expire** objects after a set number of days.
- **Clean up** incomplete multipart uploads.

Typical use case:
- Keep recent data in S3 Standard for quick access, then move older data to cheaper archive tiers.

## S3 Object Versioning
Versioning keeps multiple variants of an object in the same bucket.

Benefits:
- Protects against accidental deletion or overwrites.
- Allows restore of previous versions.
- Required for features like S3 Object Lock and MFA Delete.

Behavior:
- Deleting an object creates a **delete marker**, not a permanent delete.
- You can permanently remove a specific version if needed.

## S3 Static Website Hosting
S3 can host static websites (HTML, CSS, JS) without a web server.

How it works:
- Enable **Static website hosting** in bucket properties.
- Upload website files (e.g., `index.html`, `error.html`).
- The bucket provides a website endpoint URL.

Notes:
- Works only for static content (no server-side logic).
- Make the content public or use CloudFront for secure distribution.

## References
- https://www.geeksforgeeks.org/devops/introduction-to-aws-simple-storage-service-aws-s3/ (GeeksforGeeks S3 documents)
- https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html (Official AWS documentation)
