# AWS Certified Data Analytics Specialty DAS-C01 Cheat Sheet

## Exam Format

* 50 questions MCQ (scored) + 15 unscored questions
* 180 minutes
* Pass/fail grade, need ~75% correct to pass
* No penalty for guessing
* Domains
    * Collection — 18%
    * Storage & Data Management — 22%
    *  Data Processing — 24%
    * Analysis and Visualization — 18%
    * Security — 18%

## Exam Material

### Simple Storage Service (S3)

* Can upload files into S3 through console, CLI, and SDKs
* Problem: Users geographically away from the S3 bucket suffer from higher download and upload latency
    * Download-side solution: **CloudFront**
        *  CloudFront’s global CDN uses edge locations to cache frequently accessed data closer to the end-user
    * Upload-side solution: **Transfer Acceleration** 
        * Transfer Acceleration uses CloudFront’s globally distributed edge locations to route uploads to S3 over an optimized network path
            * Must be enabled per bucket
            * Uses special endpoints: bucketname.s3-accelerate.amazonaws.com, [bucketname.s3-accelerate.dualstack.amazonaws.com](http://bucketname.s3-accelerate.dualstack.amazonaws.com/)
            * Additional cost — $0.04/GB for the US, Europe, and Japan, $0.08/GB for all other AWS edge locations
* Multipart Uploads
    * Moving large files all at once is impractical, time consuming, and expensive
        * Single S3 put only allows 5GiB, but an S3 object can be up to 5TiB, so large files must be broken down to smaller chunks for upload
    * Three API calls
        * CreateMultipartUpload — returns bucket, key, and upload id
        * UploadPart — provide bucket, key, part number, and upload id; returns an ETag
        * CompleteMultipartUpload — provide bucket, key, upload id, all part numbers, and ETags; returns bucket, ETag, and key
    * Considerations
        * Max 10,000 parts, each part (except final) must be atleast 5MiB
        * Recommended to use MultipartUpload for files larger than 100MiB
        * Parts can be overwritten during upload incase of updates or failure
        * Configure lifecycle policy to auto-abort the multipart upload after a specified time period, incase of failure
* S3 Storage Classes 
    * Design
        * Durability (data persistence) — 99.9999999% (11 nine’s)
        * Availability (data uptime) — 99.5% - 99.99% (2-4 nine’s depending on storage class)
    * Infrequent access and archival storage classes are less expensive storage solutions, but add an additional write charge and retrieval charge
    * Storage Classes (ordered from most expensive to least expensive)
        * Standard — General purpose, data that is regularly accessed
        * Intelligent Tiering — Unknown access pattern
        * Standard Infrequent Access — Weekly/monthly access
        * One-Zone Infrequent Access — Easily replaceable data
        * Glacier — Archive, acceptable minutes to hours of retrieval time
        * Glacier Deep Archive — Archive, acceptable hours of retrieval time
    * Objects smaller than 128KB are billed as if they are 128KB objects, so best to bundle smaller files into minimum 128KB filesize
    * Lifecycle policies allow you to set rules to move data to archival storage classes after X days
        * Written in JSON or XML or using the Wizard in the console
        * Filter — which data are is the lifecycle policy referring to
        * Transition — when data should be moved from one storage class to another
        * Expiration — when data should be deleted
* S3 Security and Encryption
    * Encryption
        * In-Transit
            * Mandatory client TLS support is required
                * TLS 1.2 and above recommended, but all S3 clients are required to support at least TLS 1.0
                * Clients must support Perfect Forward Secrecy (PFS) cipher suites
                    * Ephemeral Diffie-Hellman (DHE)
                    * Elliptic Curve Diffie-Hellman Ephemeral (ECDHE)
            * VPC endpoints to limit bucket access to within VPC
                * Data travels over private secure connection to private subnets, not over public internet
            * Multiple VPN options for access from outside VPC
                * Options: Build own VPN, AWS Site-to-Site VPN, or Direct Connect
            * Access control and auditing
                * Access Control (from least to most granular)
                    * Service Control Policies (dddOrganization-level)
                    * IAM — Users, Roles, Policies
                    * Bucket ACL
                    * Bucket Policies
                    * Object ACL
                    * Object Policy
                * Auditing
                    * CloudTrail logs
                    * S3 server access logs
                    * Access Analyzer for S3
        * At-Rest
            * Client-Side Encryption (through SDKs)
                * Customer Master Key (CMK) in Key Management Service (KMS)
                    * Application requests object from S3 bucket, S3 returns encrypted object along with a cipher blob for the encryption key
                    * Application then makes a KmsDecrypt API call to pass the cipher blob to KMS, which returns the plaintext data key to decrypt S3 object
                * Application-Stored Key
            * Server-Side Encryption (using KMS/S3)
                * S3 Managed Encryption Keys (SSE-S3)
                    * Application server requests object, S3 automatically decrypts the object and sends it back
                * CMK stored in KMS (SSE-KMS)
                    * Application server requests object, S3 automatically fetches decryption key from KMS, decrypts object, and sends it back
                * Customer-provided Encryption Keys (SSE-C)
                    * Customer provides keys and exchange is handled by the application code, so decryption is not automatic. S3 just stores and sends the encrypted object when it is requested
    * Object Protection
        * Locking — write-once read-many mode (WORM)
        * Multi-factor authentication (MFA) Delete
        * Versioning — can rollback to a prior version even after “deletion”
    * Replication
        * Cross-region replication
            * Replicates S3 bucket in a bucket in a different region
                * Other bucket will need to have a unique/different name
            * Will need to set a automatic failover in application code to point to replication bucket if original region goes down
            * Must enable versioning to turn on cross-region replication to prevent overwriting
* S3 FAQs
    * S3 best-fit use-cases
        * Store large amounts of frequently-accessed data at low cost
        * Congregate data over a short interval (buffering)
        * Cloud-local cache for on-prem data (Single-zone IA)
        * Easy static website hosting
        * Archiving — tiered automatic lifecycle archiving
            * Long-term archive
            * Archiving source code
    * S3 limits
        * No hard limit on how much data a bucket can hold
        * Single object can be up to 5TB
        * Single put upload is max 5GB, so we used multi-part uploads for bigger files
    * S3 security and compliance overview
        * S3 is ideal for security compliance standards like HIPAA
        * Implements in-flight and at-rest encryption
        * Excellent data archival capability
        * GovCloud region provides ITAR compliance

### Databases

* Database Engines
    * Relational engines
        * Data normalization — storing data once and referencing it in other tables
        * Tabular (row-based) table access — Aurora, MySQL, MariaDB, Oracle, SQL Server
            * Used for Online Transaction Processing (OLTP)
                * Used for rapid transaction, protecting data through transaction rollback
                * All-or-nothing architecture
                * Ideal for low-latency applications
        * Columnar (column-based) table access — Aurora, PostgreSQL, Oracle, Redshift
            * Used for Online Analytics Processing (OLAP)
                * Used for analytics workloads that manage large amounts of data
                * Handle complex long-running query operations
                * OLTP databases are often a data source in analytics workflows
    * Non-relational engines
        * Key-value
            * Elasticache (Memcached & Redis)
                * Data is stored in memory
                * Fast but fragile
                    * Memory access is much faster than disk access
                    * But all data can be lost if there is a hardware failure
                * Redis has disk persistence, but at the cost of speed
            * DynamoDB
                * Flexible and inflexible, can be used in key-value or document style databases
                * Data model dictates style
                * Table structure can’t be changed after creation
                * Primary key
                    * Partition key should be unique identifier, sort key is optional
                    * Partition key + sort key can make a composite primary key
                    * Need it to query the table
                * Secondary Indexes can be used to query tables using alternate keys
                    * Global Secondary Index — different partition key than table
                    * Local Secondary Index — same partition key as table, but different sort key
        * Document
            * DynamoDB (see above)
            * DocumentDB
                * For structured or semi-structured document storage (JSON/YAML)
                * Metadata is stored with document records
                * Can hit critical scaling failures (more about this below)
        * Graph
            * Neptune
                * Relational+ / Hyperrelational
                    * Data is structured as nodes connected with edges (i.e. shared attributes between nodes)
                    * Can reveal otherwise hidden patterns in data
* Relational Database Service (RDS)
    * Managed service, so AWS is in charge of maintaining the OS and server
    * Database instance is configured through a Management API, and can run the following DB engines:
        * Amazon Aurora (MySQL/PostgreSQL)
        * MySQL
        * MariaDB
        * PostgreSQL
        * SQL Server
        * Oracle
    * “Second-level service” i.e. uses a bunch of other services under the hood, such as EC2, EBS, S3, and Route53
    * Since we don’t have operating system access, the DB engines are configured through Parameter Groups and Option Groups
        * Parameter Groups are used to manage engine parameters
            * Logging, Memory management, and other engine variables
        * Option Groups are used to manage plugins and plugin settings
            * Usage varies by engine: Not used at all by PostgreSQL, heavily used by Oracle
    * Uses Multi-AZ deployments for Disaster Recovery
        * Keeps a secondary instance dormant in a different AZ, and monitors the primary instance
            * Block level replication helps keep primary and secondary RDS instances in sync
        * If any part of primary instance fails, RDS automatically switches to the secondary instance, which becomes the primary
            * After the failover, RDS will create a new secondary instance in the original AZ
        * Single-AZ deployments are not recommended for production workloads
            * Multi-AZ failover takes roughly 60 seconds, whereas deploying a new RDS instance can take 5 minutes
* Neptune
    * Graph is comprised of nodes, and these nodes are connected by edges
        * Nodes contain data
        * Edges are common data points between nodes
    * Interface languages to traverse nodes across edges
        * Apache — Tinkerpop Gremlin
            * Graph Structure: Property
            * Interface: WebSocket
            * Query Pattern: Traversal
        * W3C — SPARQL Protocol and RDF Query Language
            * Graph Structure: Resource Description Framework (RDF)
            * Interface: HTTP REST
            * Query Pattern: SQL
    * Similarities to RDS
        * Fully-managed service
        * Parameter/Option Groups
        * Multi-AZ Deployments* — more like Aurora as replication happens at storage level; not engine level
        * Instance Monitoring
        * Snapshots/Backup
    * Used for mainly pattern recognition
        * Security — detecting fraud
        * Social Media — identify connections between users, or targeted advertising
        * Science — scientific modeling
* DocumentDB
    * Document store database management system, stores data in JSON
        * Able to index JSON data structures at scale (a failure point of MongoDB)
    * Fully managed service, and storage automatically scales upward
    * MongoDB compatible
    * Use cases
        * Social media profiles
        * Content management systems (CMS)
        * Object catalogues
* Serverless Database Options
    * S3 SelectObjectContent API Call
        * Available through CLI or SDKs
        * Reads data from CSV, JSON, or Parquet
        * Must be encoded in UTF-8 format
        * Compression must be either GZIP or BZIP2 for CSV and JSON
            * and only GZIP Columnar Compression for Parquet
        * Server-side encryption is supported, no client-side encryption
    * Athena
        * SQL interface for data stored in S3
        * Fully featured service, used for more complex queries than S3 Select
        * Runs Presto under the hood
        * Supports CSV, JSON, Parquet, ORC and Avro data formats
        * Service integrations with Glue and Quicksight
    * DynamoDB
        * Key-value and document store
        * Great for OLTP use-cases
        * Key features include
            * Local Secondary Indexes — use another attribute as primary key to query
            * Global Secondary Indexes
            * Streams
        * Service integrations with Lambda, DAX, and IAM
    * Aurora Serverless
        * Runs MySQL/PostgreSQL compatible versions for an “Infrequent Access” Aurora DB
        * Proxy sits infront of DB to handle incoming connection
        * Compute instances (Aurora Capacity Units or ACUs)
            * Provisioned when a user connects to the proxy, and on standby for queries
            * Runs two engines
                * MySQL — scales from 1-256 ACUs
                * PostgreSQL — scales from 2-384 ACUs
            * But for long-running events these scale events can fail, so not ideal for critical production systems
            * Ideal for infrequently run short-running queries
        * Storage Cluster
            * 10 GiB storage nodes, so virtually no IOPS limit
    * Databases FAQs
        * What services can manage data between these databases?
            * Database Migration Service (DMS), Glue, Elastic Map Reduce (EMR)
        * Other database engines not covered in this section 
            * Amazon Quantum Ledger Database (QLDB) — cannot delete a record, any changes are tracked, used for financial transactions
            * Amazon Timestream — timeseries data store, useful for IoT
            * Amazon Keyspaces — Managed Apache Cassandra (an analytics DB engine)

### Collecting Streaming Data

* What is streaming data?
    * Data loses decision-making value over time
    * Streaming data is fresh/realtime, and usually has the most decision-making value

* Amazon Kinesis Family Overview
    * Kinesis Data Streams — collect and process large streams of data records in realtime
    * Kinesis Data Firehose — deliver streaming data to various data sources
    * Kinesis Video Streams — stream live videos from devices to the cloud, and build realtime applications around these streams
    * Kinesis Data Analytics — run SQL queries in realtime over streaming data
* Kinesis Data Streams
    * Overview
        * Kinesis Data Streams aggregate realtime data and load it to other services like EMR/RedShift
        * Managed service thats durable (won’t lose data) and elastic (scales up or down)
        * Allows for parallel applications reading from the data streams, which differentiates it from queueing services
    * Process
        * Data producers (i.e. logs, social media streams, video streams, etc.) push data onto the Kinesis Data Streams
        * Shards package and aggregate the data and send it to Data Consumers
            * Default limit is 500 shards, but it can be increased
        * Data consumers are processing tools such as Kinesis Data Analytics, Kinesis Data Firehose, EMR, EC2, Lambda etc.
            * Can process, analyze or create dashboards to visualize the data
        * Storage
            * Storage isn’t necessary for streaming data as data gets less valuable over time
            * But different storage options are available, such as data lakes on S3, data warehousing on Redshift, or DynamoDB
    * Shards
        * Each shard consists of a sequence of data records, which can be ingested at 1000 records per second
        * All data within a shard shares the same partition key, and has a unique sequence number
        * Data payload per record can be up to 1MB, but most cases will be a few Kilobytes
            * 1 shard can process 1 MB/sec input data, and 2 MB/sec output data
            * 2 shards can process 2 MB/sec input data, and 2 MB/sec output data
            * and so on...
        * Scaling up shards is the way to scale Kinesis streams (e.g. add more shards if user activity increases)
        * Shards are transient data storage
            * Data records are stored for 24h by default, and can be extended up to 365 days
                * Extended data retention enables storage for 7 days
                * Long-term data retention enables storage for up to 365 days
    * Reading To/Writing From Kinesis Streams
        * Kinesis Producer Library (KPL) — Easy to use library to write to a Kinesis Data Stream
            * Uses Kinesis API under-the-hood, providing a layer of abstraction specific for ingesting data
            * Automatic and configurable retry mechanism
            * Processing delays can occur for higher packing efficiencies and better performance
            * Only usable with a Java wrapper
        * Kinesis Client Library (KCL) — Integrated with KPL for consumer applications to consume/process data from Kinesis Data Stream
        * Kinesis Agent — Pre-built Java application to be installed on Linux servers
        * Kinesis API (AWS SDK) — API operations to send data to Kinesis Data Stream using PutRecord or PutRecords
            * Uses PutRecords and GetRecords to manually handle streams
            * No delays in processing
            * Usable through any AWS SDK
    * Kinesis Data Firehose
        * Managed, server-less streaming application to load data into a few options (mostly other AWS services)
        * Receives records from multiple producers and can transform the data before storing or delivering to data consumers
            * Data producers can produce records as large as 1000KB
            * Can copy raw data to transform it in a Lambda function before storing in S3, while also storing raw data in a different bucket
            * To store in Redshift, data will always be stored in S3 first and then Firehose issues a COPY command to load data onto S3
            * Can store in Redshift, S3, ElasticSearch, Splunk, and to an HTTP Endpoint (so data can be indirectly sent to SNS/API Gateway as well)
        * Firehose buffers and compresses all data you push to it, causing a short delay
            * Buffer Interval — Ranges from 60 seconds to 900 seconds
            * Buffer Sizes
                * S3 — 1MB to 128MB
                * Elasticsearch — 1MB to 100MB
                * Redshift — depends on how fast your cluster can finish the COPY command
                * Splunk — 5MB buffer size, and 60s interval
        * Firehose Data Streams can be encrypted using KMS
    * Kinesis Video Streams
        * Uses Kinesis Video Stream Libraries 
            * Manage media sources and stream lifecycle as data flows from source to Kinesis Video Streams
            * Installed on-device to securely connect to Kinesis Video Streams
            * Can do real-time video analysis or batch-oriented video analysis after storing videos durably
    * Kinesis Data Analytics
        * Run SQL queries on streaming data
        * Can join streaming data with data in S3
        * Query output can be streamed to Kinesis Data Streams/Firehose and sent to other data consumers
* Amazon Managed Streaming for Kafka (MSK)
    * Apache Kafka — open source streaming data service, using ZooKeeper
        * Publish/subscribe to streams
        * Store streams in a durable way — Unlimited durability
        * Process streams as they occur — Very low latency
    * Amazon Managed Streaming for Kafka
        * With the managed service, you don’t need to:
            * Manage cluster
            * Ensure software updates
            * Ensure high availability
        * Structure
            * VPC
                * Broker Nodes
                    * Live in different availability zones (for high-availability)
                    * Contain streaming data
                    * Subscribed to by producer and consumer nodes outside the VPC
                * ZooKeeper Nodes — coordinate data between broker nodes
                    * Topic Creators
                    * Take care of different partitions
        * Comparisons to Kinesis Data Streams
            * Throughput provisioning (with Shards) or cluster provisioning
            * Seamless scaling for KDS, but MSK scaling is not seamless to client
            * KDS stores data for 7 days max, but MSK can for unlimited time
            * KDS has deep AWS integration, whereas MSK has 3rd party tooling
            * MSK is the most low-latency streaming solution
            * KDS has maximum payload of 1MB, whereas MSK provides configurable data payload sizes up to 6MB

### Ingesting Data

* AWS Direct Connect and AWS Snow Family
    * Moving mass amounts (GB/TB/PB) of data with minimal disruption, cost, and time
    * Managed tools to move data from on-premise to AWS network
    * Hybrid Cloud Storage
        * AWS Direct Connect
            * Direct Connect Location — connects 1 or 10Gbps lines directly to AWS region
                * Customer location connects into Direct Connect Location (physical cable) without the internet
                * Can also use hosted connections by AWS partners for connection speeds ranging from 50Mbps-10Gbps
                * Cost — billing for port hours, and data transfer out of AWS
    * Online Data Transfer — via internet (CLI, console, SDK)
        * AWS DataSync — one-time data migration, automate moving data between on-premise, S3, EFS, FSx etc.
        * FTP/SFTP/FTPS into and out of S3
        * S3 Transfer Acceleration — faster transfer of data into S3 over the internet
        * Kinesis Data Firehose — load streaming data into S3
        * AWS Snowcone — collect, process, and move data to AWS online through AWS DataSync
    * Offline Data Transfer
        * AWS Snow Family
            * Snowcone
                * Load data through WiFi or wired 10GbE networking, and ship data to AWS for offline data transfer
            * Snowball
                * Petabyte-scale data transport with import/export to S3
                * Snowball Edge
                    * Local storage and large-scale data transfer like Snowball
                    * Local compute — Lambda, EC2, and AWS IoT Greengrass
                        * Ideal for data processing / ML for places with poor internet connectivity
            * Snowmobile
                * Exabyte-scale data transport in a secure 40-foot shipping container
                * Solves security concerns, network costs, and transfer times for massive data transfer
* AWS Database Migration Service (DMS)
    * Easily and securely migrates databases and warehouses, by replicating data so databases stay fully operational during migration
        * Eases many common concerns like:
            * On-premises to cloud migrations
            * Commercial databases to open source database engine or S3 datalake migrations
            * Avoiding long periods of downtime for users
    * Scenarios/Use-cases
        * Upgrade versions of database software with no downtime
        * Archive old data in more cost efficient storage
        * Migrate business-critical databases, from classic EC2 to VPC, costly license driven data-warehouse to Redshift
        * Migrate databases from NoSQL to SQL, SQL to NoSQL, and SQL to SQL
    * Heterogenous and homogenous migrations
    * Can use a Snowball device to migrate large terabyte/petabytes of data
    * Replication — can be used for one-time data migration, or continuous replication
        * Change data capture — only replicate the changes
        * Cross region replication — Keep databases in-sync across regions to reduce latency
        * Offload Analytics to another database with replication
        * Keep data in-sync between testing, staging, and production environments
    * Cost
        * Pay for on-demand instances spun up at the start of migration process
        * Data transfer between DMS and databases in RDS/EC2 in the same AZ is free
        * Data transfer cost when data leaves an AZ, Region, or outside of AWS
* Amazon Data Pipeline
    * Move and process data between compute and storage services
    * Similar to EMR/Lambda, but more managed service to schedule ETL workloads
    * Pipeline Jargon
        * Data nodes — end destination for data
        * Activities — action that Data Pipeline initiates on your behalf
        * Preconditions — must be met before activity is ran
        * Schedules — define when/how many times the activities run on data
    * Can be used on-premise by installing a Installed Task Runner
        * Move tables between production database on RDS and non-prod db on-premise
        * Remotely execute stored procedures on-premise
* AWS Lambda
    * Event-driven service allowing code to run without managing infrastructure
    * Important Limits
        * 10x concurrent executions per region (for async non-AWS invocations, and all sync invocations)
        * 15-minute function timeout
        * 6MB invocation payload (for sync invocations), 256 KB (for async)
* Amazon API Gateway
    * Server-less API service for REST, HTTP, and WebSocket APIs
* Amazon CloudFront
    * CDN with edge locations that delivers data faster to end-users
* Comparing data migration options
    * 4 W’s and 1 H of Data Migration
        * Why are we migrating to AWS?
        * What data is being migrated? What applications are attached to it?
        * Where is the data going?
        * When does the data need to finish?
        * How much data do you have? How much network capacity do you have?
* FAQs about Data Migration
    *  How quickly can I migrate X amount of data?
        * Typically about 100 TBs in a week, but Snowmobile can transfer data at a rate of up to 1 TB/s, where 100 PBs can be migrated in a few weeks
    * Standard activities in Data Pipeline
        * CopyActivity — copy data between S3 and JDBC data sources
        * HiveActivity — execute Hive queries
        * EMRActivity — run EMR jobs
        * ShellCommandActivity — run arbitrary Linux shell commands/programs
    * Standard preconditions in Data Pipeline
        * Exists precondition — check if DynamoDBTable/DynamoDBData/S3Key/S3Prefix Exists
        * Shell command precondition — runs shell script on resources and checks if it succeeds

### Amazon Elastic Map Reduce (EMR)

* MapReduce
    * Algorithm for batch-processing large amounts of data
        * Batch workload is distributed evenly across many different nodes
        * Distributed File Systems are used to split files and replicate data across several nodes
            * Open source Apache Hadoop software makes it easy to run distributed file systems
    * Phases
        * Map — each node computes/processes the result for its own batch
        * Reduce — nodes get together and aggregate batch results into a final result 
    * EMR Cluster Components — all nodes must be in a single AZ to minimize latency
        * Primary/Master node(s) — single or three primary nodes (for high availability)
            * Manages cluster resources by coordinating all the nodes
            * Tracks and directs HDFS
            * YARN resource management
            * Conducts monitoring and health checks for core and task nodes
        * Secondary/Slave nodes
            * Run tasks for the primary node
            * Coordinates data storage
            * Multiple core nodes, but only one core instance group
        * Task nodes
            * Optional helpers
            * Add parallel computing power to help core nodes process data
            * But these nodes don’t store anything
* Amazon Elastic Map Reduce
    * Amazon’s Managed Hadoop cluster in AWS to store, process, and analyze big data
    * Storage Options
        * Hadoop Distributed Filesystem (HDFS)
            * Fast but ephemeral
            * Used for caching results produced by intermediate job-flow steps
        * Elastic Map Reduce File System (EMRFS)
            * Direct reads and writes to Amazon S3
            * Integrated with persistent S3, advantages include server-side encryption and consistency
            * Lower IOPS compared to HDFS/LFS
        * Local File System (Instance Storage and EBS)
            * Speed/size determined by instance type
            * Ephemeral, so may be used for temporary data (caches/buffers)
            * High IOPS at low cost
    * EMR Operations
        * Transient
            * Shut down cluster when its not being used to save money
            * Not using HDFS as primary storage (using EMRFS/S3 instead)
            * Intensive & iterative data processing
        * Long-running
            * Frequently running processing jobs
            * Processing jobs have an input-output dependency on one another
            * Cost-effective to store your data on HDFS compared to S3
            * Requirement of higher performance provided by HDFS
    * Choosing EMR Instance Types
        * Primary Node — does not have large computational requirements
            * Clusters with 50 or fewer nodes — M5
            * Clusters with more than 50 nodes — M4
        * Core & Task Nodes — depends on type of processing
            * General purpose — M5
            * Batch processing/HPC/ML (CPU Intensive) — C4/C5/z1d
            * Graphics processing/ML (GPU Intensive) — G3/P2/P3
            * Spark applications (Memory Intensive) — R4/R5
            * Large HDFS/MapReduce (High IOPS) — H1/I2/I3/D2
    * Choosing the Number of EMR Instances
        * Primary Node — optional one or three (for high-availability)
        * Core & Task Nodes
            * Run tasks — fewer instances will be too slow, too many instances will be costly
            * Store HDFS data — data is replicated across nodes
                * Default replication factors (changeable) — number of times data is replicated
                    * 1-3 nodes — replication factor of 1
                    * 4-9 nodes — replication factor of 2
                    * 10+ nodes — replication factor of 3
                * Types of Storage
                    * EMRFS
                    * HDFS — high IOPS requirement
                        * Will need to add extra EBS capacity if instance store capacity across all nodes is smaller than the data size*replication factor
                            * Or you can add more instances
            * AWS suggests using a smaller cluster of larger nodes
                * Reduces failure possibilities
                * Reduces maintenance required
    * When would you run Spot Instances for EMR
        * Low-cost requirement
        * Interruptions acceptable
        * Partial work loss is acceptable
            * HDFS data is lost if Spot Instances are interrupted
    * Monitoring and Resizing EMR Clusters
        * Key CloudWatch metrics for:
            * Tracking cluster progress
                * RunningMapTasks / RemainingMapTasks
                * RunningReduceTasks / RemainingReduceTasks
            * Detecting idle clusters — IsIdle
            * Check if HDFS storage is running out of capacity — HDFSUtilization
        * Monitoring can also be done with web UI through SSH tunneling and port forwarding
        * Manual resizing
            * Removing nodes — EMR will automatically remove idle nodes first, and replicate data to other nodes
            * Cannot have fewer core nodes than the current replication factor allows — i.e. reducing from 11 core nodes to 9
                * Must change the replication factor in hdfs-site.xml and restart NameNode daemon before resizing cluster
        * Autoscaling
            * EMR-managed scaling — set a minimum and maximum number of nodes, the on-demand limit, and maximum core node limit
            * Custom autoscaling policy — set a minimum and maximum number of nodes, and then define scale out and scale in policies based on CloudWatch metrics
    * EMR File Storage and Compression
        * Hadoop splits files into smaller chunks, and a single map task is run on each chunk
            * Files stored in HDFS are split automatically
            * Files stored in S3/EMRFS are split in multiple HTTP range requests
        * EMR supported compression algorithms
            * Splittable: bzip2 (speed: slow), LZO (fast)
            * Non-splittable: GZIP (speed: medium), Snappy (very fast)
        * Benefits of Compression
            * Better performance as less data is transferred between S3, mappers, and reducers
            * Less network traffic
            * Reduced storage costs for smaller files
        * EMR File Formats
            * Text — csv, json, tsv
            * Parquet — column-oriented commonly used in Hadoop (Hive, HBase, MapReduce, Pig, Spark)
            * ORC — row-column file format highly efficient for Hive data
            * SequenceFile — flat-file of binary key-value pairs, used extensively with MapReduce input/output formats
            * Avro — row-oriented data serialization framework, developed and used by Hadoop project
        * Best practices for file sizes
            * 1-2 GB files may use a non-splittable compression algorithm
            * 2-4 GB files should use a splittable compression algorithm
            * Avoid smaller files (100MB or less), plan for fewer large files
            * Files should ideally map to block size for max cost efficiency
            * For smaller files, 
                * Reduce HDFS block size from default 128 MB
                * Use S3DistCp command to combine smaller files together into a single large file
                    * The S3DistCp command can also be used to compress files
                    * Copy files within a cluster, or to another cluster
                    * Can be run as a Step or on the Primary node
    * FAQs
        * Pricing
            * Pay for EC2 instances running, and S3 usage — expensive!
                * Also pay for data transfer
            * May use dedicated/reserved/spot instances to save on EMR, along with autoscaling policies
        * Security Measures between EMR and S3
            * EMR always uses HTTPS to send data between S3 and EC2 — encryption in-transit
            * Encrypt input data before uploading to S3 and decrypt within EMR — encryption at-rest
            * Security Groups
                * Two security groups are setup in EMR — one for primary and one for secondary nodes
                * These two only allow communication with each other by default
        * How to configure custom Hadoop settings on my cluster?
            * Configure pre-defined bootstrap actions on startup
            * Console/CLI/SDK — by shorthand syntax or config object in JSON file

### Amazon Redshift

* Amazon Redshift Architecture
    * Cluster — Contains a leader node and worker nodes
        * Leader node deals with schema management, warehouse metadata, query planning
        * Worker nodes perform query execution and slice management
    * Node — Leader or worker nodes
        * Individual compute instances with storage attached
        * Three types to choose from:
            * DC2 — Dense compute — more compute and memory, but less storage
                * Storage attached is less but faster
                * 5TB — 326TB of storage
            * DS2 — Dense storage — slower storage but more of it
                * 64TB — 2PB of storage
            * RA3 nodes — split the difference between dense compute and dense storage
                * Storage backed by S3, using local storage cache to provide “hot data”
                * Only pay for the managed storage that you use
                * Might be slower if storage being accessed isn’t cached and needs to be retrieved from S3
    * Slice
        * Divides compute/memory/storage of a node into smaller separate pieces
        * Each node has 2 slices by default (but this can be adjusted)
        * Slices receive query scripts from the leader node, execute the script, and return the result
    * Query Process
        * User passes query to leader node
        * Leader node generates query plan, which is passed to slices in each worker node
        * Slices execute script and return results to the leader node
        * Leader node aggregates and returns a final result to the 
* Use Cases for Amazon Redshift
    * Warehouse vs. Lake — Data warehouses are structured, and better catalogued than data lakes, so accessing data is much faster
    * Redshift’s speed, scalability, and AWS service integrations differentiate it from other warehousing solutions
    * Use cases for Redshift
        * Central analytics repository for performance and usage of an application
        * Manage complex relational data
        * Training, refining, and reinforcing machine learning models
* Table Design
    * Columnar databases — access columns of data more efficiently
        * So Redshift is better for OLAP — much more efficient
    * Compression — many different types of compression for different data types, not super important for exam
    * Sort Keys — to set a column as a sort key, its compression must be “Raw” (no compression)
        * Compound sort keys for complex or hierarchical data
        * Interleaved for single column JOIN data
    * Distribution Styles  
        * Even — Round Robin, even distribution of data across slices
        * Key — JOIN tables, identical data stored in same slice 
        * All — Small fact/static tables, all slices store this data
        * Auto — Redshift-assigned auto distribution based on size of table data
            * Assigns to All while table data is small
            * Eventually upgrades to Even as data grows larger
    * Constraints — used for query planning hints, optional
* Spectrum
    * Create foreign tables from data stored in S3 using the EXTERNAL keyword for schema/table creation
        * Passes through Redshift Cluster, Redshift Spectrum, Glue, Athena, and S3 under the hood
    * Access control through AWS IAM or AWS Lake Formation
    * Allows SELECT and INSERT (so limited read-write access)
    * Disallows UPDATE and DELETE
* Resizing a cluster
    * Classic Resize — can take hours to days
        * Provisions new cluster and transfers data to it
        * Cluster is available in read-only mode through the risize
    * Elastic Resize — takes 10-20 minutes
        * Creates a snapshot of existing node, and provisions a new cluster with the snapshot, and transfers all remaining data from source cluster
        * Cluster is unavailable for a few minutes. Connections held open and queries are paused.
* Redshift Maintenance
    * Vacuums help reclaim disk space by removing data with a delete marker
        * Maintain optimal query performance
        * Sort data and reindex table
        * Types: Full, Sort only, Delete only, Reindex, To threshold percent, Boost
        * Automatic vacuum occurs when cluster activity is low
    * Deep copy quickly sorts large amounts of data, so its faster than a vacuum
        * With original table DDL, Create Like Table, Temp table & truncate
* Backup & Restore
    * Snapshots
        * Point in time backup of whole cluster
        * Manual/scheduled
        * Retention period is configurable
    * Restoring from snapshot
        * Creates a new cluster, where data is lazy loaded as queries request it
        * Snapshots loaded from S3 using COPY command
            * Other COPY sources include EMR, DynamoDB, SSH
        * Backing up a single table can be done in S3 using UNLOAD
            * Backup in parquet format is efficient for Athena
* Monitoring Redshift
    * Redshift Console allows for
        * Cluster/node metrics, engine-specific metrics
            * Query monitoring (duration, throughput, wait time)
    * CloudWatch provides a more granular view, and allows combining metric graphs

### Athena, Glue, and QuickSight

* AWS Glue
    * Server-less managed ETL service
        * Extract, transform, and load pipeline — categorize/clean/enrich data
        * Move data between data stores
    * Use Cases
        * Query data in S3 by crawling files
        * Joining data with a warehouse using Redshift Spectrum
        * Create a centralized data catalog from data stored in different locations
    * Components
        * Data Source — S3/DynamoDB/RDS/Redshift/EC2/Any JDBC source
        * Crawler finds data schema and creates a catalog
            * Finds important metadata
        * Job transforms data from catalog, and can store to another location
    * Data Catalog
        * Persistent metadata store
        * Centralized repository — one per AWS region
        * Provided comprehensive audit — track schema changes and data access control
        * Converts semi-structured schema into a relational schema
    * Glue Jobs
        * Business logic to perform the extract, transform, load on the data
        * Workflow
            * Data is indexed in catalog
            * Job processing environment pulls data, runs Scala or Python script to transform data, and stores in new store
            * Jobs can be run on-demand, scheduled, or triggered by an event
            * Job bookmarks process only new data that has not been processed before
                * Enabled
                * Disabled
                * Pause — set from/to value and it will only process stuff in between
        * Output File Formats — JSON*, CSV*, ORC, Parquet, Avro
            * *Optional compression (gzip, bzip2)
        * DPU — Data Processing Unit
            * More DPUs make job faster if there’s a ton of data
                * Up to a certain threshold, overutilizing or underutilizing DPUs makes process cost-inefficient
            * Job types
                * Apache Spark
                    * Min DPU — 2
                    * Max DPU — 100
                    * Default — 10
                * Spark Streaming
                    * Min DPU — 2
                    * Max DPU — 100
                    * Default — 5
                * Python Shell
                    * Min DPU — 0.0625 or 1
                    * Max DPU — 1
                    * Default — 0.625
            * Cost per DPU is generally region dependent (~$0.44/hr)
            * Right-sizing DPUs
        * Security
            * Glue jobs run on temporary virtual resources in isolation
            * Glue jobs needs 
                * Data sources and targets in VPC scope
                * IAM role
                * VPC ID/subnet ID/security group access
            * Traffic in/out is governed by VPC policies
                * Except for API calls, which can be audited through AWS CloudTrail
* Amazon Athena
    * Athena is a serverless service to query data in S3 and other data sources, scales automatically
        * Standard SQL syntax
        * Federated queries connects to non-S3 JDBC-compliant data sources
    *  Data Formats & Integration
        * Unstructured, semi-structured, and structured data
        * CSV, TSV, JSON, Textfiles, Parquet, ORC file formats
        * Snappy, Zlib, LZO, GZIP compression
        * Integrates with Quicksight and AWS Glue
        * Query logs from several AWS services 
    * Use cases
        * Ad-hoc queries and BI tools
        * Join data from multiple sources
        * Create ETL pipelines
    * Comparing Athena to other services
        * S3 Select & Glacier Select allow running queries on data in S3
            * Cons: Allows only simple S3 expressions, and can’t deal with compression, allows querying only single object querying
        * EMR & Redshift allow for large scale data processing
            * But Athena allows query processing without managing servers

* Amazon QuickSight
    * Create visualizations with data without managing additional infrastructure
    * Source Data
        * Usable data types
            * Relational Data with AWS data — RDS/Aurora, etc.
            * File data — CSV/TXT/JSON etc
            * SaaS data from GitHub, Jira, Adobe Analytics, Salesforce, etc
        * Load into SPICE (Super-fast Parallel In-memory calculation engine)
            * Query data after it is loaded into SPICE
            * Data stored can be reused without additional cost
            * SPICE capacity is shared between all users for each AWS region
        * Direct query without SPICE is possible but generally slow and inefficient for data reuse
    * Permissions for Dashboard Sharing
        * Specify which users have access
        * Differentiate between viewers vs owners (editors)
        * Can embed in website or application (only for Enterprise edition)
    * Security and Authentication
        * Data Encryption
            * SPICE data is encrypted using AWS-managed keys
            * Encryption at rest is ONLY supported for Enterprise edition
            * Encryption in-transit is always available using SSL for both tiers
            * All encryption keys are AWS-managed
        * Connecting to AWS Resources
            * Relational data
                * Ensure the following mechanisms are setup correctly: Database usernames, passwords, ports, public/private resources, security group, VPC resources, NACLs
                * Security group must contain an inbound rule authorizing access from the IP address range for QuickSight servers in the AWS Region: For RDS, RedShift, and EC2
                * Connection paths:
                    * Manual connect — username must have SELECT permissions on system tables to allow QuickSight to discover table schemas and estimate table size
                    * Auto-discovery — The resources must be in the same region as your QuickSight account
            * S3 Access
                * Ensure the following mechanisms are setup correctly: IAM role access, bucket policies, manifest files (metadata)
        * IAM in QuickSight
            * QuickSight User Types
                * Setup users using IAM credentials
                * QuickSight-only user accounts using email addresses (filters through IAM)
            * User Sign-On Integrations
                * Free: IAM & SSO (Single-Sign On) through SAML 2.0 ("Security Assertion Markup Language")
                * Enterprise Edition Only: AWS Directory Service, AWS Directory Service with AD (“Active Directory”) Connector, On-Premise AD with SSO or AD Connector, SSO using AWS SSO service or third-party federation service
        * Best Practices
            * Firewall — Allow HTTPS and WebSockets protocol access for QuickSight to reach databases not on AWS
            * SSL — Use SSL to connect to databases, with publicly recognized certificate authorities
            * Enhanced Security
                * Encrypt data stored at-rest in SPICE
                * Integrate AD/SSO auth
                * Limit access with low-level security IAM rules
            * VPC — Use VPC for data in AWS data sources, or Direct Connect to link on-premises resources to VPC and QuickSight
* Amazon Elasticsearch/OpenSearch
    * Runs most of the ELK (Elasticsearch, Logstash, Kibana) stack
        * Elasticsearch — organizes data as indexes rather than tables
        * Logstash — ingests, processes, and stores log data
        * Kibana — powerful visualization engine
    * Using Elasticsearch
        * Organizes data in JSON documents
            * Top-level organizational unit is the index
            * Type is the second-level used to categorize data
        * Interface
            * REST API with either GET, PUT, POST, or DELETE methods
            * Anything interacting with REST API endpoint can read/update Elasticsearch
            * Load data from anything that can generate JSON
        * Direct Elasticsearch Service Integrations without writing code:
            * Kinesis Data Firehose
            * CloudWatch
            * IoT
    * FAQ
        * Security for Elasticsearch Domains
            * API access controlled with IAM
            * VPC security groups and ACLs secure the Elasticsearch domain
            * Elasticsearch provides fine-grained IAM controls for security
        * How do I backup ElasticSearch?
            * Automatic/manual snapshots
            * Snapshots allow migration of ES domains between regions/accounts
        * Logstash and Elasticsearch
            * Logstash is not included in Elasticsearch Service domains
            * ES domains are a compatible backend for logstash implementations

Other Notes (from practice test questions that I got wrong)

* Secrets Manager has fully automated secret rotation for RDS, DocumentDB, and Redshift
* Windowed Queries
* manage query queues in redshift
    * Amazon Redshift workload management (WLM) enables users to flexibly manage priorities within workloads so that short, fast-running queries won't get stuck in queues behind long-running queries. Amazon Redshift WLM creates query queues at runtime according to service classes, which define the configuration parameters for various types of queues, including internal system queues and user-accessible queues
* EMR Uniform Instance Groups vs Instance Fleets
    * Cannot have a cluster with both instance groups and instance fleets
* KCL/KPL retries
    * KPL — Retries are automatic when using the KPL and records will be included in subsequent Kinesis Data Streams API calls, in order to improve throughput and reduce complexity while enforcing the Kinesis Data Streams record’s time-to-live value. There is no backoff algorithm, making this a relatively aggressive retry strategy. Spamming due to excessive retries is prevented by rate limiting. [KPL Retries and Rate Limiting](https://docs.aws.amazon.com/streams/latest/dev/kinesis-producer-adv-retries-rate-limiting.html)
    * KCL — The KCL automatically takes care of failed process records in a shard, so you would not need to implement this logic separately.
    * Since KCL takes care of tracking by passing a checkpointer when processing records, if the worker fails, the KCL will use this information to restart the processing of the shard at the last known processed record.
    * Kinesis Data Streams requires the record processor to keep track of the records that have already been processed in a shard. The KCL takes care of this tracking for you by passing a checkpointer (IRecordProcessorCheckpointer) to `processRecords()`. The record processor calls the checkpoint method on this interface to inform the KCL of how far it has progressed in processing the records in the shard. If the worker fails, the KCL uses this information to restart the processing of the shard at the last known processed record. [Developing a Kinesis Client Library Consumer in Java](https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-implementation-app-java.html)
* Athena Workgroups
    * By default, all Athena queries execute in the primary workgroup. As an administrator, you can create new workgroups to separate different types of workloads. Administrators commonly turn to workgroups to separate analysts running ad-hoc queries from automated reports
* Redshift — Manifest to specify data files loaded using COPY commands 
    * Amazon S3 provides strong read-after-write consistency for COPY, UNLOAD, INSERT (external table), CREATE EXTERNAL TABLE AS, and Amazon Redshift Spectrum operations on Amazon S3 buckets in all AWS Regions. You can use a manifest to ensure that the COPY command loads all of the required files, and only the required files, for a data load. [Managing Data Consistency](https://docs.aws.amazon.com/redshift/latest/dg/managing-data-consistency.html) [](https://docs.aws.amazon.com/redshift/latest/dg/loading-data-files-using-manifest.html)
* Redshift — For a COPY command to be successful, two things are required:
    * IAM role with appropriate S3 permissions
    * Explicit COMMIT at the end of COPY command to save changes uploaded from S3; or ensuring that data is auto-committed
* Glue — Cannot process irregular files
    * While Glue can process text-based files with a common delimiter, this data has irregular delimiters that will require something more customizable to handle processing.
    * Because you have irregularly formatted data, you need to perform manual data transforms. So, the bulk of the work here is to map a line from a file in each bucket prefix and then apply that transformation where appropriate. After that, you can easily deliver the results to a Kinesis Firehose stream, which can have an Elasticsearch domain configured as the target.
* Athena — Process (CSV→Parquet & Partition & Compress) data in-place without using Glue
    * You can process the data "in place" with just Athena. By default, CTAS queries will store the output in Parquet format, and from there it's relatively simple to create partitions and configure column compression. All of these things will improve query performance and reduce the cost of querying the data.
* EMR — Calculate # of mappers
    * Each file less than the mapper block size will require its own mapper
    * Each file larger than the mapper block size will require (file_size/block_size) mappers
* EMR — Encryption Types
    * EBS Encryption
        * EBS Encryption — Beginning with Amazon EMR version 5.24.0, you can choose to enable EBS encryption. The EBS encryption option encrypts the EBS root device volume and attached storage volumes.
        * LUKS Encryption — Linux Unified Key Setup, or LUKS encryption is supported for encrypting data on EBS volumes attached to EC2 instances in an EMR cluster.
        * Open-source HDFS encryption
    * EMRFS Encryption
        * SSE-S3
        * CSE-KMS
