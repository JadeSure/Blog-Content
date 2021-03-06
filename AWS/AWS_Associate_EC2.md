---
layout: post
title: Install TensorFlow-GPU by Anaconda (conda install tensorflow-gpu)
subtitle: It might be the simplest way to install Tensorflow or Tensorflow-GPU by conda install in the conda environment
date: 2019-03-29
author: Wang Pei
header-img: img/machine-learning.jpg
catalog: true
tags:
  - ubuntu
  - TensorFlow
  - deep learning
  - CUDA
---

## AWS Regions

Regions all around the world. eg. us-east-1, eu-west-3...  
A region is a cluster of data centers

#### How to choose an AWS Region?

1. compliance with data governace and legal requirements: data never leaves a region without your explicit permission.
2. Proximity to customers: reduce latency.
3. Available services: not all services available in every region.
4. Pricing: pricing varies region to region and is transparent in the service pricing page.

#### AWS Availability Zones

1. Each region has many availability zone(AZ). (2 ~ 6) eg. ap-southeast-2a, ap-southeast-2b, ap-southeast-2c(they are separate with each other(isolated from disasters), but connected with high bandwidth, utlra-low latency networking.)
2. Each AZ is one or more discrete data centers with redundant power, networking and connectivity.

#### AWS global services

IAM, Route53(DNS service), CloudFront(Content Delivery Network), WAF(Web Application Firewall)

#### AWS services region-scoped

EC2(infrastructure as a service), Elastic Beanstalk(platform as a service), Lambda(function as a service), Rekognition???(software as a service)

1 region -> many AZ
1 AZ -> many discrete data centers

## IAM: Identity and Access Management

1 user => many gourps  
1 gourp => many users  
1 user cannot have a group  
1 group cannot contain another group  
In AWS, you apply the least privilege principle; don't give more permissions than a user needs.

#### IAM Policies in heritance

inline policy: only assign policies to a single person rather by a group.
!(IAM_Policies_Structure)[]

#### IAM Password Policies

1. strong Password with rules setting
2. Multi Factor Authentication - MFA = password you know + security device you own (for root account)  
   eg. Virtual MFA device: Google Authenticator, Authy; and Universal 2nd Factor (U2F) Security Key

#### How can users access AWS?

1. AWS Management Console (protected by password + MFA)
2. AWS Command Line Interface(CLI): protected by access keys(Access Key ID = username, Sercret Access Key = password)
3. AWS Software Developer Kit(SDK) - for code: protected by access keys

#### Config AWL CLI

```js
aws configure
  : aws access key -> aws secret access key -> Default region name
aws iam list-users (check all users in ur account)
```

#### What is Cloud Shell?

like a Cloud Terminal :)

#### IAM Security Tools(Audit permissions of your account)

IAM Credentials Report(account-level): a report that lists all your accounts users and the status of their various credientials(get info about all ur users)  
IAM Access Advisor(user-level): Access advisor shows the service permissions granted to a user and when those services were last sucessed(audit specific user permission for a single user).

#### Summary

Users: personal accessibility;  
group: many users have same permissions;  
Roles: the service in AWS communicate with other services in AWS (defines what you can do after you authenticate). the permission of one service to access another service.e.g. EC2, S3;  
Policies: Json documents (the permission of users/group can do aws services); Json documents that define a set of permissions for making requests to AWS services, and can be used by IAM Users, User Groups, and IAM Roles. the permission of document for the templates for specific access rights; How many resources can be accessed;  
one Role has many policies;  
one group has many users.

## AWS EC2: Elastic Compute Cloud

Renting virtual machine(EC2)  
Storing data on virtual drives(EBS)  
Distributing load across machines(ELB: Elastic Load Balancer)  
Scaling the services using an auto-scaling group(ASG)

#### EC2 Instance types

eg. m5.2xlarge  
m: instance class  
5: generation
2xlarge: the size within the instance class (small => large => 2xlarge => 3xlarge....)

