<p align="center"><img width="150" alt="portfolio_view" src="https://user-images.githubusercontent.com/26981510/226486412-56773797-7fff-45e4-a52c-0f144b099bb0.png"></p>

<h1 align="center">AWS Solutions Architect Certificate Notes</h1>

## Simple Storage Service (S3)

- Object-based storage
- Not for rapidly changing data
- Virtual-host style and path-sytle URLs (soon to be retired) supported
  - Virutal-host style example: https://bucket-name.s3.Region.amazonaws.com/key
  - Path-style example: https://s3.Region.amazonaws.com/bucket-name/key
- *Multi-part upload*
  - For larger files
  - Improved throughput
  - Ability to begin upload before knowing its size
  - Can pause and resume uploads
  - Quick recovery from network issues
- To make sure that S3 objects are only accessible from Cloudfront, create an Origin Access Identity (OAI) for Cloudfront and grant access to the objects to that OAI.
- Supports the following destinations where it can publish events:
  - Simple Notification Service (SNS) topic
  - Simple Queue Service (SQS) queue
  - Lambda


- **Storage Tiers**
  1. **Standard**
      - General purpose
      - Low latency, high throughput
      - 99.99% availability
  2. **Intelligent-Tiering**
      - Unknown or changing access pattern
      - Small monthly monitoring and auto-tiering fee
      - 99.9% availability
  3. **Standard Infrequent Access (IA)**
      - Infrequent access
      - Long-term storage, backups, and as a data store for disaster recovery files
      - 99.9% availability
  4. **One Zone Infrequent Access (IA)**
      - Only stored in one AZ
      - Costs 20% less than Standard IA
      - Lower-cost option for infrequently accessed data but do not require the availability and resilience
      - 99.5% availability
  5. **Glacier**
      - Low-cost storage for long-term archival data
      - Retrieval times from minutes to hours
      - Can have one resource-based vault access policy
      - Can have one **Vault Lock** policy
        - Example: Retain archives for 1 year before you can delete them
      - Retrievals
        - Expedited - Costly 1-5 min, <250MB
        - Standard - Default, 3-5 hours
        - Bulk - Cheapest, 5-12 hours
  6. **Glacier Deep Archive**
      - Lowest cost storage class
      - Data will be retained for 7-10 years
      - Retrieval time within 12 hours


- **Options**
  - **S3 Select**
    - Oranize and mange your date in ways to suppport specific use cases
    - Allows retrieval of subset of data from an object by using simple SQL expressions (ie. single gzip csv file)
  - **Transfer Acceleration**
    - Enables fast, easy, and secure transfer of files over long distances between your client and your Amazon S3 bucket
    - Can speed up content transfers to and from Amazon S3 by as much as 50-500% for long-distance transfer of larger objects
  - **Provisioned Capacity**
    - Ensures retrieval capacity for expidted retrievals is available when you need it
    - If your workload requires highly reliable and predictable access to a subset of your data in minutes
  - **S3 Object Lock**
    - Write once and read many times (WORM)
    - Help prevent objects form being deleted or overwritten
  - **Server Access Logging**
    - provides detailed records for the requests that are made to a bucket
    - Access log information can be useful in security and access audits.
    - No extra charge for this, except storage of log files

### Athena
- Interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL
- Relies on pooled resources provided by AWS to return query results
- Serverless

---

## DynamoDB

- Serverless database
- NoSQL
- Can store - JSON, BLOB data, web sessions, key/value pairs
- The combined Value and Name combined must not exceed 400 KB
- ACID (Atomicity, Consistency, Isolation, Durability) compliant
  - Guarantee data validity despite errors, power failures, and other mishaps
