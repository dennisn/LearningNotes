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

## 03. Computer Services

## 04. Going Global

## 05. Networking

## 06. Storage

## 07. Database

## 08. AI ML and Data Analytics

## 09. Security

## 10. Monitoring, Compliance & Governance in the AWS Cloud

## 11. Pricing & Support

## 12. Migrating to the AWS Cloud

## 14. Well-Architected Solutions