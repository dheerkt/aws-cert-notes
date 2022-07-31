# AWS Cloud Practitioner CLF-C01 Cheat Sheet

## Exam Outline

The exam tests:

* Explain AWS Cloud
* AWS Shared Responsibility Model
* Security best practices
* Cloud costs, economics, and billing practices
* Explain core AWS services, including compute, network, databases, and storage
* Identify AWS services for common use cases

Exam format:

* 50 questions MCQ (scored) + 15 unscored questions
* Pass/fail grade, need 70% correct to pass
* No penalty for guessing
* Sections
    * Cloud Concepts — 26%
    * Security and Compliance — 25%
    * Technology — 33%
    * Billing and Pricing — 16%

## Foundations of Cloud Computing

* What is cloud computing?
    * Delivery of computing over the internet
    * On-demand/no upfront payments
    * Pay-as-you-go pricing
    * Virtualization is at the heart of cloud computing
        * Divides a hardware physical server into many different virtual machines, each acting as their own server
* What are the advantages of cloud computing?
    * Companies don’t want to spend money, time, resources to maintain own data center infrastructure. Renting from AWS makes deploying faster, easier, more scalable
    * Six advantages of cloud
        * Global — Deploy around the world in minutes
        * Focus on OpEx — Allows companies to focus on projects that differentiate their business
        * EoS — Benefit from massive economies of scale
        * Speed & Agility — Innovate and deploy faster
        * Stop guessing about capacity — as it always matches exactly to demand
        * Trade capital expenses for virtual expenses with the cloud — i.e. pay for only what you use
    * Cloud terminology
        * High availability — systems designed to operate without failure for a long time
        * Elasticity — don’t have to plan capacity ahead of time
        * Agility — speed to market
        * Durability — data protection, AWS guarantees durability
    * CapEx vs OpEx
        * CapEx is fixed costs, OpEx is variable costs
* Different cloud service models
    * Infrastructure as a Service (IaaS)
        * Fundamental building blocks that can be rented
        * Like web hosting
    * Platform as a Service (PaaS)
        * Services like Cloud9
    * Software as a Service (SaaS)
        * Managed services like SageMaker
* Cloud deployment models
    * Private Cloud / On-Premises
        * Increased security and more control
        * Increased CapEx cost to maintain hardware
        * No advantages of cloud computing
    * Public Cloud i.e. AWS
        * Advantages of cloud computing
        * No responsibility of maintaining hardware
    * Hybrid — combination of private and public
        * Sensitive data stored in private cloud, whereas app infrastructure may be stored in public cloud. 
        * Two deployment models can communicate using Direct Connect service
* Leveraging the AWS Global Infrastructure
    * Regions — i.e. a physical location
        * Want to select region closer to users to improve speed and reduce latency
        * Each region is spread out, fully independent and isolated
        * Regions are resource and service-specific, so stuff isn’t automatically replicated across them
    * Availability Zone — i.e. collection of one or more data centers, each AZ is physically separated with redundant power, networking, and connectivity, housed in separate facilities
        * AZs are physically separated
        * Connected through low latency links
        * Fault tolerant
        * Allow for high availability
    * Edge Locations — mini-data center used to cache content through CloudFront
        * Reduce latency
            * Latency is the time taken for website to load, lower is better
        * Used to cache static content through CDN like CloudFront
        * Does not run main infrastructure like EC2 instances
    * Remember:
        * Multi-AZ deployments provide high availability
        * Each AZ has multiple datacenters
        * Each region is global and has 2 or more AZs
        * Edge locations lower latency by caching content closer to users
* Exploring your AWS account
    * Management Console has UI, great for non-technical roles
    * Root user is created when you initially sign up for AWS account
        * Must use multi-factor authentication
        * Best practice is not to use the root user for anything
        * Root user has ability to delete the account
    * Programmatic Access
        * CLI
        * Application Code (through SDKs)

## Technology

### Compute