#### Security Groups(control traffic into & out for ur EC2 instances)

1. ecurity groups only contain allow rules
2. Security groups rules can referred by IP or by security group
3. inbound: specific port and IP address
4. outbound: www to any IP and any port
5. one security group can have many instances
6. one instance can have many security groups
7. Security groups is locked down to a region/VPC combination
8. Security is a firewall for EC2 - if traffic is blocked, EC instance wont see request.
9. it is better to seperate security group for SSH access
10. time out error? => security group issue
11. connection refused? => application error or its not launched
12. port 22 = SSH
13. port 21 = FTP (File Transfer Protocol )
14. port 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
15. port 80 = HTTP - access unsecured websites
16. port 443 = HTTPS - access secured websites
17. 3389 = RDP(Remote Desktop Protocol) - log into a Windows instance

#### Inbound and outbound

Inbound: server can get access into(including http request)  
outbound: allow ec2 instance to get full internet connectivity anywhere.

#### Instance types

Instance Type Family:

1. Accelerated computing: P3, P2, G3, F1 (machine learning, Neural network)  
   based on cost for the instance types:
2. On-Demand Instances: when you use, you cost; before have a higher volumn to use it. billing per second. No long-term commitment. for short-term and un-interrupted workloads, where you cannot predict how application will behave. e.g. Black Friday. (???????????????????????????)
3. Reserved Instances: a contract that server one year(maybe). it becomes cheaper with longer development. (?????? instances???????????????????????????)
4. Spot Instances: during peak computing level, it needs more money. The MOST cost-efficient instances in AWS. (??????)
5. Scheduled Reserved Instances: periodically use server. (???????????????)
6. Dedicated Instances & Dedicated Hosts: VIP~! Host: A physical server with EC2 instance capacity. Instances: A hardware that's dedicatd to you.(??????????????????)
7. Saving Plans: commit to a certain type of usage ($10/hour for 1 or 3 years). When it beyond EC2 Saving plans => on-demand price. And locked to a specific instance family & AWS region.(??????????????????????????????????????????????????????)
8. Capacity Reservations: Reserve on-demand instances capacity. suitable for short-term, uninterrupted workloads that needs to be in a specific AZ.(?????? on-demand instances?????????????????????????????????)

#### How to terminate spot instance

1. you can only cancel spot instance requests that are open, active or disabled.
2. cancel spot request first then terminate the associated spot instances.

#### What is Spot Fleets?

spot fleets = set of spot instances + (optional)on-demand instances  
spot fleet stops launching instances when reaching capacity or max cost  
strategies:

1. lowest price from the launch pools(instance type, OS, AZ...)
2. diversified: distributed across al pools (great for availability, long workloads)
3. capacityOptimized: pool with the optimal capacity for the number of instance

#### Elastic IP Address

fixed IP address(public)  
try to avoid using elastic IP, instead, use a random public IP and register a DNS name to it.  
or we use a load balancer and dont use a public IP

#### Placement Groups

1. cluster: clusters instances into a low latency group in a single AZ(all EC2 on the same rack/hardware. eg. big data processing)
2. spread: spreads instances across underlying hardware - critical applications; EC2 instances are on different physical hardware; (limited to 7 instances per AZ per placement group)
3. partition: spreads instances across many different partitions through multiple AZs in the same region; The instances in a partition do not share racks with the instances in the other partitions. (isolated from another partition of failure; up to 7 partions per AZ; up to 100s of EC2 instances; eg. HDFS, Hbase, Apache Kafka.)

#### Elastic Network Interfaces (ENI)

A virtual network card  
ENI:

1. primary IPV4, one or more secondary IPV4
2. one Elastic IP(IPV4) per private IPV4
3. one public IPV4
4. one or more security groups
5. a MAC address
   attach them on the fly on EC2 instances for failover  
   bound to a specific AZ(whitin the same AZ)

#### EC2 Hibernate(??????)

The RAM state is preserved in EBS;
prerequisites:

