---
title: "Storage"
---


## AWS DataSync
- Synchronize data - hourly daily weekly
- S3, EFS, FSx for Windows File Server
- SMB/NFS compatible file systems
- EFS <-> EFS

## Snowball
- Petabyte-scale data transport solution using secure appliances - import/export S3
- 50TB or 80TB
- Tamper resistant, 256 encryptions, Trusted Platform Module
- Snowball Edge - 100TB w/ storage and compute capabilities
- Snowmobile - Exabyte scale - 100PB, 45 foot long ruggedized shipping container

## Storage Gateway
- On-prem virtual / physical device for transfer into AWS
- ESXI or Hyper-V
- File Gateway - NFS & SMB connections. data stored in s3
  - All the options of S3
- Volume Gateway - iSCSI. 
  - Stored Volumes - store locally, backup to AWS async s3 as EBS snapshots
  - Cached Volumes - s3 primary data storage, retain frequently accessed data locally in storage gateway on premises
- Tape Gateway - Virtual Tape Library (VTL)
