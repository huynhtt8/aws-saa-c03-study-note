Amazon Elastic Compute Cloud (EC2) is a core AWS service that provides resizable compute capacity in the cloud. It offers various pricing models to suit different workloads and budget requirements.

## EC2 Pricing Models

### On-Demand Instances
- Pay for compute capacity by the hour or second with no long-term commitments
- Ideal for applications with short-term, spiky, or unpredictable workloads
- Development environments or testing
- No upfront payment, highest cost per hour

### Reserved Instances (RI)
- Significant discount (up to 72%) compared to On-Demand pricing
- Commitment to a specific instance configuration for 1 or 3 years
- Payment options:
  - All Upfront: Largest discount
  - Partial Upfront: Balance between upfront cost and discount
  - No Upfront: Smallest discount
- Types:
  - Standard RI: Cannot change instance attributes
  - Convertible RI: Can change instance family, OS, tenancy, and payment option
  - Scheduled RI: Reserved for specific time windows

### Savings Plans
- Flexible pricing model offering savings similar to Reserved Instances
- 1 or 3-year commitment to a specific amount of usage ($/hour)
- Automatically applies to EC2, Fargate, and Lambda usage
- Compute Savings Plans: Any instance family, size, AZ, region, OS, or tenancy
- EC2 Instance Savings Plans: Specific instance family in a region

### Dedicated Hosts
- Physical EC2 server dedicated entirely to your use
- Helps meet compliance requirements
- Allows use of existing server-bound software licenses
- Most expensive option
- Can be purchased On-Demand or as a Reservation

### Spot Instances
- Discount up to 90% compared to On-Demand pricing
- Define maximum spot price; you get the instance while current spot price < max
- The hourly spot price varies based on offer and capacity
- If current spot price > max, you can choose to stop or terminate (2-minute grace period)
- **Spot Block:**
  - Specified time frame (e.g., 1-6 hours) without interruption
  - Rarely reclaimed
- Ideal for:
  - Batch jobs
  - Data analysis
  - Workloads resilient to failures

#### How to Terminate Spot Instances
1. First cancel the spot request (important)
2. Then terminate the spot instance (not doing step 1 first may cause the spot request to launch new instances)

### Spot Fleet
- A set of Spot Instances + (optional) On-Demand instances
- Tries to meet target capacity within price constraints
- Allocation strategies:
  - Lowest price: Cost optimization for short workloads
  - Diversified: Better availability for long workloads
  - Capacity Optimized: Pool with the optimal capacity for the number of instances
  - Price-Capacity Optimized (recommended): Selects pools with highest capacity available, then selects the lowest price

## EC2 Instance Types [[EC2 Instance Types and Families]]
- **General Purpose (T, M)**: Balanced compute, memory, and networking
- **Compute Optimized (C)**: High-performance processors for compute-bound applications
- **Memory Optimized (R, X, z)**: Fast performance for workloads processing large datasets in memory
- **Storage Optimized (D, I, H)**: High sequential read/write access for large datasets on local storage
- **Accelerated Computing (P, G, F)**: Hardware accelerators like GPUs for specific functions

## EC2 Placement Groups
- **Cluster**: Packs instances close together for low-latency network performance
- **Partition**: Spreads instances across logical partitions to reduce correlated failures
- **Spread**: Strictly places instances on different hardware to reduce simultaneous failures

## EC2 Hibernation
- Preserves the in-memory state (RAM) of an instance by persisting it to the encrypted EBS root volume
- Different from stop: hibernation saves RAM contents, stop discards RAM
- At restart, RAM state is restored from EBS, allowing faster application resumption
- Root EBS volume **must be encrypted** for hibernation to work
- The hibernation process:
  1. The OS writes RAM contents to the EBS root volume
  2. The instance enters the "stopped" state
  3. On restart, the EBS root volume is restored to its previous state and RAM contents are reloaded