* Elastic Compute Cloud (EC2)
    * Virtual server in the cloud
        * Provision immediately, deploy app directly to instance
        * Use pre-configured AMI or set up your custom configuration
    * Access an instance
        * AWS Management Console
        * SSH
            * Requires a key-pair
        * EC2 Instance Connect
        * AWS Systems Manager
    * EC2 Pricing
        * On-demand instances
            * Fixed price billed per second, no contract
            * Low-cost, no upfront payment or long-term commitment
            * Great for workloads that do not need to be interrupted, or workloads that will not run for more than a year
            * Can reserve capacity for on-demand instances
        * Spot instances
            * Take advantage of unused capacity
            * Great for interrupt-able workloads
            * Low compute price — save up to 90% off on-demand prices
            * Pay spot price thats in effect at the beginning of the hour
        * Reserved instances
            * Commit to reserve instance for 1 or 3 years; Sign contract
            * Can pay a portion upfront, and receive a proportional discount compared to on-demand prices
            * Convertible reserved instances can be changed after reservation
        * Dedicated Host
            * Pay for physical server fully dedicated to running your instances
            * Useful for if you want to bring your own server bound software license from Microsoft/Oracle type vendors
            * Useful for regulatory compliance reasons
            * No multi-tenancy i.e. sharing of physical server hardware with other customers
        * Savings Plans
            * Committing to purchase compute usage across EC2, Lambda, Fargate, etc. 
            * 1 or 3 year contracts
            * Can save up to 72% from on-demand instances
            * No capacity reservation
    * EC2 Features
        * Load balancing
        * Autoscaling
* Lambda
    * Serverless compute service
        * Developers can focus on code instead of managing servers like with EC2
    * Features
        * Java, Go, Powershell, Python, C#, Ruby, Node.js
        * Can execute code in response to events
        * Functions have 15-minute timeout, and maximum memory limitations
    * Pricing
        * Always free-tier
        * First 1,000,000 requests are free per month, even after the free tier expires
        * Pay only for compute time for when the code is running
* Fargate
    * Serverless computer engine for containers that scales automatically
    * Allows management of containers like Docker
* Lightsail
    * Quickly deploy simple preconfigured applications like WordPress websites
    * Simple screens for people without technical/cloud experience
    * Provides infrastructure VM, SSD, Static IP, data transfer, DNS Management for a low monthly fee
* Outposts
    * Run cloud services in internal data center

### Storage

* Simple Storage Service (S3)
    * Unlimited object storage in buckets that are highly available
    * Features
        * Accessible via Management Console, CLI, or code (boto3 API)
        * Durability — Will my data be here in X days?
            * AWS guarantees 99.999999999% (11 9’s) durability
        * Availability — Can I access my data when I need it?
            * AWS guarantees 99.99% availability
        * Granular Secure Access
            * Set security at bucket level or object level by using access control lists (ACLs), bucket policies, or access point policies
            * S3 access logs track access to buckets and objects
            * Versioning allows for multiple versions of objects to prevent from accidental deletion
            * S3 is regional, but bucket names are globally unique
    * Storage Classes
        * S3 Standard
            * General purpose storage for any type of data
            * Recommended for frequently accessed data
        * S3 Intelligent Tiering
            * Automatically moves data to most cost-effective storage class
            * Recommended for data with unknown or changing access patterns
            * No retrieval fees
            * Data stored across AZs
        * S3 Standard Infrequent Access
            * Data stored across multiple AZs
            * Recommended for long-lived data, infrequently accessed, but requires quick access
            * Cheaper than S3 Standard
        * S3 Standard One-Zone Infrequent Access 
            * Like S3 Standard IA, but only stored in a single AZ
            * Costs 20% less than S3 Standard IA
            * Recommended for recreate-able data that’s infrequently accessed
            * Availability and durability not essential
            * 99.5% availability instead of 99.99%
        * S3 Glacier
            * Long term data storage
            * Recommended for long-term backups and archival reasons
            * Cheap, but data retrieval can take longer
            * 3 retrieval options: 1-5 minutes, 3-5 hours, or 5-12 hours
            * Data stored across multiple AZs
        * S3 Glacier Deep Archive
            * Like S3 Glacier, but longer access times
            * 2 retrieval options: 12 hours or 48 hours
            * Cheapest option
            * Recommended for data accessed once or twice a year, useful for retaining data for regulatory compliance requirements
        * S3 Outposts
            * Provides object storage on-premises
            * Single storage class, across multiple devices/servers
            * Recommended for data that needs to be kept local due to performance needs
    * Use Cases
        * Static websites
            * Deploy using CloudFront for global distribution
        * Data archive
        * Analytics systems
            * Data lakes for services like RedShift and Athena
        * Mobile applications
            * S3 Transfer Acceleration speeds up file uploads
