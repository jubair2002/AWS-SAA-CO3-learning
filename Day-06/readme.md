# Day 06 - All About EBS Volume, Performance Metrics, and Types

Last modified: 25 Feb 2026

Amazon EBS (Elastic Block Store) is network-attached, persistent block storage.  
An EBS volume behaves like a virtual hard disk that you can attach to an EC2 instance over the AWS network.

## What is an EBS Volume
- EBS is block-level storage designed for EC2.
- A volume is created inside a single Availability Zone (AZ).
- You can detach and reattach a volume to another instance in the same AZ.

## EBS and EC2 Attachment
When you launch an EC2 instance, AWS automatically creates the root EBS volume (OS disk).  
After that, you can create additional EBS volumes and attach them to the instance.

![After EC2 creation - volume](./after%20create%20ec2%20.volume.png)

## Create a New EBS Volume
Create a volume by selecting size, type, and AZ.  
The volume must be in the same AZ as the target EC2 instance for attachment.

![Create volume](./create%20volume.png)

## Attach Additional Volumes
You can attach one or more newly created volumes to the EC2 instance as additional block devices.

![Attach newly created volumes with EC2](./attached%202%20newly%20create%20vlume%20with%20ec2%20attach.png)

## Verify Volumes in the Instance
After attachment, log in to the server and verify that new block devices are visible in the OS.  
Here, `nvme0n1p1` is the OS disk and `nvme1n1` is an additional 5 GB disk.

![Check 2 volumes from Ubuntu server](./check%202%20volume%20from%20ubuntuserver.png)

## Create EBS Snapshots
Snapshots are point-in-time backups of EBS volumes.  
They are used for backup, restore, and migration.

![Create snapshot](./create%20snapshot.png)

## Create Volume from Snapshot
You can create a new EBS volume from an existing snapshot and use it to recover or clone data.

![From snapshot created EBS](./from%20ebs%20snapshort%20created.png)

## Restore to Another Availability Zone
Here you can see two EBS volumes created from snapshots.  
From a snapshot, you can create an EBS volume in a different AZ and attach it to an EC2 instance.

![From snapshot create EBS in another AZ](./from%20snapshot%20create%20ebs%20anohter%20avaaiability%20zone.png)

## Performance Metrics in Storage Devices

When selecting a storage device, two key performance metrics are:

### 1) IOPS (Input/Output Operations Per Second)
- Measures how many read/write operations a disk can process per second.
- Best for small, random I/O workloads.
- Typical workloads: OLTP databases, transactional apps, latency-sensitive systems.

### 2) Throughput
- Measures how much data can be transferred per second (MB/s).
- Important for large sequential I/O workloads.
- Typical workloads: log processing, analytics scans, big data pipelines.

In short:
- `IOPS` = number of operations per second.
- `Throughput` = amount of data per second.

## EBS Volume Types

![Volume type](./volume%20tpe.png)

AWS EBS volumes are broadly grouped into two families: `SSD` and `HDD`.

## SSD Volume Types (2 Types)
![SSD types details](./ssd%20types%20details.png)

### 1) General Purpose SSD (`gp3` / `gp2`)
- Balanced price and performance.
- Suitable for boot volumes, dev/test, and general application workloads.
- `gp3` is the preferred default for new deployments.
- `gp2` is considered legacy in many environments.

### 2) Provisioned IOPS SSD (`io2` / `io1`)
- Designed for very high IOPS and low latency.
- Best for mission-critical databases and I/O-intensive applications.
- You provision performance to meet strict requirements.

## HDD Volume Types (2 Types)
![HDD type details](./hdd%20type%20details.png)

### 1) Throughput Optimized HDD (`st1`)
- Low-cost HDD for frequently accessed, throughput-heavy workloads.
- Best for large sequential reads/writes.

### 2) Cold HDD (`sc1`)
- Lowest-cost HDD option.
- Best for infrequently accessed data.

## Key Features of EBS
| Feature | What it means |
|---|---|
| Scalability and Elasticity | You can increase EBS volume size as needed. For `gp3` and `io1/io2`, you can also adjust provisioned IOPS and throughput. |
| Backup and Recovery (Snapshots) | Snapshots are point-in-time backups and incremental after the first snapshot. You can create new volumes from snapshots and copy snapshots across regions. |
| Encryption | EBS supports encryption at rest (and integration with encrypted in-transit paths on supported instances). Encryption uses AWS KMS, and volumes/snapshots created from encrypted sources stay encrypted. |
| Durability and Availability | EBS volumes are replicated within an Availability Zone to improve durability. `io2 Block Express` is designed for very high durability. |


## References
- https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html (Official AWS documentation)
- https://www.geeksforgeeks.org/devops/introduction-to-aws-elastic-block-storeebs/ (GeeksforGeeks EBS documents)