### Technical Limitations
- Maximum hibernation duration: 60 days
- RAM size limitation: Instances with up to 150 GB RAM can hibernate
- Supported instance families: Not all instance types support hibernation
- Root volume requirements:
  - Must be EBS
  - Must be encrypted
  - Must have sufficient space to store RAM contents
- AMI requirements: Must be configured for hibernation (specific OS support)

### Use Cases
- **Long-running processing**: Save progress for compute-intensive workloads
- **RAM state preservation**: Maintain application state without keeping instances running
- **Services with long initialization times**: Avoid lengthy startup procedures
- **Pre-warming applications**: Load applications into memory before needed
- **Maintaining session state**: Preserve user sessions for stateful applications
- **Development and testing**: Quick save and resume of development environments

### Benefits
- Faster instance startup compared to cold start
- No need to restart applications or reconnect to databases
- Reduced billing for stopped instances (only pay for EBS storage)
- Preservation of RAM-resident data without persistent network connections

### Compared to Other Instance States
| State | RAM Contents | Billing | Startup Time | Instance IP |
|-------|--------------|---------|--------------|-------------|
| Running | Maintained | Full charges | N/A | Unchanged |
| Stopped | Lost | EBS storage only | Slow | Public IP changes |
| Hibernated | Saved to EBS | EBS storage only | Faster | Public IP changes |

## Quiz Questions

### Question 1
Which EC2 pricing model offers up to 90% discount compared to On-Demand pricing but can be terminated if AWS needs the capacity back?
- A) Reserved Instances
- B) Spot Instances
- C) Dedicated Hosts
- D) Savings Plans

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Spot Instances**  
  Spot Instances offer the highest discount (up to 90%) compared to On-Demand pricing, but AWS can reclaim these instances with a 2-minute notification if the Spot price exceeds your maximum price or AWS needs the capacity back. This makes them suitable for workloads that are resilient to interruptions.
</details>

### Question 2
A company wants to run a critical application that needs to be available 24/7 and cannot tolerate any interruptions. Which EC2 pricing model should they choose?
- A) On-Demand Instances
- B) Spot Instances
- C) Reserved Instances
- D) Spot Fleet with the lowest price strategy

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Reserved Instances**  
  Reserved Instances provide a significant discount (up to 72%) compared to On-Demand pricing and guarantee capacity reservation when used in a specific Availability Zone. For critical applications that need to run 24/7 without interruptions, Reserved Instances are the most cost-effective and reliable option. Spot Instances could be terminated, making them unsuitable for critical applications.
</details>

### Question 3
What is the correct order of operations to properly terminate a Spot Instance?
- A) Terminate the Spot Instance first, then cancel the Spot request
- B) Cancel the Spot request first, then terminate the Spot Instance
- C) Only terminate the Spot Instance; the Spot request will automatically be canceled
- D) Only cancel the Spot request; the Spot Instance will automatically be terminated

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Cancel the Spot request first, then terminate the Spot Instance**  
  The correct order is to first cancel the Spot request and then terminate the Spot Instance. If you terminate the instance first without canceling the request, the Spot request might still be active and could launch new instances if it's a persistent request.
</details>

### Question 4
Which of the following Spot Fleet allocation strategies is recommended for most workloads?
- A) Lowest price
- B) Diversified
- C) Capacity Optimized
- D) Price-Capacity Optimized

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: D) Price-Capacity Optimized**  
  The Price-Capacity Optimized strategy is recommended for most workloads because it selects pools with the highest capacity availability first (reducing the risk of interruptions), and then selects the lowest price from those pools (optimizing cost).
</details>

### Question 5
Which EC2 instance type would be most suitable for a database application that processes large amounts of data in memory?
- A) Compute Optimized (C)
- B) Memory Optimized (R)
- C) Storage Optimized (D)
- D) General Purpose (T)

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Memory Optimized (R)**  
  Memory Optimized instance types like the R family are designed specifically for workloads that process large datasets in memory, such as high-performance databases, distributed memory caches, and real-time big data analytics.
</details>