* EC2 Storage Types
    * EC2 Instance Store
        * Local storage physically attached to instance host
        * Ephemeral, does not persist if instance is stopped
        * Throughput/latency is low, faster read/write speeds
        * Temporary storage needs or fast I/O
    * Elastic Block Storage (EBS)
        * Volumes that can be attached to and detached from instances
        * Data persists if the instance is stopped
        * Only attached to a single instance in the same AZ
        * Recommended for quickly accessible data, running a database, or long-term data storage
    * Elastic File System (EFS)
        * Shared drive, associates with many different instances
        * Serverless Google Drive for EC2 instances
        * Only used on Linux filesystem
        * Exists across an entire region
        * More expensive than EBS
        * Recommended for business critical apps, or lift-and-shift enterprise apps
    * Storage Gateway
        * Hybrid storage service
        * Connect on-premises and cloud data
        * Recommended for moving backups to the cloud, reducing hybrid cloud storage costs, and low latency access to data
    * AWS Backup
        * Backups data from EC2, EBS, EFS, and more
        * Create a scheduled backup plan to selectively backup data at certain times
* Content Delivery Network
    * Delivers content based on geographic location
    * CloudFront
        * CDN that provides global delivery with low latency
        * Speeds up static and dynamic web content
        * Uses edge locations to cache content globally
        * If data is not cached, CloudFront requests data from origin, stores it in the cache and delivers it to user
        * CloudFront can prevent DDoS attacks, and block IP addresses/geo-restriction
    * Global Accelerator
        * Improves latency and availability of single-region applications
        * Sends traffic through AWS global network infrastructure to provide a 60% performance boost
        * Reroutes traffic to healthy available regional endpoints
    * S3 Transfer Acceleration
        * Allows for fast transfer of files over long distances, using CloudFront’s global edge location network

### Network

* Virtual Private Cloud
    * Secure private network in the cloud to isolate and launch your resources in
        * A “fence” around your resources
    * Spans all AZs in a region
    * Subnet
        * Splits network inside VPC, where you can launch resources like EC2 instances
        * Private subnets aren’t accessible from the internet
        * Public subnets are accessible via:
            * NACLs — ensure proper traffic is allowed into the subnet
            * Router and Route Tables — defines where the network traffic is routed
            * Internet Gateway — allows public traffic to internet
        * Peering Connections allow two subnets to transfer data securely
* Other Networking Services
    * Route53 — DNS service
        * Translates domain name to server IP addresses
        * Allows for domain name registration
        * Performs health checks on AWS resources
        * Supports hybrid cloud architectures
    * Direct Connect
        * Dedicated physical network connection i.e. superfast
        * Connects on-premises data center to AWS
        * Data travels over private network
        * Used for:
            * Large dataset
            * Business critical data
            * Hybrid models
    * Virtual Private Network (AWS VPN)
        * Similar to Direct Connect, connects on-premises data center to AWS
        * Data travels over public internet rather than private network
        * Data is automatically encrypted
        * Cheaper than Direct Connect
    * API Gateway — build and manage APIs
        * Share data between systems, integrating with other services like Lambda

### Other Services

