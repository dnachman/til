---
title: "EC2"
---

## General

- On Demand - by hour/second
- Windows instances billed by hour/partial hour
- Reserved - 1 or 3 yrs. predictable usage
    - Standard RI - up to 75% - can’t move across regions
    - Convertible RI - up to 54% - change type
    - Scheduled RI - schedule that only requires only fraction of day - NOT AVAILABLE
- Spot - bid. Flexible start/end times. Charged for partial hour if you stop. NOT charged for partial hour if AWS terminates
- Dedicated - license/regulatory restrictions
- Termination Protection is turned off by default
- EBS backed instances - default is to terminate volume on termination
- EBS Root Vols can be encrypted
- Hypervisors : Xen (originally) and Nitro
- Hibernate - saves the instance ram to EBS root volume. data volumes reattached and instance id is retained. Root volume must be encrypted. RAM less than 150GB. Win/AML2/Ubuntu. Not more than 60 days of hibernation
- Instance metadata - curl
    - http://169.254.169.254/latest/user-data (bootstrap script)
    - http://169.254.169.254/latest/meta-data/public-ipv4
- Placement Groups
    - Clustered - single AZ close together - low net latency, hi throughput - only certain instances - recommend homogeneous instances
    - Spread - multiple AZ - placed on distinct underlying hardware (rack) - small number of critical instances that should be kept separate from each other - THINK INDIVIDUAL INSTANCES - protect from HW failure. 7 Running Instances per availability zone
    - Partitioned - Similar to spread but can be multiple instances - each partition in the group has it’s own set of racks - THINK MULTIPLE INSTANCES - HDFS, HBase, Cassandra
    - Name of placement group must be unique to your account
      - Can move instance into Placement Group via CLI/SDK but must be in stopped (not console yet)

## High Performance Compute (HPC)

- Data Transfer
    - snowball, snowmobile (tb/pb of data)
    - AWS DataSync
    - Direct Connect
- Compute / Network
    - GPU or CPU optimized
    - Fleets
    - Placement groups (cluster placement groups)
    - Enhanced networking - ENI, ENA, EFA
- Storage
    - EBS / Instance Store
    - S3 / EFS
    - FSx for lustre
- Orchestration / Automation
    - AWS Batch - multi node parallel jobs that span multiple EC2
    - AWS ParallelCluster - Open source - automation

## AWS WAF - Layer 7 aware firewall

- web application firewall to monitor http(s) to CloudFront, ALB, Api Gateway
- Behaviors:
    - Allow all except specified
    - Block all except specified
    - Passive - count requests that match properties
- IP address / Country origination / Values (regex) / SQL Code / malicious scripts
- One way to bock malicious IP addresses (along w/ network ACLs)

## Security Groups

- All inbound traffic is blocked by default. All Outbound traffic is allowed.
- Rules take effect immediately
- Security groups are stateful (inbound rule for http in, automatically creates outbound)
- Can’t block individual IP addresses or ports (will be able to in network ACL vpc) - no deny rules
- Can add more than 1 security group to EC2 instances

## EBS

- Elastic block store - virtual HD in cloud
- General Purpose SSD - gp2
- Provisioned IOPS SSD - io1
- Throughput Optimised HDD - st1
- Cold HDD - sc1
- Magnetic - Standard
- HDD based options are cheaper
- Volumes will be in same AZ as the instance
- Volumes exist on EBS, Snapshots exist on S3
- To move a VM -> Snapshot EBS vol; Create Image; Launch Instance
- Can move a AMI to another region
- Snapshots are incremental (only parts that change)
- Stop snapshot for root device while stopped

## EFS - Elastic File System

- Automatically grow/shrink - only pay for what you use
- Share across instances, etc
- Uses default VPC security group
- NFSv4
- Scales to Petabytes; thousands of concurrent NFS conx
- Multi AZ in a region. Read after write consistency
- Linux only (EC2 windows can’t connect)

## Amazon FSx

- For Windows File Server
    - SMB based
    - Supports AD users, ACL, groups, security pols, along w/ Distributed File System namespaces and replication
- For Lustre
    - Compute intensive workloads for HPC, ML, Media, financial modeling, EDA
    - Up to hundreds of GB per second throughput, millions IOPS, sub-millisecond latencies

## AMIs

- Backed by EBS or instance store
- EBS Snapshot to load it - can’t delete the EBS Snapshot w/o first deleting the AMI it uses
- Instance store -> template stored in S3. Ephemeral storage
    - Can only add add’l ones at launch - not after.
    - Can’t stop the instance - only reboot
    - If underlying host fails, you lose your data
- Encrypted Root Device
    - Can be done at creation time
    - To convert unencrypted volume to encrypted -> Take Snapshot; Copy snapshot select encrypted + key; Create Image (AMI) from encrypted snap; launch;
    - Can share snapshots only if unencrypted

## ENI vs. ENA vs. EFA

- Elastic network interface - virtual nic
    - Create management network
    - Network security appliances
    - Low budget HA
- Enhanced Networking - single root i/o virtualization SR-IOV for hi perf on supported instances
    - Higher IO and lower CPU
    - No additional charge, but instance must support it
    - ENA - up to 100Gbps (ENA is better than VF if given the option)
    - Intel 82599 Virtual Function (VF) up to 10Gbs on older instances
- Elastic Fabric Adapter - accelerate HPC and ML
    - lower and more consistent latency and higher throughput
    - can use OS-bypass - apps can communicate direct to adapter at lot lower latency (linux only)

## Spot Instances & Spot Fleets
- Up to 90% savings. Provisioned as long as below your spot price
- No more bidding
- If goes above, have 2 minutes to decide
- Spot blocks - hold for several hours
- Useful for following workloads:
    - Big data and analytics
    - Containerized workloads
    - CI/CD and testing
    - Web services
    - Image and media processing
    - HPC
- Request: Max price, num instances, launch specification, request type (1 time or persistent), valid from/to
- Spot Fleets - spot instances and on-demand instances to meet target capacity
    - Different/multiple pools
    - Strategies for pools: capacityOptimized, lowestPrice (default), diversified, InstancePoolsToUse

## AWS Config

- Create rules on configurations
- E.g. if tagged then make sure using a specific ami
