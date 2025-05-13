# EBS Snapshots and Backups

## Overview
Amazon EBS snapshots are point-in-time copies of your EBS volumes that can be used to transfer data to new Availability Zones (AZs) or regions. They serve as an essential mechanism for data backup, migration, and disaster recovery.

## Key Features

### 1. Data Migration
- EBS snapshots allow you to transfer data to new Availability Zones or regions
- Useful for geographic expansion or disaster recovery planning

### 2. Storage Tiers
- **Archive Tier**: 75% more cost-effective than standard storage
  - Restoration time: 24-72 hours
  - Best for long-term retention and rarely accessed backups

### 3. Data Protection
- **Recycle Bin**: Protection mechanism for deleted snapshots
  - Retention period: Configurable from 1 day to 1 year
  - Enables recovery of accidentally deleted snapshots

### 4. Performance Optimization
- **Fast Snapshot Restore (FSR)**: Eliminates latency when creating volumes from snapshots
  - Pre-initializes the entire snapshot for immediate use
  - Higher cost but provides optimal performance when neededapshot make transfer data to new AZ