* Databases
    * Organized collection of various forms of data, necessary to save/persist data
    * AWS Types of Databases
        * RDS — Relational Database Service
            * Supports popular DB engines — Aurora, PostgreSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server
            * AWS manages database with automatic software patches, backups, OS maintenance, etc.
            * High availability and fault tolerance using multi-AZ deployment options
            * Read replicas across regions provide enhanced performance and durability
        * Aurora
            * Relational database compatible with MySQL/PostgreSQL
            * 5x faster than MySQL and 3x faster than PostgreSQL
            * Scales automatically, while providing high durability + availability
            * Fully managed by RDS
        * DynamoDB
            * NoSQL key-value DB / non-relational
            * Fully managed and serverless
            * Scales automatically with fast performance
        * DocumentDB
            * For documents
            * MongoDB compatible
            * Serverless / fully managed
            * Non-relational
        * ElastiCache
            * Fully managed in-memory datastore, compatible with Redis or Memcached
            * Data can be lost
            * Offers high performance and low latency
        * Neptune
            * Fully managed serverless graph database for highly connected services like social media sites
            * Fast and reliable
* Migrations
        * Database Migration Service (DMS)
            * Migrate to or within AWS, homogenous or heterogenous migrations
            * Continuous data replication
            * Virtually no downtime
            * Use cases:
                * OP Oracle to Aurora MySQL
                * OP Oracle to RDS Oracle
                * RDS Oracle to Aurora MySQL
        * Server Migration Service (SMS)
            * Migrates on-premises servers to AWS
            * Server saved as new AMI
            * Use AMI to launch servers as EC2 instances
        * Snow Family
            * Snowcone
                * Smallest, holds 8TB of usable storage
                * Offline or online shipping with DataSync
            * Snowball and Snowball Edge
                * Petabyte-scale data transport, in and out
                * Cheaper than internet transfer
                * Supports EC2 and Lambda
            * Snowmobile
                * Exabyte scale data
                * Data loaded to S3
                * Secure transportation — GPS monitoring, security escort, etc.
            * DataSync
                * Migrate data from on-premises to AWS storage services like S3 or EFS
                * Copy data over Direct Connect or the internet
                * Copy data between AWS storage services
                * Replicate data across regions or accounts
* Analytics Services
        * Amazon Redshift
            * Scalable data warehousing solution, used to consolidate data from multiple sources or relational databases
            * Exabyte-scale
        * Athena
            * Serverless SQL query service for S3
            * Pay per query
        * Glue
            * ETL service to prepare and load data for analytics
        * Kinesis
            * Analyze real-time streaming data
            * Supports video, audio, logs, website clickstreams, and IoT
        * Elastic MapReduce (EMR)
            * Big data processing using Hadoop and big data frameworks
        * Data Pipeline
            * Move data between compute and storage services on AWS or on-premises
            * Move data at specific intervals or based on certain conditions
            * Sends success/failure notifications
        * QuickSight
            * Visualize data using dashboards and embed into applications
* Machine Learning
        * Rekognition
            * Automate image + video analysis
            * Identify custom images and perform face/text detection in videos/images
        * Comprehend
            * Natural language processing service that uncovers insights and relationships in text
        * Polly
            * Text to speech, natural sounding voice
            * Creates custom voice
        * SageMaker
            * Prep/train/deploy data for models
            * Deep learning AMIs
        * Translate
            * Real-time and batch translation across multiple languages
            * Translates many content formats
        * Lex
            * Recognize speech and understand language to build highly engaging chatbots
            * Powers Amazon Alexa
* Developer Tools
        * Cloud9
            * Browser IDE allowing writing/debugging code
            * Supports several popular languages
        * CodeCommit
            * Source control system for private Git repositories
            * Collaborate with other software developers
        * CodeBuild
            * Build/test source code before deploying
            * Enables CI/CD
        * CodeDeploy
            * Deploys code to EC2, Fargate, Lambda, and on-premises
            * Maintains application uptime through rolling deployments
        * CodePipeline
            * Integrates CodeBuild, CodeCommit and CodeDeploy to quickly deliver new features/updates
        * X-Ray
            * Analyze/debug production applications
            * Map application components
            * View end-to-end requests
        * CodeStar
            * Helps developers collab on projects by connecting developer environments
            * Integrates with CodeBuild, CodeCommit, and CodeDeploy
            * Contains issue tracking dashboard
