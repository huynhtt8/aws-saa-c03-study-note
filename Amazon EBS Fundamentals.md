# Amazon Elastic Block Store (EBS) Fundamentals

Amazon Elastic Block Store (EBS) provides persistent block storage volumes for use with Amazon EC2 instances. These network-attached storage devices can be created, attached, detached, and managed independently from the lifecycle of EC2 instances.

## Key Characteristics

### Network-Attached Storage
- EBS volumes are attached to instances over the network (similar to a "network USB stick")
- This introduces some network latency compared to instance store (local) volumes
- Allows for quick detachment and reattachment to different instances

### Data Persistence
- Data persists even after EC2 instance termination (unless "Delete on Termination" is enabled)
- Enables long-term data storage independent of instance lifecycle

### Single Instance Attachment
- An EBS volume can only be attached to one EC2 instance at a time
- Advanced configurations with multi-attach are available for specific volume types and use cases

### Availability Zone Lock
- EBS volumes are locked to a specific Availability Zone (AZ)
- A volume in us-east-1a cannot be directly attached to an instance in us-east-1b
- To move volumes across AZs, you must create a snapshot first

### Provisioned Capacity
- Storage capacity must be provisioned in advance
- You are billed for all provisioned capacity, not just what you use
- Capacity can be increased over time without disrupting attached instances

### Free Tier Eligibility
- AWS Free Tier includes 30GB of EBS General Purpose (SSD) or Magnetic storage

## EBS Volume Types

### General Purpose SSD (gp2/gp3)
- Balanced price and performance
- Ideal for boot volumes, dev/test environments, and low-latency applications
- gp3 (latest generation) offers independent provisioning of IOPS and throughput

### Provisioned IOPS SSD (io1/io2)
- Highest performance for critical, I/O-intensive workloads
- Used for databases and applications requiring consistent, low-latency performance
- io2 offers higher durability and more IOPS per GB than io1

### Throughput Optimized HDD (st1)
- Low-cost HDD volume for frequently accessed, throughput-intensive workloads
- Ideal for big data, data warehouses, and log processing
- Cannot be used as boot volumes

### Cold HDD (sc1)
- Lowest cost HDD volume for less frequently accessed workloads
- Ideal for archive data or scenarios where the lowest storage cost is important
- Cannot be used as boot volumes

### Magnetic (standard)
- Legacy volume type
- Lowest cost per GB for EBS volumes that are infrequently accessed
- Used for workloads where data is accessed infrequently and performance is not critical

## Key Operations

### Snapshots
- Point-in-time copies of EBS volumes stored in Amazon S3
- Used for backup, volume copying across AZs or regions, and volume type changes
- Only charged for the storage of incremental data changes

### Encryption
- Encrypted volumes use AWS KMS keys for encryption at rest and in transit
- Minimal impact on performance
- Encryption status cannot be changed after volume creation (requires snapshot and recreation)

### Multi-Attach
- Available for io1/io2 volumes in specific scenarios
- Allows a single volume to be attached to multiple instances in the same AZ
- Primarily used with clustered databases and applications that manage concurrent write operations