### Question 6
A company needs to use its existing SQL Server licenses that are bound to specific physical cores. Which EC2 option should they choose?
- A) On-Demand Instances
- B) Dedicated Instances
- C) Dedicated Hosts
- D) Reserved Instances

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Dedicated Hosts**  
  Dedicated Hosts provide a physical server dedicated entirely to your use, allowing you to use your existing server-bound software licenses, including those with licensing based on physical cores/sockets. This is ideal for software like Windows Server, SQL Server, or SUSE Linux Enterprise Server that have licensing tied to physical hardware.
</details>

### Question 7
Which EC2 placement group type would you use for an application that requires the lowest network latency possible between instances?
- A) Spread
- B) Partition
- C) Cluster
- D) Distributed

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Cluster**  
  Cluster placement groups pack instances close together within a single Availability Zone, providing the lowest network latency and highest network throughput between instances. This is ideal for high-performance computing (HPC) applications that require low-latency communication between nodes.
</details>

### Question 8
What is the key difference between Compute Savings Plans and EC2 Instance Savings Plans?
- A) Compute Savings Plans only apply to EC2, while EC2 Instance Savings Plans apply to EC2, Fargate, and Lambda
- B) Compute Savings Plans offer a higher discount than EC2 Instance Savings Plans
- C) EC2 Instance Savings Plans apply to specific instance families in a region, while Compute Savings Plans are more flexible
- D) Compute Savings Plans require a 3-year commitment, while EC2 Instance Savings Plans require only a 1-year commitment

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) EC2 Instance Savings Plans apply to specific instance families in a region, while Compute Savings Plans are more flexible**  
  Compute Savings Plans provide flexibility across instance family, size, AZ, region, OS, or tenancy. In contrast, EC2 Instance Savings Plans apply to a specific instance family in a region but still offer flexibility in terms of instance size, AZ, and OS within that family. Compute Savings Plans also apply to Fargate and Lambda usage, offering greater versatility.
</details>

### Question 9
Which feature of Spot Instances allows you to request them for a specified duration with minimal risk of interruption?
- A) Spot Fleet
- B) Spot Block
- C) Reserved Spots
- D) Spot Reservation

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Spot Block**  
  Spot Block is a feature that allows you to request Spot Instances for a specified time frame (1-6 hours) with minimal risk of interruption. While AWS notes that these instances may still be reclaimed, it happens very rarely, making them suitable for short-duration tasks that need to complete within a specific timeframe.
</details>

### Question 10
A company has a batch processing workload that needs to run for 4 hours and is cost-sensitive but cannot be interrupted. What is the most cost-effective EC2 pricing option?
- A) On-Demand Instances
- B) Spot Instances with high maximum price
- C) Spot Block for 4 hours
- D) Reserved Instances with partial upfront payment

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Spot Block for 4 hours**  
  Spot Block allows you to request Spot Instances for a specified duration (1-6 hours) with minimal interruption risk. For a 4-hour batch job that cannot be interrupted, Spot Block provides the best balance of cost savings (similar to regular Spot pricing, which is up to 90% off On-Demand) and reliability for the specific duration needed.
</details>

### Question 11
Which payment option for Reserved Instances offers the largest discount?
- A) No Upfront
- B) Partial Upfront
- C) All Upfront
- D) They all offer the same discount

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) All Upfront**  
  All Upfront payment for Reserved Instances offers the largest discount. With this option, you pay for the entire term (1 or 3 years) at the beginning, which maximizes your savings compared to Partial Upfront or No Upfront payment options.
</details>

### Question 12
A company wants to ensure their EC2 instances are distributed across different hardware to minimize the impact of hardware failures. Which placement group should they use?
- A) Cluster
- B) Spread
- C) Partition
- D) Availability Zone

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Spread**  
  Spread placement groups strictly place a small group of instances across distinct underlying hardware to reduce the risk of simultaneous failures. Each instance in a spread placement group is placed on different hardware, making it ideal for applications where maximum availability is critical and you want to isolate each instance from the failure of others.
</details>