1. The RAM state is written to a file in the root EBS volume;
2. The root EBS volume must be encrypted.

#### EC2 Nitro

1. Better networking options (enhanced networking, HPC, IPV6)
2. Higher Speed EBS (Nitro is necessary for 64000 EBS IO operations - max 32000 on non-Nitro)

#### EC2 - vCPU

1. Each thread is represented as a virtual CPU (vCPU)  
   eg. m5.2xlarge => 8 vCPU = 4 CPU \* 2 threads per CPU
2. based on requirements to change CPU cores, threads per cores

#### EC2 Capacity Reservations

1. you have EC2 Capacity when needed
2. Capacity access is immediate, u get billed as soon as it starts

## EC2 Instance Storage Section

EBS: Elastic Block Store(connected by network)  
It can only be mounted to one instance at a time (at the CCP level); However, two EBS can attached within one EC2.  
When it created, it can only be bounded in a specific AZ;  
after snapshot, it can be moved a volumn across different AZ;  
EC2 Instance Store

## EC2 instance metadata

Metadata = info about the EC2 instance  
Userdata = launch script of the EC2 instance

#### EBS Snapshots

1. Make a backup of your EBS volume at a point in time and copy snapshots across AZ or Region;
2. Recycle Bin for EBS Snapshots that retains delted snapshots which can be recovered them after an accidental deletion. (within one year to recover it.)

#### AMI: Amazon machine image

AMIs are built for a specific AWS Region, they're unique for each AWS Region. You can't launch an EC2 instance using an AMI in another AWS Region, but you can copy the AMI to the target AWS Region and then use it to create your EC2 instances.

#### EBS Volume Types

1. gp2/gp3(SSD): General purpose SSD volume
2. io1/io2: Highest-performance SSD volume for mission-critical low lattency or high-throughput workloads
3. st 1(HDD): low cost HDD volume for frequently accessed(for archive)
4. sc 1 (HDD): Lowest cost HDD volume designed for less frequently accessed

io2 volume with Multi-Attach: one io2 volume can be attached to multi-ec2(concurret processing)
can attach the same EBS volume to multiple EC2 instances in the same AZ. Each EC2 instance has full read/write permissions.

#### Different between gp2 and gp3?

size, throughoutput, IO operations  
gp3 can independently set the IOPS and throughput

#### AWS EFS: Elastic File System

Managed NFS (Network File System) that can be mounted on many EC2 with mult-AZ;

1. Pay per user
2. only work in Linux (POSIX file systems)
3. Highly available, scalable, expensive
4. use security group to control access to EFS (inbound)
5. storage classes: EFS Scale, Performance mode, Throughtput move
6. storage Tiers (lifecycle management feature - move file after N days(budget choice))

#### The difference between EBS & EFS? (Udemy 69)

!(IAM_Policies_Structure)[EBSvsEFS1.png]
!(IAM_Policies_Structure)[EBSvsEFS3.png]

## High availabitlity and Scalability ELB & ASG

Scalability: vertical scalability and horizontal scalability

1. vertical: more strong CPU limited by hardware
2. scalability: more tiny EC2 CPUs
   High availability: to survive a data center loss:
3. be passive: for RDS multi AZ
4. be active: for horizontal scaling

#### Load Balancers

1. spread load across multiple downstream???????????? instances
2. Expose a single point of access (DNS) to your application
3. Seamlessly handle failures of downstream instances
4. Do regular health checks to your instances
5. Provide SSL termination(HTTPS) for your website
6. High availability across zones
7. Enforce stickness with cookies
8. Separate public traffic from private traffic

##### ALB: Application Load Balancer (V2)

1. Application load balancers is Layer 7 (HTTP)
2. load balancing to multiple HTTP applications across machines(target groups)
3. loading balancing to multiple applications on the same machine(eg. containers)
4. support HTTP/2 and web socket
5. support redirects (from HTTP to HTTPS in load balancer level)
6. suitable for micro services & container-based application