* Deployment & Infrastructure Management
        * Infrastructure-as-Code
            * Scripts to provision AWS resources in a reproducible manner
            * Automation saves time, reduces errors
            * JSON/YAML
        * CloudFormation
            * Allows IaC to provide repeatable process to provision resources
            * Works with most AWS services
            * Create templates
        * Elastic Beanstalk
            * Orchestration service used to deploy and scale web apps by provisioning more or less resources to autoscale
            * Capcity provisioning/Load balancing/Auto scaling
            * Monitor the health of your resources
        * OpsWorks
            * Works with Chef/Puppet to automate configuration of your servers and code
            * Manage on-premises servers or EC2 cloud instances
* Messaging and Integration Services
        * SQS
            * Loose coupling is best policy for cloud computing
                * Break systems into microservices which are connected but not dependent on each other
            * Queues help implement loose coupling
                * Messages wait in queue to be received in FIFO format (...in most cases)
                * SQS allows component-to-component communication through async messages
        * SNS
            * Send emails/texts from applications
            * Publish to topic, and subscribers receive those messages
        * SES
            * Send/receive richly formatted emails from applications (HTML emails)
            * Ideal for marketing campaigns or professional emails
* Auditing, Monitoring, and Logging
    * Proactively find and resolve errors
    * CloudWatch — Collection of services
        * Monitor metrics, logs, events
            * Timeseries data for metrics
            * Events allow for automated action based on triggers
        * Detect anomalies through real-time monitoring
        * Visualize logs
            * Performance data for EC2/Lambda/etc.
        * Set alarms
            * Notification based on event rules, or anomalies
    * CloudTrail — Track user activity and API calls
        * Log and retain account activity in console, CLI or SDKs
            * Logs retained for past 90 days in CloudTrail event history log
                * Can track specific time, on a per region basis
        * Identify which user made changes
            * User, event time, IP address, region, access key, error code
        * Detect unusual activity
* Additional Services
    * Workspaces
        * Host virtual desktops in the cloud (Linux & Windows)
        * Enables WFH
    * Connect
        * Cloud contact center or helpdesk
        * Provides customer service functionality
        * Improves productivity of help desk employees

## Security and Compliance

* Shared Responsibility Model
    * AWS’ responsibility — Security OF the cloud
        * Protect AWS Global Infrastructure and facilities 
            * Regions, edge locations, AZs
        * Data center physical security
            *  Security guards, fences, location, etc.
        * Networking components
            * Maintains physical infrastructure like UPS systems, generators, CRAC units, fire suppression systems, etc.
        * Software
            * Responsible for all managed services like RDS, S3, ECS and Lambda
            * Including patching and upgrading operating systems and securing data access endpoints
    * Your responsibility — Security IN the cloud
        * Application data — data encryption
        * Security configuration — securing API endpoints, rotating access keys, etc.
        * Patching — guest OS on EC2 instances
        * Identity Access Management — keep keys safe
        * Network traffic — firewall configuration, VPC security groups and NACLs
        * Installed software — frequently scan and patch vulnerabilities in your code
* Well-Architected Framework
    * Main summary of most of the following points:
        * Automate stuff through IaC
        * Use managed services
        * Audit usage and costs to ensure you’re getting the most out of your resources
        * Test security and resilience often, and optimize
    * Operational Excellence
        * Anticipate failure
        * Script operations as code using IaC
        * Deploy small, reversible changes
        * Learn from failure and refine
    * Security
        * Encrypt data in transit and at rest
        * Automate security tasks
        * Principle of least privilege
        * Ensure security at all layers 
        * Track account activity — who did what and when?
    * Reliability
        * Recover from failure automatically — self healing systems
        * Scale horizontally for resilience
        * Manage change through automation — IaC
        * Test recovery procedures
        * Reduce idle resources
    * Performance Efficiency
        * Prioritize serverless architecture
        * Multi-region deployments to reduce latency and create redundancy
        * Delegate tasks to cloud vendor — use managed services
        * Experiment with virtual resources
    * Cost Optimization
        * Consumption based pricing
        * Implement cloud financial management
        * Measure overall efficiency
        * Pay only for resources that your app requires
    * Sustainability
        * Understand impact, and reduce downstream impact
        * Establish sustainability goals
        * Maximize utilization
        * Use managed services