### Question 13
What is the primary advantage of the "Diversified" allocation strategy in Spot Fleet compared to the "Lowest Price" strategy?
- A) It always provides instances at a lower cost
- B) It improves availability by distributing instances across multiple pools
- C) It offers more instance types
- D) It guarantees capacity for a longer period

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) It improves availability by distributing instances across multiple pools**  
  The Diversified allocation strategy allocates Spot Instances across multiple Spot pools (different instance types and Availability Zones), improving availability for long-running workloads. If one pool becomes unavailable or experiences price increases, only a portion of your fleet is affected, providing better overall resilience compared to the Lowest Price strategy.
</details>

### Question 14
Which EC2 instance type is best suited for applications with short-term, unpredictable workloads that cannot be interrupted?
- A) Reserved Instances
- B) Spot Instances
- C) On-Demand Instances
- D) Dedicated Hosts

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) On-Demand Instances**  
  On-Demand Instances are ideal for short-term, unpredictable workloads that cannot be interrupted. They provide flexibility with no long-term commitments, allowing you to use compute capacity as needed and terminate when finished. While they are more expensive than other options, they ensure capacity is available when you need it without risk of interruption.
</details>

### Question 15
What happens if the current Spot price exceeds your maximum Spot price for a running Spot Instance?
- A) The instance continues to run at your maximum price
- B) The instance is immediately terminated
- C) The instance continues to run, but you're charged the current Spot price
- D) You receive a 2-minute warning, after which the instance is stopped or terminated

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: D) You receive a 2-minute warning, after which the instance is stopped or terminated**  
  When the current Spot price exceeds your maximum price, AWS provides a 2-minute warning notification. During this period, you can gracefully handle the interruption by saving your work, uploading logs, or completing processing. After the 2-minute grace period, the instance is either stopped or terminated based on your specified interruption behavior.
</details>

### Question 16
Which of the following is NOT a valid type of Reserved Instance?
- A) Standard
- B) Convertible
- C) Scalable
- D) Scheduled

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Scalable**  
  There are three types of Reserved Instances: Standard, Convertible, and Scheduled. "Scalable" is not a valid type of Reserved Instance. Standard RIs offer the highest discount but cannot be changed. Convertible RIs allow you to change instance attributes for flexibility. Scheduled RIs are reserved for specific time windows.
</details>

### Question 17
A company needs to run a memory-intensive application for 9 months. Which pricing model would be most cost-effective?
- A) On-Demand Instances
- B) 1-year Reserved Instance
- C) Spot Instances
- D) 3-year Reserved Instance

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) 1-year Reserved Instance**  
  For a 9-month commitment, a 1-year Reserved Instance would be the most cost-effective option. While you'd pay for 3 extra months, the discount (up to 72% compared to On-Demand) would likely make it more economical than On-Demand for 9 months. Spot Instances would be cheaper but risky for a continuous 9-month workload due to potential interruptions.
</details>

### Question 18
What EC2 instance type would be most appropriate for a high-performance computing workload that requires significant parallel processing capabilities?
- A) General Purpose (M)
- B) Memory Optimized (R)
- C) Compute Optimized (C)
- D) Storage Optimized (D)

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Compute Optimized (C)**  
  Compute Optimized instances (C family) are designed specifically for compute-intensive workloads like High-Performance Computing (HPC) applications. They feature high-performance processors with high CPU-to-memory ratios, making them ideal for batch processing, scientific modeling, distributed analytics, and compute-intensive simulations that require substantial processing power.
</details>

### Question 19
Which of the following statements about EC2 Placement Groups is FALSE?
- A) A cluster placement group can span multiple Availability Zones
- B) A spread placement group can span multiple Availability Zones
- C) A partition placement group divides each group into logical segments called partitions
- D) Instances in the same cluster placement group enjoy higher per-flow throughput limits

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: A) A cluster placement group can span multiple Availability Zones**  
  Cluster placement groups cannot span multiple Availability Zones; they are limited to a single AZ. This limitation exists because cluster placement groups are designed to pack instances close together for low-latency, high-throughput network performance, which requires instances to be in the same physical proximity. Both spread and partition placement groups can span multiple AZs.
