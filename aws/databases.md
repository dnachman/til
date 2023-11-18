---
title: "Databases"
---

## AWS Databases
- Relational Database Service RDS
  - Oracle, SQL Server, MySQL, PostgreSQL, Aurora, MariaDB
  - Multi-AZ - for disaster recovery - exact copy in another AZ, AWS will change the endpoint during failover - no intervention (not available for Aurora b/c it’s fault tolerant by design). no charge for data transfer. Can use Reserved DB Instances
  - Read Replicas - for performance - async replication from primary. RR can be promoted to standalone DB (breaks replication)
    - Not for DR
    - Must have automatic backups turned on 
    - Up to 5 read replicas (but an of RR of RR)
    - Each RR has its own DNS endpoint
    - RR can be Multi-AZ
    - Can be in a second region
  - Not serverless (except Aurora)
  - Runs on VMs - can’t log in - Amazon responsible for patching
  - Automated Backups are enabled by default
    - Point in time recovery w/in retention period (1-35 days)
    - Full daily snapshots plus transaction logs throughout day
    - May experience latency during window
    - Deleted with the RDS db
  - DB Snapshots are done manually - stored even after you delete original RDS db
  - Restore an instance to a new RDS instance, which means new endpoint (change your apps)
  - Encryption at rest - KMS - backups, etc also
  - RDS for SQL Server max is 16TB when using Provisioned IPS and General Purpose (SSD) storage types
- Non relational NoSQL DynamoDB
  - Collection = table; Document = row; Key Value Pairs = fields
- Data Warehouse - Redshift
  - OLTP - online transaction processing
  - OLAP - online analytics processing
- ElastiCache - in memory cache
  - Redis
  - memcached 

## DynamoDB
- SSD storage - single digit millisecond latency - document and k-v data models
- Across 3 Geo-distinct data
- Eventual consistent reads (default = ~1sec) vs. strongly consistent reads
- Accelerator - DAX : in-memory cache. Can write-through cache
  - Fully managed, HA. 10x performance improvement
  - Request time down to microseconds
- Transactions - 
  - Multiple “all or nothing” ops
  - 2 underlying reads/write - prepare/commit
  - Up to 25 items or 4MB of data
- On-Demand Capacity - pay per request pricing (balance cost and performance) - pay more per request than provisioned
- On-Demand Backup and Restore - full backup at any time, zero impact on perf or availability. Consistent w/in seconds and retained until deleted
- Point in Time Recovery (PITR) - protect against accidental writes or deletes, up to 35 days, incremental backups, not enabled by default. Latest restorable = 5 mins in past
- Stream - time-ordered sequence of item-level changes - stored 24 hrs. Inserts, updates, deletes. Combine w/ lambda’s to simulate stored procedures
- Global Tables - managed multi-master, multi-region replication - based on streams, multi-region redundancy for DR or HA. No application requires. Replication latency under one second. 
- Database Migration Service (target, not source)
- Encrypted at rest using KMS. Site to Site VPN, Direct Connect (DX), IAM, Fine-grained access, VPC endpoints, Cloudwatch and CloudTrail
- Strongly Consistent Reads can be used but may have higher latency
- DynamoDB allows for the storage of large text and binary objects, but there is a limit of 400 KB.

## Redshift
- Fully managed petabyte-scale data warehouse
- no commitments/upfront costs - 0.25/hr -> 1K per TB per year
- Single Node (160Gb)
- Multi-Node
  - Leader Node - manages client conx and receives queries
  - Compute Node - store data and perform queries and computations (up to 128 compute nodes) 
- Advanced Compression - columnar data stores can be compressed much more than row-based. doesn’t require materialized views, etc
- Massively parallel processing
- Backups - enabled by default w/ a 1 day retention (max 35)
  - attempts to maintain at least 3 copies of data (original, replica on compute nodes, and backup in s3)
  - async replicate your snapshots to s3 in another region for DR
- Charges - compute node only - 1 unit per node per hour + backup + data transfer w/in VPC
- Security - transit + rest. KMS but can use HSM
- Currently only available in 1 AZ
- Can restore snapshots in another AZ

## Aurora
- MySQL & PostgreSQL compatible
- Up to 5x perf over MySQL; 3x over PGSQL
- 10GB, scale in 10GB increments to 64TB
- Compute can scale to 32vcpu and 244 gb memory
- 2 copies of data in each AZ, minimum 3 az = 6 copies of your data
- Transparently handle the loss of up to 2 copies of data w/o affecting write avail. up to 3 copies w/o affecting read availability
- Self healing - data blocks and disks are scanned for errors and repaired automatically
- Aurora Replicas - max 15 - in region replications
- Mysql Read Replicas - max 5
- PGSQL read Replicas - max 1
- Backups are always enabled. Snapshots can be shared w/ other AWS accounts.
- Aurora Serverless - automatically starts/stops/scales - for infrequent, intermittent or unpredictable workloads

## ElastiCache
- In memory cache in the cloud - increase DB and web application performance
- memcached - simple
- Redis - pub sub, complex data types, backup and restores, multi-az

## Database Migration Service (DMS)
- migrate to cloud, between on-premises, combos
- Server that runs replication software - source/target
- Will create tables and primary keys, but can pre-create manually or use AWS Schema Conversion Tool (SCT) needed for heterogeneous
- Supports homogenous / heterogeneous migrations
- Sources - usual DBs, Azure SQL, RDS, S3
- Targets - usual DBs, RDS, Redshift, DynamoDB, S3, Elasticsearch, Kinesis data streams, DocumentDB

## Caching Services
-  CloudFront
- API Gateway
- ElastiCache - Redis / memcached
- DynamoDB Application Accelerator - DAX

## Amazon EMR
- Elastic Map Reduce
- Spark, Hive, HBase, Flink, Hudi, Presto
- Petabyte-scale analysis at 1/2 cost of traditional on-prem and 3x faster than Apache Spark
- Cluster - nodes w/ roles
  - Master node - status of tasks & monitors health - every cluster has a master node
  - Core node - runs tasks and stores data in Hadoop Distributed File System (HDFS) - multi-node clusters have at least one core node
  - Task node - only runs task and does NOT store data in HDFS - optional
- Log files stored on master node at /mnt/var/log
  - configure to archive logs to S3 at 5-minute intervals in case of normal shutdown or error
  - must be done when first set up cluster