* Identity and Access Management (IAM)
    * Identities involves WHO accesses (Authentication), whereas Access involves WHAT resources are accessed (Authorization)
    * Users — entities created to represent person or application accessing resources
        * Root user created when you first open account
            * Has the permission to close account/change email/modify support plan
        * Individual users
            * Can be applications or people
            * Perform administrative tasks, access app code, launch EC2 instances, configure DBs
        * Principle of least access — give users minimum needed access to do what they need
        * IAM generates access keys for users to access AWS through the CLI
    * Groups
        * Users that perform similar tasks (admins, devs, etc.) can be assigned to a group
        * Access permissions of the group apply to all members assigned to that group
        * Access is assigned using policies and roles
    * Roles
        * Define access permissions
        * Temporarily assumed by IAM users or services to perform an action
    * Policies
        * Manage permissions for IAM users, groups, and roles
        * Authored in JSON
        * Can be AWS managed, or customer managed
    * Best Practices
        * Enable MFA
        * Create individual users, don’t use root
        * Strong password policies; credentials should be rotated regularly
        * Use temporary roles for EC2 instances instead of long-term credentials like access keys
    * IAM Credential Report
        * Lists all users and status of their passwords, access keys, and MFA devices
        * Used for auditing and compliance purposes
* Application Security Services
    * Web Application Firewall (WAF)
        * Protects apps against common attack patterns, SQL injections, cross-site scripting
        * Firewall
    * Shield
        * Managed DDoS protection service with always-on detection
        * Provides notifications of DDoS attacks via CloudWatch
        * Two packages: Shield Standard (free); and Shield Advanced (paid)
            * Standard provides free protection against common and frequently occurring attacks
            * Advanced provides enhanced protection and 24/7 access to experts for a fee
        * Provided on several services: CloudFront, Route53, ELB, AWS Global Accelerator
    * Macie
        * Discover and protect sensitive data using ML to evaluate S3 environment
        * Uncovers PII (personal identifiable information) e.g. passport/credit card numbers
* Additional Security Services
    * Config — set guardrails within AWS account
        * Assess/audit/evaluate configurations of AWS resources
        * Track changes over time, and notify about any change using SNS
        * Store configuration history in S3 file 
    * GuardDuty — uses ML to analyze traffic within account
        * Identifies common unauthorized access patterns using ML
        * Built in for IAM, EC2, and S3
        * Reviews CloudTrail logs, VPC flow logs, and DNS logs
        * Takes automated action to mitigate attack
    * Inspector — alerts to network and config vulnerability on EC2 instances
        * Agent is installed on EC2 instance
        * Checks instance for vulnerabilities and reports any that are found
            * Access from internet, remote root login, vulnerable software versions, etc.
        * Report includes severity of vulnerability
    * Artifact — Deliver compliance reports for third-party auditing
        * Central repository for compliance reports
            * e.g. Service Organization Controls (SOC) reports, Payment Card Industry (PCI) reports
        * Reports can be shared to third-parties as read-only files
    * Cognito — control access to web/mobile apps
        * Provides authentication and authorization of users for web/mobile apps
        * Helps manage users, and assists them with signup/sign in
        * Real world use — enable Sign in through FB/Google etc.
* Data Encryption and Secrets Management
    * Key Management Service (KMS)
        * Key generator that allows storage and control of encryption keys
        * AWS manages encryption keys; gives decryption key to only those who have sufficient privilege
        * Automatically encrypted for certain services like CloudTrail logs, S3 Glacier
    * CloudHSM
        * Hardware security module used to generate encryption keys
        * Generate and manage your own keys with dedicated hardware device
        * AWS has no access to your keys; may be necessary for compliance reasons
    * Secrets Manager
        * Rotate, manage, and retrieve secrets like passwords, database credentials, API access keys
        * Encrypt secrets at rest using your encryption keys
        * Integration with services like RDS, Redshift, DocumentDB
        * Removes need to hardcode sensitive information in app code; acts like Lambda Environment variables

## Pricing, Billing, and Governance