</details>

### Question 20
A company is running a critical database on EC2 and wants to ensure it has consistent performance and dedicated capacity. However, they don't need physical isolation at the server level. What's the most appropriate and cost-effective solution?
- A) Dedicated Hosts
- B) Dedicated Instances
- C) On-Demand Instances with high priority
- D) Reserved Instances in a Dedicated Tenancy

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Dedicated Instances**  
  Dedicated Instances run on hardware dedicated to a single customer, providing isolation at the hardware level, but don't give you control or visibility into physical servers. They're more cost-effective than Dedicated Hosts while still providing isolation required for compliance or licensing needs. Dedicated Hosts would provide physical server-level control but at a higher cost. There's no concept of "high priority" On-Demand Instances.
</details>

### Question 21
Which EC2 Purchasing Option offers the best cost benefits for fault-tolerant applications like big data processing frameworks that can handle interruptions?
- A) Reserved Instances with Partial Upfront payment
- B) Spot Instances
- C) On-Demand Instances
- D) Savings Plans

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Spot Instances**  
  Spot Instances are ideal for fault-tolerant applications like Hadoop, Spark, or containerized microservices that can handle interruptions gracefully. They offer the highest discount (up to 90% compared to On-Demand pricing) and are perfect for distributed big data processing frameworks that can checkpoint progress and resume from interruptions, making them extremely cost-effective for these specific workloads.
</details>

### Question 22
What should you use to control traffic in and out of EC2 instances?
- A) Network Access Control Lists (NACLs)
- B) Security Groups
- C) Route Tables
- D) Internet Gateways

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Security Groups**  
  Security Groups act as a virtual firewall that controls the traffic to and from EC2 instances. They operate at the instance level and can specify which protocols, ports, and source IP ranges are allowed to reach your instances. Security Groups are stateful, meaning that return traffic is automatically allowed, regardless of any outbound rules.
</details>

### Question 23
How long can you reserve an EC2 Reserved Instance?
- A) 6 months or 1 year
- B) 1 year or 3 years
- C) 2 years or 4 years
- D) Any duration from 1 month to 3 years

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) 1 year or 3 years**  
  AWS offers Reserved Instances for either 1-year or 3-year terms. These are the only two term options available. Longer term commitments (3 years) typically offer higher discounts compared to shorter terms (1 year).
</details>

### Question 24
You would like to deploy a High-Performance Computing (HPC) application on EC2 instances. Which EC2 instance type should you choose?
- A) General Purpose (M)
- B) Memory Optimized (R)
- C) Compute Optimized (C)
- D) Storage Optimized (D)

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Compute Optimized (C)**  
  Compute Optimized instances (C family) are designed specifically for compute-intensive workloads like High-Performance Computing (HPC) applications. They feature high-performance processors with high CPU-to-memory ratios, making them ideal for batch processing, scientific modeling, distributed analytics, and compute-intensive simulations that require substantial processing power.
</details>

### Question 25
Which EC2 Purchasing Option should you use for an application you plan to run continuously for one year?
- A) On-Demand Instances
- B) Spot Instances
- C) Reserved Instances
- D) Dedicated Instances

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Reserved Instances**  
  Reserved Instances provide significant cost savings (up to 72% compared to On-Demand) for applications with predictable usage that will run continuously over an extended period. For a one-year continuous workload, a 1-year Reserved Instance term would be the most cost-effective option, providing capacity reservation and lower hourly rates.
</details>

### Question 26
You are preparing to launch an application that will be hosted on a set of EC2 instances. This application has unpredictable usage patterns with occasional spikes in traffic. Which purchasing option is most appropriate?
- A) Reserved Instances
- B) Spot Instances
- C) On-Demand Instances
- D) Dedicated Hosts

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) On-Demand Instances**  
  On-Demand Instances are ideal for workloads with unpredictable usage patterns or occasional spikes in traffic. They provide flexibility with no long-term commitment, allowing you to scale up or down based on actual demand. While they are more expensive per hour than other options, they ensure you have capacity when needed without paying for unused resources during periods of low demand.