When using an Application Load Balancer to distribute traffic to your EC2 instances, the IP address you'll receive requests from will be the ALB's private IP addresses. To get the client's IP address, ALB adds an additional header called "X-Forwarded-For" contains the client's IP address.

##### Target Groups

1. EC2 instances can be managed by an Auto Scaling Group - HTTP
2. ECS tasks (managed by ECS itself) - HTTP
3. Lambda functions - HTTP request is translated into a JSON event
4. ALB can route to multiple target groups
5. Health checks are at the target level
6. Talk with each other must based on private IP

##### NLB: Network load balancer (Layer 3)

deploy scale and manage a fleet of 3 party network virtual appliances in AWS eg. Firewalls, Intrusion Detection and prevention systems  
uses the GENEVE protocol on port 6081

Only Network Load Balancer provides both static DNS name and static IP. While, Application Load Balancer provides a static DNS name but it does NOT provide a static IP. The reason being that AWS wants your Elastic Load Balancer to be accessible using a static endpoint, even if the underlying infrastructure that AWS manages changes.  
Network Load Balancer provides the highest performance and lowest latency if your application needs it.  
Network Load Balancer has one static IP address per AZ and you can attach an Elastic IP address to it. Application Load Balancers and Classic Load Balancers have a static DNS name.

##### Sticky Sessions (session Affinity)

same client is always redirected to the same instance behind a load balancer(cookie and session)  
for classic load balancers & application load balancers

1. Applciation-based Cookies
   Custom cookie
   1. generated by the target
   2. include any custom attributes required by the application
   3. Cookie name must be specified individually for each target group  
      Application cookie
   4. generated by the load balancer
   5. cookie name is AWSALBAPP
   6. The following cookie names are reserved by the ELB (AWSALB, AWSALBAPP, AWSALBTG).
2. Duration-based Cookies
   1. generated by the load balancer

##### Cross-Zone Load Balancing

Each load balancer instance distributes evenly across all registered instances in all AZ.

#### SSL/TLS?

allows traffic between your clients and your load balancer to be encrypted in transit.  
SSL: Secure Socket Layer -> encrypt connections  
TLS: Transport Layer Security  
HTTPS listener:

1. client use SNI(Server Name Indication) to specify the host name they reach
   1. SNI solves the problem of loading multiple SSL certificates onto one web server(to serve multiple webistes)
   2. only work for ALB & NLB and CloudFront
   3. CLB only work for one certificate
      Server Name Indication (SNI) allows you to expose multiple HTTPS applications each with its own SSL certificate on the same listener. Read more here: https://aws.amazon.com/blogs/aws/new-application-load-balancer-sni/

#### Connection Draining ????????????

1. Connection Draining - for CLB
2. Deregistration Delay - for ALB & NLB

Time to complete "in-flight requests" while the instance is de-registering or unhealthy. (eg. uploading videos...)

#### Auto Scaling Group (ASG)

scaled in/out based on Cloud Watch Monitor  
Good metrics to scale on:

1. CPU utilization(?????????): average CPU utilization across your instances
2. RequestCountPerTarget: to make sure the number of requests per EC2 isntances is stable
3. Average Network in/out
4. Any custom metric

ASG Default Termination Policy:

1. find the AZ which has the most number of instances
2. if there are multiple instances in the AZ to choose from, delete the one with the oldest launch config.  
   ps: ASG tries the balance the number of instances across AZ by default

!(ASG_Lifecycle_hooks)[ASG_lifecycle_hooks.png]
use ASG lifecycle hooks to pause the EC2 instance in the terminating state for troubleshooting.

Lauch template is better!!!(new)

1. multi versions
2. create params subsets (partial config for re-use and inheritance)
3. provision using both on-demand and spot instances

## RDS: relational database service(Storage Auto Scaling) + Aurora + ElastiCache

1. Set Maximum Storage Threshold to DB storage
2. Automatically modify storage:
   1. Free storage is less than 10% of allocated storage
   2. low-storage lasts at least 5 minutes
   3. 6 hours have passed since last modification