* Pricing
    * Three main ways AWS charges customers: compute, storage, egress data transfer
    * Free offer types
        * 12-month free tier following initial sign-up
        * Always free — never expires
        * Trials — short-term free trials per service
    * RDS Pricing
        * DB runtime, size, type, purchase type, API requests, deployment type, outbound data transfer
    * Total Cost of Ownership
        * Direct & indirect costs of AWS
        * TCO calculator no-longer available
        * How to reduce your TCO
            * Minimize CapEx — pay for what you use
            * Use RIs — lock in savings by using reserved instances
            * Right-size your resources — match supply with demand
        * Price-List API allows querying cost of services
    * App Discovery Service
        * Helps plan migration projects, working with other services to migrate servers
        * Used to estimate TCO, with pricing calculator
* Billing Services
    * Budgets
        * Receive alerts when costs or usage exceeds budgeted amounts
        * Cost, usage, and reservation budgets available
            * Cost — how much you want to spend
            * Usage — how much you want to use
            * Reservation — plan for reserved instances or long term savings targets
    * Cost and Usage Report
        * Detailed comprehensive report listing cost and usage for each service category
        * Aggregate data on a daily, hourly, or monthly level
    * Cost Explorer
        * Visualize and forecast cost and usage over the past 12 months, and future 3 months respectively
    * Cost Allocation Tags
        * Track costs via cost allocation reports
        * Label resources using a key-value pair to track spend over time
* Governance Services
    * Organizations
        * Group multiple accounts in one organization, having a single payer for all accounts
        * Automate account creation
        * Allocate resources and apply policies across accounts
        * Features
            * Consolidated billing
            * Cost savings — volume discounts
            * Account governance — manage many accounts
    * Control Tower
        * Helps Organization accounts ensure comply with company-wide policies
        * Set up new accounts using multi-account strategy
        * Enforces best use of services across accounts
        * Provides dashboard to manage accounts
    * Systems Manager — visibility and control over resources
        * Automate operational tasks on your resources
        * Group resources and take actions
        * Patch/run commands on multiple EC2 instances or manage RDS instances
    * Trusted Advisor
        * Checks your account and makes recommendations, helping users follow best practices
        * Helps you see service limits
        * Some checks are free; Enterprise and Business support plans get the premium TA checks
    * License Manager
        * Manage on-premises and AWS software licenses, e.g. Oracle, Microsoft, SAP, etc.
    * Certificate Manager
        * Public and private certificates for free
        * Integrates with ELB, API Gateway, and more
* Management Services
    * Managed Services
        * Augment internal staff, reducing operational risks and overhead
        * Ongoing management of infrastructure
    * Professional Services
        * Helps enterprise customers move to a cloud-based operating model
        * Proposes, architects, implements solutions
    * AWS Partner Network (APN)
        * Technology partners, consulting partners, approved vendors with deep AWS experience
        * If team lacks technical expertise, hire APN
    * Marketplace
        * Buy third-party software; Search catalog and install with click of button
        * Sell solutions for AWS customers
    * Personal Health Dashboard
        * Troubleshooting guidance and feedback
* Support Plans
    * Plans
        * Basic — free
            * Account and billing support
            * Service limit increases
            * 24/7 customer service access via email only
        * Developer — $29/mo — testing/dev
            * Technical support
            * 1 primary contact, unlimited cases
            * Cloud support associate, during business hours via email only
                * < 24h for general guidance, <12h if system impaired
        * Business — $100/mo — production
            * Unlimited contacts, unlimited cases
            * Full trusted advisor checks
            * 24/7 access via email, phone, chat to Cloud Support Engineers
                * <24h for general guidance, <12h for system impaired, <4h for production impaired, <1h for production system down
        * Enterprise — $15000/mo — mission-critical production
            * Dedicated Technical Account Manager (TAM)
            * Concierge support team — billing/acc questions
            * Infrastructure event management — operational support during product launches/migrations
            * Cloud Support Engineers 24/7 via email, phone, chat
                * Business + <15m for business critical system down
    * Support Case Types
        * Account and billing
        * Service limit increases
        * Technical support — only for developer, business, or enterprise (paid plans only)
            * No debugging help or sysadmin tasks