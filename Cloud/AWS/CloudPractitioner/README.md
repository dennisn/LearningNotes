# AWS Certified Cloud Practitioner
- Exam CLF-C02
- Learning material: [https://skillbuilder.aws/learn/94T2BEN85A/aws-cloud-practitioner-essentials]

## 01. Introduction to Cloud
- Benefits
  1. Fixed expense for variable expense (operational cost)
  2. Benefit from economies of scale
  3. Stop guessing capacity needed
  4. Increase speed & agility (i.e. faster provision)
  5. Outsource cost of data centers
  6. Fast global deployment
- Shared responsibility model:
  - "Of the cloud": Physical layers of security (e.g. physical, hardware, etc.) ==> AWS
  - "In the cloud": Application layers of security (e.g. client data, OS patches, etc.) ==> User
  - Middle layers of security: data storage encryption, networking, etc. ==> Shared, depending on service
- Region & Availability zones:
  - AZ: 1+ data center with redundant physical facilities
  - Region: multiple AZs for fault-tolerance

## 02. Compute in the Cloud
- EC2: Amazon Elastic Compute Cloud
  - Virtual machine with choice of OS & apps: type of instance & OS
  - Share resources from a host: multi-tenancy
  - Can grow/shrink as needed
- EC2 instance types:
  - General purpose: web services, code repositories
  - Compute optimized: gaming servers, ML, scientific modeling
  - Memory optimized: processing large datasets, data analytics
  - Accelerated computing: use hardware accelerators (e.g. GPU): graphic processing, floating point calculations, ML
  - Storage optimized: databases, data warehousing, IO-intensive applications

### Provision AWS resources
- AWS Management console: visual interfae
- AWS CLI: suitable for automate tasks, script actions
- AWS SDK: for developers to integrate AWS services into applications using language-specific APIs

### EC2 pricing
1. On-demand: no upfront payments or long-term commitments
   - For critical workloads with strict capacity requirement --> `Capacity Reservation` to ensure available compute when needed, but can't reduce resources during "delivered" periods
2. Savings plans: commit to a consistent usage for 1/3 years for up to 72% savings (vs. On-demand)
   - Apply for EC2, Fargate, Lambda, SageMaker AI, regardless of instance type or AWS region
   - Can pay upfront All/partial, or 100% pay later
3. Reseved instances: commit to 1/3 years term for predictable workloads with specific instance types & AWS Regions --> up to 75% saving
4. Spot instances: bid on spare compute capacity --> up to 90% saving, but maybe interrupted when AWS reclaims the instance
5. Dedicated instances: instances dedicated solely to your accounts
6. Dedicated hosts: exclusive use of physical servers --> ideal for security/compliance driven workloads

### Scaling
- Scalability: ability to handle increased workload by adding resources: up (vertically) or out (horizontally)
- Elasticity: ability to `automatically` scale resources `up or down` in response to real-time demand

#### EC2 Auto Scaling 
- Two approaches:
  1. Predictive scaling: schedule the scaling based on anticipated demand
  2. Dynamic scaling: adjust in real-time to fluctuations in demand
     - Often based on monitoring service such as `CloudWatch`
- Scaling with "Auto Scaling group": collections of EC2 instances that can be scaled together
  - Min/max capacity: the min/max number of instances --> control cost & ensure availability
  - Desired capacity: the ideal number of instances

### Load Balance
- Can use off-the-shelf, or AWS Elastic Load Balancing (ELB) service
- ELB benefits:
  - Distributes traffic across "all" instances --> prevent overload on any single instance
  - Seamless scaling --> adapt to add/remove of instances
  - Simplify managment: decouple front-end & back-end --> reduce manual synchronisation
- Routing methods:
  - Round Robin: basic
  - Least Connections --> to server with fewest active connections
  - IP Hash --> consistently route traffic to same server for same client
  - Least Response Time --> minimize latency

### Messaging and Queuing
- Micro-services (vs Monolithic applications): decouple components for ease of scaling out & improve fault tolerance
- `EventBridge`: Push event to targets using rules on content
- `Simple Queue Service (SQS)` --> Point-to-Point pull-based queue with retention
- `Simple Notification Service (SNS)` --> Publish/Subscriber --> broadcast to subscribers

## 03. Compute Services
- Managed vs. un-managed services: varying degree of AWS vs. customer responsibility
  - Managed service: off the cloud + OS, network, firewall + Platform & application management
- Fully-managed services (e.g. serverless): customer only need "in-the-cloud" responsibility (i.e. application layer)

### Lambda
- Serverless compute service that runs code in response to events, without needs to provision or manage servers
- Charged for the compute time --> max to 15 minutes
- Customer main responsibility: lambda function, triggers & runtimes

### Containers
- Container: package application code & dependencies into portable unit --> easy to deploy & redeploy
- Related services:
  - Elastic Container Registry (ECR): store, manage & version container images
  - Elastic Container Service (ECS) vs Elastic Kubernetes Service (EKS): simple container management vs full Kubernetes cluster for complex management
  - EC2 vs Fargate: full control over infrastructure vs. serverless engine for container (i.e. infrastructure is fully managed)

### Additional services
- **Elastic Beanstalk**: fully managed service for deploy & manage web application: manage & scale, load balancing & application health monitor --> for web app, RESTful APIs, backend services & micro-services
- **AWS Batch**: fully managed service for batch computing: manage & scale resources, schedule batch job --> for large scale, parallel workloads such as scientific computing, financial risk analysis, big data processing, etc.
- **Lightsail**: virtual private servers (VPSs) --> for basic business, dev & test environment, learning cloud services
- **Outposts**: fully managed hybrid cloud --> for low-latency apps, legacy applications, meeting regulatory compliance or data residency requirements

## 04. Going Global
- Basic infrastructure:
  - **Availability Zone**: location with 1 or more data centers that are desgined to be isolated from failures in other areas
  - **Region**: where AWS operates multiple data center, grouped into Availability Zones
  - **Edge location**: smaller facilities that cache content --> lower latency
- Choose a region --> key considerations:
  1. Compliance requirement
  2. Proximity to user --> lower latency
  3. Feature availability
  4. Pricing
- `AWS CloudFormation`: Infrastructure-as-Code (IaC) service --> DevOps for CI/CD, scaling to multi-Region

## 05. Networking
- `Amazon Virtual Private Cloud (VPC)`: isolated section of the AWS Cloud --> a virtual network where you can launch AWS resources
- `Subnet`: to organize your AWS resources in your VPC --> can be made public/private
- `Internet Gateway`: allow access to public resources within VPC
- `Virtual Private Gateway`: AWS service for VPN connection
  - `Virtual Private Network (VPN)`: allow "secure" access to private resources within VPC from on-premises data center or internal corporate network

### Ways to connect to the AWS Cloud
- **AWS Client VPN**: fully-managed elastic VPN service --> connect remote workers & on-premisis networks to the cloud
- **AWS Site-to-Site VPN**: secure connection between branch office or on-premises data center with AWS Cloud (i.e. Network layer)
- **AWS PrivateLink**: connect VPC to specific services & resources as if they were within VPC
- **AWS Direct connect**: establish a dedicated private connection between your network and VPC in the AWS Cloud
  - Bypasses the internet for consistent, low-latency
  - Smooth & reliable data transfers, especially at massive scale
  - Best for hybrid cloud --> reliable performance (i.e. no network congestion)

### Security groups & Network Access Control Lists (ACLs)
- **Network ACLs**: stateless, control inbound/out-bound packets to subnet via sender address & ports
- **Security groups**: fine-grained control for individual/group EC2 instances --> allow rules only, stateful (remember states, return traffic is automatically allowed)

### Building an AWS Virtual Private Cloud
- Create the AWS VPC within required region
- Create the subnets (public/private for each Availability Zone)
- Create an internet gateway
  - Create route table for the gateway --> default with route entry for traffic within VPC
  - Add route entry "to the internet" from within VPC
  - Add association to "public" subnets
- Setup network ACLs & security groups

### Other networking services
- `AWS Route 53`: cloud-based DNS service, with advance routing policies (i.e. Latency-based, geolocation/geoproximity, failover, weighted routing)
- `CloudFront`: Content-delivery network (CDN) service that cache contents closer to users
- `AWS Global Accelerator`: use intelligent routing & fast failover based on AWS global network to improve network traffic

## 06. Storage

## 07. Database

## 08. AI ML and Data Analytics

## 09. Security

## 10. Monitoring, Compliance & Governance in the AWS Cloud

## 11. Pricing & Support

## 12. Migrating to the AWS Cloud

## 14. Well-Architected Solutions