3. Useful for applications with unpredictable workloads
4. RDS (NOT Aurora) feature when used does not require you to change the SQL connection string: Multi-AZ keeps the same connection string regardless of which database is up.
5. for a RDS database, you can have up to 5 read replicas
6. Oracle does not support IAM database authentication

### database replica and RDS multi AZ (disaster recovery)

1. replica is only work for READ from database that can increase read speed, and this is no async transaction fee in the same region
2. standby(?????????) database as a failover
   The read replicas be setup as multi AZ for disaster recovery(DR)
3. Read replica uses asynchronous replication and multi-AZ uses synchronous replication

### From Single-AZ to Muli-AZ

1. Zero downtime operation (no need to stop the DB)
   1. a snapshot is taken
   2. a new DB is restored from the snapshot in a new AZ
   3. Sync is estabilished between two databases

### RDS Security - Encryptions

At rest encryption

1. possiblity to encrypt the master & read replicas with AWS KMS encryption
2. encryption has to be defined at launch time
3. if the master is not encrypted, the read replicas cannot be encrypted

In-flight encryption

1. or unencrypted DB => snapshot => copy snapshot as encrypted => create DB from snapshot

### Aurora DB

!(AWS_Aurora)[AWS_Aurora.png]

1. automatic failover
2. backup and recovery
3. isolation and security
4. industry compliance
5. push-button Scaling
6. automated patching with zero downtime
7. advanced monitoring
8. routing maintenance
9. backtrack: restore data at any point of time without using backups
10. can have mutli-master nodes
11. Aurora Global Databases allows you to have an Aurora Replica in another AWS Region, with up to 5 secondary regions.
12. maximum 15 aurora read replicas that you can have in a single Aurora DB cluster

### Amazon ElastiCache

caches are in memory databases: helps reduce load off of databases for read intensive workloads
!(REDIS_vs_MEMCACHED)[REDIS_vs_MEMCACHED.png]
Redius can be used in leaderboards game that sorted sets gurantee both uniqueness and element ordering  
each time a new element added, its ranked in real time, then added in correct order

### Patterns for ElasticCache

1. Lazy loading: all the read data is cached, data can become stale in caches
2. write through: adds or update data in the cache when written to a DB(no stale(?????????) data)
3. session storage: store temporary session data in a cache(using TTL features)

## Route 53

1. a highly available, calable, fully managed and Authoritative(the customer can update the DNS records) DNS
2. Route 53 is also a Domain registration
3. Ability to check the health of your resources
4. provide 100% availability SLA(server level agreement)

### Record Types

1. A - maps a hostname to IPv4
2. AAAA - maps a hostname to IPv6
3. CNAME - maps a hostname to another hostname (must have an A or AAAA record already; cannot create example.com => www.example.com)
4. NS - Name Servers for the Hosted Zone(control how traffic is to routed for a domain)

### Hosted Zones?

A container for records that define how to route traffic to a domain and its subdomains

1. public hosted zones - application.mypublicdomain.com (public domain names)
2. private hosted zones - route traffic within one or more VPCs (private domain names) : application.company.internal

## AWS SDK Overviews

if you dont specify or configure a default region, then us-east-1 will be chosen by default

## SQS, SNS, Kinesis

SQS: queue model(decoupling applications)  
SNS: pub/sub model  
Kinesis: real-time streaming model

You have enabled versioning and want to be extra careful when it comes to deleting files on an S3 bucket. What should you enable to prevent accidental permanent deletions? Enable MFA Delete

You are looking to get recommendations for S3 Lifecycle Rules. How can you analyze the optimal number of days to move objects between different storage tiers? S3 Analytics

You are looking to build an index of your files in S3, using Amazon RDS PostgreSQL. To build this index, it is necessary to read the first 250 bytes of each object in S3, which contains some metadata about the content of the file itself. There are over 100,000 files in your S3 bucket, amounting to 50 TB of data. How can you build this index efficiently?