</details>

### Question 27
Which EC2 Instance Type would be most suitable for running SAP HANA, an in-memory relational database management system?
- A) Compute Optimized (C)
- B) Memory Optimized (R, X)
- C) Storage Optimized (D)
- D) General Purpose (T)

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) Memory Optimized (R, X)**  
  Memory Optimized instance types in the R and X families are specifically designed for memory-intensive enterprise applications like SAP HANA. The X family, in particular, is optimized for in-memory databases with the highest memory-to-vCPU ratio in the EC2 portfolio. SAP HANA requires substantial memory resources to keep the entire database in memory for faster transaction processing and real-time analytics.
</details>

### Question 28
You have an e-commerce application with an OLTP database hosted on-premises. This application has seasonal spikes in demand. You want to migrate it to AWS. Which EC2 instance type would be most appropriate for the database?
- A) General Purpose (M)
- B) Memory Optimized (R)
- C) Storage Optimized (I)
- D) Compute Optimized (C)

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: A) General Purpose (M)**  
  General Purpose (M) instances provide a balance of compute, memory, and network resources, making them suitable for OLTP (Online Transaction Processing) databases. For most e-commerce applications with seasonal spikes, M-series instances offer a good balance of resources without over-provisioning in any specific dimension. If the database is particularly memory-intensive, R-series could be considered, but M-series is typically the default choice for most OLTP workloads.
</details>

### Question 29
Security Groups can be attached to only one EC2 instance. True or False?
- A) True
- B) False

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) False**  
  Security Groups can be attached to multiple EC2 instances. A single Security Group can be applied to thousands of instances, and each instance can have up to 5 Security Groups. This allows for efficient and consistent security management across multiple instances that require the same access controls.
</details>

### Question 30
You're planning to migrate on-premises applications to AWS. Your company must meet HIPAA compliance requirements for healthcare data processing. Which EC2 deployment option is required to maintain compliance?
- A) Spot Instances
- B) Dedicated Instances
- C) On-Demand Instances with encryption
- D) Any instance type with proper security configurations and encryption

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: D) Any instance type with proper security configurations and encryption**  
  For HIPAA compliance with healthcare data, AWS doesn't require specific instance purchasing models like Dedicated Instances. Instead, HIPAA compliance requires proper security controls including encryption of data (at rest and in transit), access controls, logging, and monitoring. Any EC2 instance type can be used for HIPAA-compliant workloads as long as these security requirements are properly implemented according to AWS's Business Associate Addendum (BAA) requirements.
</details>

### Question 31
You would like to deploy a database technology on an EC2 instance and the vendor license bills you based on the physical cores. Which EC2 purchasing option should you use?
- A) Reserved Instances
- B) On-Demand Instances
- C) Dedicated Hosts
- D) Spot Instances

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: C) Dedicated Hosts**  
  Dedicated Hosts provide visibility and control over the physical server, including the number of sockets and physical cores. This makes them appropriate for software with licensing that is tied to the physical characteristics of the underlying hardware, such as databases that bill based on physical cores. With Dedicated Hosts, you can track and manage these licenses more effectively.
</details>

### Question 32
Spot Fleet is a set of Spot Instances and optionally...
- A) Reserved Instances
- B) On-Demand Instances
- C) Dedicated Hosts
- D) Burstable Instances

<details>
  <summary>Click to reveal answer</summary>
  
  **Answer: B) On-Demand Instances**  
  A Spot Fleet is a set of Spot Instances and optionally On-Demand Instances. Spot Fleet attempts to launch the number of Spot and On-Demand instances to meet the target capacity that you specified in the Spot Fleet request. This combination provides flexibility to ensure capacity requirements are met while maximizing cost savings.
</details>