- More: [DynamoDB Cheat Sheet](https://tutorialsdojo.com/amazon-dynamodb/?src=udemy)
- Partition Keys
  - Determines the logical partitions in which a table's data is stored
  - Effects underlying physical partitioning
  - Partition key design can effect performance
  - Should not reduce the number of partition keys
  - "Composite" partition keys provides more partitions
  - Cardinality - Number of elements in set
- Good for storing user session data

- **DynamoDB Accelerator (DAX)**
  - In-memory cache for DynamoDB
  - Speeds up interaction with DynamoDB
  - APP/USER <--> DAX <--> DynamoDB
- **DynamoDB Streams**
  - Streams provide continuous, real-time access to DynamoDB
  - Captures a time-ordered sequence of item-level modifications for up to 24 hours
  - Ordered flow of info about changes to items in DynamoDB table (PUT, DELETE, etc.)
  - There are different API endpoint for DynamoDB and DynamoDB Streams
- **Auto Scaling**
  - Default setting, however, can be changed
  - Automatically provisions and removes capacity when needed
  - Depends on CloudWatch metrics
- **Strongly Consistent Read**
  - Request returns a response with most up to date data
  - Slower than regular *eventually consistent* reads because it has to look at all replicas
  - Not supported across AWS regions
- **Global Tables**
  - Deploy multi-region database without deploying/maintaining replicas yourself
- **Encryption**
  - Can ONLY be enabled at table creation
  - Encrypted with KMS

---

## Relational Database (RDS)

- Database Engines:
  - Aurora *(AWS developed engine)*
  - PostgreSQL
  - MySQL
  - MariaDB
  - Oracle
  - SQL Server - Something doesn't work with this ????

- **Multi-AZ**
  - Enhanced durability
  - Creates a primary and secondary RDS instance in different AZs
  - Can add **read replicas** to primary and secondary database
  - **Active-Active Failover** - All regions are running and can quickly be used.
  - **Active-Passive Failover** - Secondary is on standby, and needs to be spun up on primary failure
  - Synchronous replication - highly scalable

- **Read Replicas**
  - Enhanced availability
  - Can lower read latency
  - Can be manually promoted as a standalone database instance
  - Up to 5 read replicas per master
  - Read replicas are updated asynchronously
  - Can create read replicas within AZ, cross-AZ, and cross-region
  - Asynchronous replication - highly scalable


### Aurora

- Relational database managed by AWS
- ACID (Atomicity, Consistency, Isolation, Durability) compliant
  - Guarantee data validity despite errors, power failures, and other mishaps
  - Typically involves a cluster of DB instances instead of a single instance
    - Uses **endpoints** to route requests to different DB instance(s)
    - Different instance groups can perform different functions (ie. reader, writer, user-specific, etc)
    - 
- **Aurora Serverless**
  - Only serverless relational database offered
  - On-demand, auto-scaling configuration of Aurora
  - Automatically starts up, shuts down, scales when needed
- **Aurora Global Database**
  - For globally distributed applications
  - Single database to span multiple regions
  - Globally distributed and available
  - Fast local reads, low latency in each region


---

## Elastic Cloud Compute (EC2)

- Server in the cloud
- Secure, resizable compute capacity
- Types (**FIGHT DR MCPXZ AU**):
  - **F** - FPGA (Field Programmable Gate Array)
  - **I** - IOPS (I/O Performance). High storage instance, low latency, high random IO, high sequential read throughput, high IOPS
  - **G** - Graphics-intensive GPU instances
  - **H** - High disk throughput. HDD-based local storage
  - **T** - Cheap general purpose (ie. T2 Micro). Burstable Performance.
  - _
  - **D** - Density. High Disk throughput.
  - **R** - Memory Optimized / RAM
  - _
  - **M** - General purpose apps. Fixed performance.
  - **C** - Compute optimized / CPU. Cost-effective HPC at low price.
  - **P** - Graphics / Pictures. General purpose GPU instances.
  - **X** - Extreme memory optimized
  - **Z** - Extreme memory and CPU
  - _
  - **A** - Arm-based
  - **U** - Bare Metal

### Security Groups

- Default:
  - All inbound traffic blocked
  - All outbound traffic allowed
- Can only specify allow traffic rules, but not deny rules
- Changes take effect immediately
- Cannot block specific IP addresses (need NACL for that)
- **Stateful**
  - When opening up a port, it will be applied to inbound and outbound traffic

### Network Access Control Lists (NACL)

- Security for your VPC that acts as a firewall for controlling traffic in and out of one or more subnets
- VPC comes with default NACL
  - Allows all inbound and outbound IPv4 traffic (and IPv6, if applicable)
- Custom VPC comes with NACL that denies all in and out traffic
- 1 NACL to many subnets, but NOT 1 subnet to many NACLs
- Rules are evaluated with lower number rules first
- Can use to block specific IP addresses
- Rules based on:
  - Traffic type (SSH, HTTP, etc.)
  - Port range
  - Source, Destination (IP address, CIDR)
- **Statless**
  - Seperate in and out traffic, adding inbound traffic rule will not automatically add outbound traffic rule

### EC2 Instance Meta Data and User Data

- Information available on the EC2 instance
- Can access with a `curl` command request

- **Meta Data**
  - `http://169.254.169.254/latest/meta-data/`
  - Instance ID, public keys, public IP address, private IP address, etc
  - Should not have sensitive information in meta-data
- **User Data**
  - `http://169.254.169.254/latest/user-data/`
  - Essentially your bootstrap script


---

## EC2 Network Interface Cards

1. **Elastic Network Interfaces (ENI)**
    - Utilize for normal use cases
    - When high performance is not required
    - All instances types support ENI
2. **Enhanced Network Adapter (ENA)**
    - Provides Enhanced Networking (EN) for EC2 instances
    - Up to 20 Gbps (100 Gbps for supported instances)
    - X1 instances only
    - Available: Intel Virtual Function Interface (VFI) is part of EN (10 Gbps)
      - Note: Typically the wrong answer

3. **Elastic Fabric Adapter (EFA)**
    - Provides everything ENA does plus more
    - OS-bypass functionality
    - Machine learning apps to communicate directly with network interface
    - Used for high performance computing (HPC)
    - Cannot be added to a running instance

---

## EC2 Placement Groups

1. **Cluster**
   - Group of EC2 instances that share the same AZ.
   - High Performance Computing (HPC) applications.
2. **Partition**
   - Spread instances across logical partitions.
   - Groups of instances in one partition do not share underlying hardware with different partitions.
   - Each partition group has its own set of server racks / network / power source
   - Partition 1 - Partition 2 - Partition 3
3. **Spread**
   - Strictly places small group of instances across distinct underlying hardware to reduce correlated failures
   - Each instance is on its own server rack
   - Only 7 running instances per AZ

- If you receive a capacity error when launching an instance in a placement group that already has running instances, stop and start all of the instances in the placement group, and try the launch again. Restarting the instances may migrate them to hardware that has capacity for all the requested instances.

---

## EC2 Purchase Options
  
1. **On-demand**
    - Pay as you go
    - Calculated by second
2. **Spot**
    - Request unused EC2 instances, specifying instance type and price
    - Set a bid price. If price is below your bid price, EC2 will launch a new instance
    - Gives you 2-minute warning before instance interruption
    - No guarantee that the 2-minute warning will be given
3. **Reserved**
    - Commitment to a consistent instance configuration
        - Type
        - Region
        - Term: 1 or 3 years
    - *Payment types:*
        - **All Upfront** - Payment fully made at start of term
        - **Partial Upfront** - Portion of cost paid at start of term, remaining still discounted
        - **No Upfront** - Billed at discounted hourly rate for every hour within term
    - *Offering classes:*
        - **Standard** - Instances cannot be exchanged
        - **Convertible** - Lower discount. Instances can be exchanged for different instance attributes
    - Standard Reserved Instances can be sold on the *AWS Reserved Instance Marketplace*
4. **Dedicated Hosts**
    - Physical hosts fully dedicated to running your instances
    - Have control over number of cores
5. **Dedicated Instances**
    - Pay per hour for instances that run on single-tenant hardware

---

## EC2 Storage Systems

1. **Elastic Block Store (EBS)**
    - Block-based storage = For a OS/DB file system
    - Raw, unformatted, bootable
    - Can be attached to an instance
    - Can take snapshots of it
    - Can persists after instance is terminated
    - Automatically backed up within same AZ
    - Can be used while snapshot is in progress
    - `DeleteOnTermination` Option for EBS Volumes. Set to `false` to keep the volume after the instance is terminated.
2. **EC2 Instance Store**
    - Temporary block-based storage ("Ephemeral")
    - Ideal for temp data that changes quickly (buffers, caches, etc.)
    - Only persists for the life of the instance
    - Is deleted if instance is stopped, hibernated, or terminated
    - *BUT*, data persists when instance is rebooted
    - Cannot add to an instance after it is launched.
    - Not all EC2 instance types support instance store volume.
3. **Elastic File System (EFS)**
    - File storage
    - Mounts to one or more EC2 instances (common data source)
    - POSIX-compliant (Linux)
    - Lifecycle policy of 90 days
    - NFS port 2049
    - Has lifecycle management
      - Only time frames: 7, 14, 30, 60, 90 days
      - Standard–Infrequent Access (Standard-IA)
      - One Zone–Infrequent Access (One Zone-IA) 
4. **FSx**
    - Windows-based storage, Windows-based apps or windows servers
    - High-performance computing (HPC), machine learning, electronic design automation (EDA).
5. **FSx for Lustre**
    - Machine learning, high performance computing (HPC), video rendering, and financial simulations
    - Does **NOT** support Windows-based apps or windows servers
    - Sub-millisecond latencies
    - Up to hundreds of gigabytes per second of throughput
    - Millions of IOPS
6. **Simple Cloud Storage (S3)**
    - Object-based storage
    - Can backup and store, AMIs, snapshot, etc.

---

### RAID (Redundant Array of Independent Drives)

- Data storage virtualization technology that combines multiple physical disk drive components into one or more logical units for the purposes of data redundancy, performance improvement, or both.
- Can be set up between EC2 instance stores
- Types:
  - RAID 0 - Is used for higher performance, it can combine multiple disk drives together.
  - RAID 1 - Is used for mirroring, high-availability and redundancy


### Storage Temperature

- Hot storage - storage frequently used
- Warm storage - storage less frequently accessed
- Cold storage - storage infrequently accessed


### vCPU Limit

- On-Demand Instance limit per AWS region
- EC2 -> Limits > Limit calculator
- Can request and increase in vCPU limit

---

## EBS Volume Types

1. Solid State Drives (SSD)
    - Small, random IO
    - Can be used as bootable storage for instances
    1. General Purpose - *Most cases*
        - **gp2** - 16,000 IOPS, 250 MiBs throughput - 1G - 16T
        - **gp3** - 16,000 IOPS, 1,000 MiBs throughput - 1G - 16T
    3. Provisioned IOPS SSD - *Databases*
        - **io1** - 64,000 IOPS, 1,000 MiBs throughput, 4G - 16T (99.9% durability)
        - **io2** - 64,000 IOPS, 1,000 MiBs throughput, 4G - 16T (99.999% durability)
        - **io2 Block Express** - 256,000 IOPS, 4,000 MiBs throughput - 4G - 64T (sub-millisecond latency)
        - Full IOPS only with nitro-based storage

2. Hard Disk Drives (HDD)
    - Large, sequential IO
    - Can **NOT** be used as bootable storage for instances
    - **st1** - Throughput Optimized - 500 IOPS, 500 MiBs throughput - 125G - 16T (Data Warehouses)
    - **sc1** - Cold HDD - 250 IOPS, 250 MiBs throughput - 125G - 16T (File Servers)
3. Previous Generation
    - **standard** - Magnetic Drive - 400-200 IOPS, 40-90 MiBs throughput - 1G - 1T (Infrequent Access)

- Migration to another AZ or region
  1. Take snapshot
  2. Create AMI
  3. Use AMI to launch the instance

- Encrypt a un-encrypted EBS volume
  1. Stop the instance
  2. Take snapshot of EBS volume
  3. Copy snapshot but enable encryption
  4. Make AMI with new snapshot
  5. Start instance with new AMI

---

## Storage Gateway

- Hybrid cloud service that gives you low-latency on-premice access to cloud storage
- Connects via HTTPS to AWS
- Can connect via *AWS Direct Connect*
- Useful for backups or regular access to cloud storage
- Supports *caching* on premise for lower latency on frequently accessed data
- Types:
    1. **S3 File Gateway** - Store data, files, images in S3. SMB or NFS access.
    2. **FSx File Gateway** - For Windows file servers. SMB.
    3. **Tape Gateway** - Replace physical tapes on premises with virtual tapes in AWS. S3.
    4. **Volume Gateway** - iSCSI block storage on S3. Can use AWS Backup as EBS snapshots.

---

## Direct Connect
- Direct Connect is a private/dedicated connection between on-premise network and AWS.
- Communication does not go over the public internet
- Increased bandwidth, throughput, and more consistent network experience


---

## DataSync

- Installed agent deployed as a Virtual Machine
- Can transfer data between on-premise devices and AWS storage services
  - Network File System (NFS)
  - Server Message Block (SMB)
  - Self-managed object storage
  - AWS Snowcone
- Can transfer between AWS storages
  - S3
  - EFS
  - FSx (for Windows)
- Connects through Direct Connect OR public internet
- Comes pre-installed with AWS Snowcone

---

## Snow Services

- Physical devices provided by AWS for data transfer.
- Potentially because of slow network or poor connectivity on customer side
- Aggregate and process data in edge locations, then transfer to AWS when possible
- Data is encrypted

1. **Snowcone** *(Small)*
    - 8 TB (HDD) - 4 GB Memory
    - Can use with DataSync agent
    - NOT HIPAA Compliant
    - Can be battery operated
2. **Snowball** / **Snowball Edge** *(Medium)*
    - 80 TB (HDD) - 1 TB (SSD) - 80 GB Memory
    - Edge version has GPU, 208 GB Memory
3. **Snow Mobile** *(Insanly Large)*
    - 100 PB (HDD)
    - No edge version, only for data migration

---

## Auto Scaling Group (ASG)

- Auto Scaling Policies
  - **Maintain** - Maintain current instance levels
  - **Manually/Target** - Set max, min, and desired instance count
  - **Step** - choose scaling metrics and threshold values for the CloudWatch alarms that trigger the scaling process 
  - **Schedule** - Based on some schedule, time, date, or cron expression
  - **Dynamic** - Responsive to a metric (ie. demand/load)
  - **Predictive** - Proactive based on pattern (Machine Learning)

- Cool down period
  - Prevents ASG from launching or terminating instances too quickly / before effects are visible
  - Default to 300 seconds

- Launch Configuration
  - Instance configuration template that an Auto Scaling group uses to launch EC2 instances
  - Can specify instance type and/or AMI

- EC2 Launch Template
  - Similar to launch configuration, in that it specifies instance config
  - AMI, instance type, SG, etc
  - With launch template you can have multiple versions of a template
  - May have different features than launch configurations

  - The following metrics are available for instances:
    - CPU Utilization
    - Disk Reads
    - Disk Read Operations
    - Disk Writes
    - Disk Write Operations
    - Network In
    - Network Out
    - Status Check Failed (Any)
    - Status Check Failed (Instance)
    - Status Check Failed (System)

  - The following metrics are available for ECS Service:
    - ECSServiceAverageCPUUtilization — Average CPU utilization of the service.
    - ECSServiceAverageMemoryUtilization — Average memory utilization of the service.
    - ALBRequestCountPerTarget — Number of requests completed per target in an Application Load Balancer target group.

- Auto Scaling Group EC2 Instance Termination Policy
  1. Align to spot policy
  2. Instance with AZ with most number of instances
  3. Oldest launch configuration
  4. Closest to the next billing hour
  5. Random

---

## Load Balancer Types

1. **Application (ALB)**
    - Layer 7 load balancer (application/service level)
    - Routing decisions are on the application level (HTTP/HTTPS)
    - Path based or host based routing
    - Supports port based routing
    - Supports IPv6
    - Bi-directional communication channels using WebSockets
    - Can perform load balancing for the following TCP ports: 1-65535
    - Can use AWS Certificate Manager to issue certificates
2. **Network (NLB)**
    - Layer 4 load balancer (network level)
    - Routing decisions are on the transport layer (TCP/UDP/SSL)
    - High performance (millions requests per second)
    - Can use AWS Certificate Manager (ACM)
3. **Gateway**
    - Deploy, scale, and manage third-party virtual appliances, such as security appliances
    - Deployed in the VPC of the virtual appliance, which are registered as a target for the load balancer
    - A virtual appliance inspects the traffic coming through
    - Provides both Layer 3 gateway and Layer 4 load balancing capabilities
    - [Docs Link](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
4. **Classic**
    - Handles both application (HTTP/HTTPS) and network traffic (TCP/UDP/SSL)
    - Depreciated
    - Can not convert a Application or Network load balancer to a Classic load balancer

- **Cross-Zone Load Balancing**
  -  distribute incoming requests evenly across the Availability Zones enabled for your load balancer
  -  Otherwise, each load balancer node distributes requests only to instances in its Availability Zone

---

## Kinesis Service Types

1. **Kinesis Data Streams**
    - Collect and store stream data
    - Uses shards to store data - Shards hold data max of 24 hours
    - Gigabytes of data per shard
    - Custom applications or streaming data for specialized needs
    - You are responsible to process the data pulled from the shards
    - `UpdateShardCount` - Update the number of shards in a stream
2. **Kinesis Data Firehose**
    - Handles data loading directly into AWS resources for processing (S3, Elesticsearch, Redshift, etc)
    - Automatic scaling
    - Only supports Amazon S3, Amazon Redshift, Amazon Elasticsearch
3. **Kinesis Data Analytics**
    - Analyze streaming data for insight in real time
    - Actionable insights from streaming data
    - Real time log analytics, IoT data, and machine learning
    - Can be added to Streams and Firehose

---

## Messaging Services

1. **Simple Queue Service (SQS)**
    - Amazon managed queue service
    - **Pull-based**: Consumer applications constantly poll messages from the queue
    - Queue Types:
        - **Standard** - Queue of fixed size. Out of order. High performance.
        - **FIFO** - First in, first out. Ordered.
    - Maximum retention queue period of 1 minute to 14 days
    - Cannot set queue priority level
    - If queue is not deleted by consumer, message can be processed by another consumer/processor
    - **Visibility timeout**
      - Determines how long a message stays *invisible* to other applications during processing
      - Default: 30 seconds - Maximum: 12 hours
    - `ApproximateNumberOfMessages`
      - Queue size/backlog
      - Backlog per instance can be determined with this
      - Divide by `InService` number of instances to get backlog per instance
      - With the acceptable latency and the time it takes to process, can determine to scale fleet up or down to meet process time
    - **Long Polling**
      - Instead of constanly polling for messages, set a time to wait before polling again
      - Save CPU cycles and cost
      - Maximum long polling time is 20 seconds
      - `ReceiveMessageWaitTimeSeconds` > 0 seconds (Queue attribute)
      - `RecieveMessage` > 0 seconds (In `ReceiveMessage` call)
      - Short polling - `WaitTimeSeconds` = 0
    - `DelaySeconds`
      - When new message is added to the SQS queue, it will be hidden from the consumer instances for a fixed time
      - Default: 0 - Maximum: 15 minutes
    -  **Dead Letter Queue**
       -  Isolate messages that cannot be processed right now

2. **MQ**
    - Managed message broker for ActiveMQ, Apache Qpid, and RabbitMQ
    - When the messaging service already exists
3. **Simple Notification Service (SNS)**
    - Amazon managed notification service
    - **Push-based**
    - Publisher/Subscriber (Pub/Sub) - Subscribe to topics, get notifications when a message is published to that topic
    - *Application to person*: SNS, mobile push, and email
    - *Application to application*: Lambda, SQS, Kinesis Firehose, HTTP/HTTPS, Email
4. **Simple Email Service (SES)**
    - Sending emails from their application
    - Transactional, marketing, mass email communication
    - Custom email headers, attachments, and templates
5. **Simple Workflow Service (SWF)**
    - Amazon managed workflow service
    - Task based
    - Can include human tasks, machine tasks, and workflow tasks
    - Expiration on task up to 1 year
    - Has a nice dashboard to monitor status of tasks

---

## Route53

- Domain Name System (DNS)
  - Translates human readable domain names (for example, www.amazon.com) to machine readable IP addresses (for example, 192.0.2.44).
- Connects user requests to infrastructure running in AWS
- Can also be used to route users to infrastructure outside of AWS
- Configure DNS health checks to route traffic to healthy endpoints or to independently monitor the health of your application and its endpoints

- Route 53 Routing Policies
  1. **Weighted** - Route based on percentage weights between records
  2. **Latency** - Route to the resource with the lowest latency
  3. **Geolocation** - Route to the resource *closest to the viewer*
  4. **Geoproximity** - Route to *closest resource*. Can shift traffic form one resource to another
  5. **Failover** - Route to the resource that is available and listed as secondary (Health Check)
      - *Active-Active Failover* - All resources to be available majoirty of time
      - *Active-Passive Failover* - Secondary resources on standby

- Types of Records on Route53 supported
  - A (address record) - can be root domain
  - AAAA (IPv6 address record)
  - CNAME (canonical name record) - based on subdomains
  - CAA (certification authority authorization)
  - MX (mail exchange record)
  - NAPTR (name authority pointer record)
  - NS (name server record)
  - PTR (pointer record)
  - SOA (start of authority record)
  - SPF (sender policy framework)
  - SRV (service locator)
  - TXT (text record)

---

## CloudWatch

-  Provides you with data and actionable insights to monitor your applications, respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health
- EC2 instances are monitored by default
- Monitoring interval:
  - Default: 5 minutes
  - Detailed: 1 minute

- **CloudWatch Metrics**
   1. CPU
   2. Disk Utilization
   3. Network Activity
- **CloudWatch Agent**
  - Installed on running EC2 instance
  - Collects more system-level metrics
      1. Memory
      2. Disk swap
      3. Disk space
      4. Page file
      5. Logs

---

## Lambda

- Serverless -  no need to manage servers (like EC2)
- A function that runs in response to an event
- Can be triggered by various service invocations:
  - *Synchronous:* API Gateway, ELB, Step Functions, Kinesis Firehose, Cognito, Alexa, Lex
  - *Asynchronous:* S3, SNS, SES, CF, CW Logs/Events, CodeCommit, Config, IoT, CodePipeline, DynamoDB Streams
- AWS CodePipeline and AWS OpsWorks can **NOT** invoke lambda functions.
- Scales out (not up) automatically
- 1 event = 1 function
- Can be debugged by X-Ray
- Lambda can read events from:
  - Kinesis
  - DynamoDB
  - SQS
- Limits
  - Function memory allocation: 128 MB to 3008 MB (in 64 MB increments)
  - Function timeout: 900 seconds.
  - Deployment package: 50 MB * 5 layers.
  - tmp directory storage: 512 MB.

- **Lambda@Edge**
  - Enables you to run your Lambda functions in the edge of your network.
  - Run Lambda functions in AWS locations closer to viewers
  - Functions run in response to CloudFront Events
  - Viewer --> **viewer request** --> CloudFront --> **origin request** --> Origin/Server
  - Viewer <-- **viewer response** <-- CloudFront <-- **origin response** <-- Origin/Server
  - Customize content that CloudFront delivers
      1. **viewer request** - After CloudFront receives a request from viewer
      2. **origin request** - Before CloudFront forwards request to origin/servers
      3. **origin response** - After CloudFront receives the response from origin/servers
      4. **viewer response** - Before CloudFront forwards response to viewer


## Gateway API

- Create, publish, maintain, monitor, and secure APIs at any scale
- RESTful APIs and WebSocket APIs
- Usually works perfectly with Lambda to create a serverless application
- Traffic management
- Cross-origin resource sharing (CORS) support
  - Restricts cross-origin HTTP requests that are initiated from scripts running in the browser
- Request Throttling
  - Prevent your API from being overwhelmed by too many requests
  - When request submissions exceed the **steady-state request rate** and **burst limits** -> Client error `429 Too Many Requests`
- Monitoring
- Authorization and access control
- Automatically protects the backend systems from DDoS attack
- All of the APIs created with Amazon API Gateway expose *HTTPS endpoints only*.
  - Amazon API Gateway does not support unencrypted (HTTP) endpoints.
  - By default, Amazon API Gateway assigns an internal domain to the API that automatically uses the Amazon API Gateway certificate.

---


## Key Management Service (KMS)

- Create and manage cryptographic keys to encrypt data at rest
- Validated under **FIPS 140-2** - Can show proof of tempering
- Integrated with AWS CloudTrail to provide you with logs of all key usage to help meet your regulatory and compliance needs
- Server-side encryption
  - Each object is encrypted with a unique key
  - **S3-Managed Keys (SSE-S3)**
    - S3 manage the data and the encryption keys
    - Encrypts the key itself with a master key that it regularly rotates
    - Can add in policy `"s3:x-amz-server-side-encryption": "AES256"`
  - **KMS-Managed Keys (SSE-KMS)**
    - Provides you with an audit trail that shows when your CMK was used and by whom
  - **Customer-Provided Keys (SSE-C)**
    - You manage the encryption key
    - Have to manage/rotate yourself
    - Can add in policy `x-amz-server-side​-encryption​-customer-algorithm`


## CloudHSM

- Cloud-based hardware security module (HSM)
  - Physical device that:
    - Safeguards and manages digital keys
    - Performs encryption and dycription
- Generate and use own encryption keys
- Seperate cluster in separate VPC
- Custom keys
- **FIPS 140-2 level 2** ([Wiki](https://en.wikipedia.org/wiki/FIPS_140-2#Level_2))
  - Show evidence of tampering
- **FIPS 140-2 level 3** ([Wiki](https://en.wikipedia.org/wiki/FIPS_140-2#Level_3))
  - Show evidence of tampering
  - Prevent intruder form gaining access to critical security parameters (CSP)
  - Detect and respond to attempts to access


## AWS Inspector

- Automated security assessment service
- Helps improve security and compliance of applications deployed on AWS


## X-Ray

- Helps debug and analyze your micro-service and serverless app with request tracing
- Provides an end-to-end view of requests as they travel through your application
- Can find root causes of issues and performance


## Directory Service

- Managed Microsoft Active Directory in AWS
- Enables AWS resources to use managed Active Directory for authentication
- **Managed Microsoft AD**
  - Build on actual Microsoft AD
  - Does not require you to sync or replicate data from your existing AD to cloud
  - SAML (Security Assertion Markup Language)
- **Directory Service Simple AD**
  - Standalone managed directory service
  - Small - 500 users, Large - 5,000 users
  - Subset of features offered by AWS Managed Microsoft AD
  - Does not support SSO
  - Can use with Directory Service AD Connector
- **Directory Service AD Connector**
  - Directory gateway with which you can direct directory requests to your on-premises MS AD without caching any info in the cloud
  - Cannot share accounts, not multi-VPC

## AWS Backup

- Manage and automate backup across AWS services
- Backup plans
- backup retention rules and lifecycle rules
- Monitor backup activity


## Step Function

- State machine
- Low-code visual workflow service used to orchestrate AWS services / build serverless applications
- Has a drag-and-drop user interface


## Elastic Map Reduce (EMR)

- Framework for processing parallelizable problems across large data sets using large number of nodes (ie. in cluster or grid)
- Processing vast amounts of data using open source tools
  - Apache Spark
  - Apache Hadoop
  - etc
- Petabyte-scale analysis at half the cost
- Separates storage and compute
  - Store data in S3
  - Run compute as you need it when processing the data
- Can setup EMR cluster in minutes
- Can run multiple clusters in parallel on the same data set


## Data Cycle Manager (DLM)

- Manage lifecycle of your AWS resources
- Automate operations on specified resources
- Can do automated EBS volume snapshots


## OpsWorks

- Automate instance configuration management with **Chef and Puppet**
- Uses configuration as code


## Trusted Advisor

- Recommendations to help follow AWS best practices
  - Cost Optimization
  - Security
  - Performance
  - Fault Tolerance
  - Service Limits
- Monitor your AWS resources


## ElastiCache

- Fast in-memory data store for use with database, cache, message broker, and queue
- Ephemeral data sub-millisecond response
- Uses open-source systems
- Can be good to store and share user session data

- **Redis**
  - In-memory data store
  - Advanced Data structures
  - Pub/Sub Architecture
  - Snapshots and replication
  - Increasing redis security
    - `AUTH` - -  command can improve data security by requiring user to enter password
    - `--auth-token` - Parameter to enter password/token
    - `--transit-encryption-enabled` - Optional, to encrypt data in transit
- **MemcacheD**
  - Object caching
  - Multi-threaded architecture


## Systems Manager Parameter Store VS Secret Manager

- **Parameter Store** - Manage parameters for your AWS resources
- **Secret Manager** - Manage secrets for your AWS resources
- Similarity
  - Managed key/value store
  - Similar encryption options. Both can be encrypted using KMS.
  - Reference in CloudFormation
- Difference
  - Secrets Manager can generate passwords
  - Secrets Manager can rotate secrets
  - Secrets Manager costs slightly more
  - Secrets Manager allows secrets to be shared across accounts
  

## Security Token Service (STS)

- Generate temporary security credentials
- Used to access services that require AWS authentication
- Used for API access
- Temporarily grant access to AWS services


## Glue

- Fully managed Extract, Transform, and Load (ETL) service
- Serverless data integration service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and application development.
- Data integration is the process of preparing and combining data for analytics, machine learning, and application development.
- Provides both visual and code-based interfaces to make data integration easier


## System Manager (Formerly known as Simple Systems Manager (SSM))

- View and control your infrastructure on AWS
- Can view operational data from multiple AWS services and automate operational tasks across your AWS resources
- A managed instance is a machine configured for use with Systems Manager
- Supported machine types
  - EC2
  - On-Premise servers
  - Virtual Machines
  - VMs in other cloud enviroments
- Can associate AWS resources together by applying the same identifying resource tag
- Can group resources together so you can mange and monitor them as a group
- **System Manager Agent (SSM Agent)**
  - Can be installed and configured on an EC2 instance, an on-premises server, or a virtual machine (VM)
  - Update, manage, and configure these resources
- **Run Command**
  - Lets you remotely and securely manage the configuration of your managed instances
    - A managed instance is any Amazon EC2 instance or on-premises machine in your hybrid environment that has been configured for Systems Manager. 
  - Enables you to automate common administrative tasks and perform ad hoc configuration changes at scale.
  - Run Command from the console to configure instances without having to login to each instance


## Network Address Translation (NAT) Gateway

- Enables that instances in a private subnet can connect to services outside your VPC, but external services cannot initiate a connection with those instances.
- Use this instead of a NAT instance (custom approach)
  - NAT instances has to be managed by customer
  - NAT instance has to set NACL restrictions off ????
- Should have more than one in a AZ for availability
- Supports TCP, UDP, ICMP
- Only applicable for IPv4 and not IPv6
- Not supported for IPv6 traffic (use egress-only internet gateway)
- You can associate exactly one Elastic IP address with a public NAT gateway
- You cannot associate a security group with a NAT gateway
- You can use a network ACL to control the traffic to and from the subnet for your NAT gateway
- NAT gateways use ports 1024–65535


---

# Other Notes

- CloudFront
  - Content Delivery Network (CDN) at the edge of the web
  - Seamlessly integrated with AWS Shield, AWS Web Application Firewall and Amazon Route 53

- Global Accelerators
  - Networking service that improves the performance of your users’ traffic
  - Optimizes the path to your application
  - Provided two global static public IPs that act as a fixed entry point to your application, improving availability
    - Can also bring your own public IPs
  - Acceleration varies by region

- VPC Flow Logs
  - Enables you to capture information about the IP traffic going to and from network interfaces in your VPC
  - Flow log data can be published to Amazon CloudWatch Logs or Amazon S3
  - Can create a flow log for a VPC, a subnet, or a network interface
  - Can use the default format for the flow log record, or you can specify a custom format

- VPC Endpoints
  - Privately connect your VPC to supported AWS and VPC endpoint services powered by AWS PrivateLink without needing an Internet gateway, NAT computer, VPN connection, or AWS Direct Connect connection
  - Instances do not require a public IP address
  - Traffic stays within AWS network

- Neptune 
  - Graph database

- CloudTrail
  - Record of an activity in an AWS account
    - Action taken by a user, role, or service
  - Both API and non-API account activity made through the AWS Management Console, AWS SDKs, command-line tools, and other AWS services
  - Events to track:
    - Management - On by default
    - Data - Not default, but can be enabled
  - AWS CLI:
    - `--include-global-service-events` - Enable to track all AWS services
    - `--is-multi-region-trail` - Enable to track events in all regions
  - By default, event log files are encrypted using Amazon S3 server-side encryption (SSE)
  - Can store your log files in your bucket for as long as you want.
  - Can also define Amazon S3 lifecycle rules to archive or delete log files automatically.



- Redshift
  - data warehouse - Reporting and data analysis of data
  - Can query and combine exabytes of structured and semi-structured data across your data warehouse, operational database, and data lake using standard SQL
  - Lets you easily save the results of your queries back to your S3 data lake
  - **Redshift spectrum analytics** - Complex queries on data stored in S3
  - **Cross-Region-Snapshots Copy**
    - Needs to be enabled separately, not default
    - All new manual and automatic snapshots are copied to the specified region

- Config
  - Assess, audit, and evaluate the configurations of your AWS resources
  - Continuously monitor and record AWS resources configs and allow you to automate the evaluation of recorded configs against desired configurations
  - Different than cloudtrail in that *cloudtrail cannot enforce rules to comply with or policy*

- AWS Resource Manager
  - Share AWS resources (Transit gateways, licence configs, Route 53 Resolver rules)
  - Elimiates need to create duplicate resources in multiple accounts

- Transit Gateway
  - Simplify the connectivity between multiple VPCs and also connect to any VPC attached to AWS Transit Gateway with a single VPN connection.
  - Enables you to scale the IPsec VPN throughput with equal-cost multi-path (ECMP) routing support over multiple VPN tunnels
  - A single VPN tunnel still has a maximum throughput of 1.25 Gbps

- CodeDeploy
  - deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers
  - rapidly release new features
  - update Lambda function versions
  - avoid downtime during application deployment
  - handle the complexity of updating your applications

- Macie
  - Machine Learning (ML) powered security service
  - Recognize sensitive data such as personally identifiable information (PII) or intellectual property, assigns a business value

- Elastic IP (EIP)
  - Atatic IPv4 address designed for dynamic cloud computing.
  - Yours until you release it
  - Doesn’t incur charges as long as the following conditions are true:
     - The Elastic IP address is associated with an Amazon EC2 instance.
     - The instance associated with the Elastic IP address is running.
     - The instance has only one Elastic IP address attached to it.

- **Egress-only Internet gateway**
  - Use with IPv6 traffic only
- Fargate - Serverless container technology for EMR or ECS
- Active Directory / SAML
- How to implement Single-Sign-On SSO?
- Basics on how to use Gateway API with lambda
-  POSIX-compliant - works on all kinds of UNIX systems
- VPN is established over a Virtual Private Gateway.
- S3 Bucket gateway endpoint?
- `EC2ThrottledException` - Error when VPC not sufficient ENIs or subnet IPs for Lambda/EC2 to scale
- Remote Desktop Protocol (RDP) - Port 3389
- Secure Shell (SSH) - Port 22
