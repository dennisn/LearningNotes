# AZ-900: Foundational Cloud Concepts

## Cloud computing
  - Definition:
    + Multiple vendors
    + On demand delivery of computing services (e.g. servers, storage, db, etc.), over the internet
    + Flexibility: scale up/down quickly, high availability and ranges of services to choose from
    + Lower cost by increase efficiency --> pay for what you need
  - Shared responsibility model: important to determine correct service for client, while prevent security gaps
    + Vendor & client split responsibility --> different way of split, but normally client handle the data for privacy reason, while vendor handle from the physical level up
    + On-prem: client handle everything
    + IaaS: vendor handle physical hosts, network & data center
    + PaaS: vendor handle OS, but share network, application, Identity & Directory infrastructure with client
    + SaaS: vendor share Identity & Directory infrastructure, while client only handle information & data, accounts & identities and accessing devices
  - Cloud models: private, public & hybrid
    + Private: single customer, full control & privacy, but high upfront cost & responsibility --> less flexibility
    + Public: publicly available, no upfront cost, but shared tenancy with other users, and can't control the physical server
    + Hybrid: mixed, with private connectivity with VPN, ExpressRoute --> more complex management
      - May also include connect multiple public clouds
      - Azure Arc: manage Azure + non-Azure resources together in Azure service
  - Economy of cloud computing
    + Capital expenditure (CapEx): upfront costs for physical infrastructure (e.g. servers, data centers) --> forecast risk, but tax deduction
    + Operation expenditure (OpEx): rent services, pay as you go --> no forecast risk
    + Cloud model = Consumption-based (e.g. OpEx)

## Cloud services types
  - Cloud Service Types: 3 main types: more control = more customer responsibiltiy
  - IaaS: Basic computing infrastructure, pay for what you allocate --> more management overhead
    + Often through Virtual Machines (VMs) = Virtual computer: CPU/Memory, Disk, Networking, Network Controls (Firewall), maybe event OS --> often hosted on physical server (hypervisor)
  - PaaS: Pre-packaged cloud services, Pay for what you use  --> less management overhead
    + Vendor managed Development/Deployment services: development tools, database/storage and analytics --> user focus on developing solutions
      - OS, storage, software licenses, auto-scaling
    + Serverless: extreme form of PaaS, where all resource is managed for you --> user only focus on run code
    + Trade off: pre-packaged configuration, limited choice of tools --> application compatibility constraint
  - SaaS: Cloud-based applications, pay for what you subscribe --> minimal management overhead
    + Client responsibility: accounts & identities (authentication), device management, information & data on the service
    + Main points: For specific use, subscription model, but can access anywhere, with hosting & scaling provided
  - Defense in Depth: to stop/slow unauthorized data access --> using layered defense (e.g. similar to a castle)
    + Physical security: first line of defense --> often by vendor
    + Identity & access: user management
    + Perimeter: firewalls/DDoS protection
    + Network: secure (by limit) connectivity between resources
    + Compute: secure virtual machines, endpoint, OS patching
    + Application: design/fix security holes, secure secrets/password
    + Data: primary target --> control access to db, disk, SaaS application

## Cloud benefits
  - High Avail. & Scalability: can "instantly" acquire new server(s)
    + High Avail.: is ability to remain available even if some sort of interruption or disruption takes place
    + Scal-ability: increase/reduce resources to meet the demands of your customers
      - Horizontal scaling (scale out with load balancer): can be automated with no downtime
      - Vertical (scale up): increase resources on existing server/VM --> often manual, requies downtime/reboot
  - Reliability & Predictability
    + Reliability: ability to recover from failure/disasters and continue to function
      - No single point of failure with decentralized design
      - Deploy to multiple locations --> protect against regional failure
    + Predict-ability in performance: consistent performance regardless of demand/location --> to have enough resources when needed
      - Required deployment following best practices (e.g. Well-Architected Framework) --> auto-scaling, global deployments
    + Predict-ability in cost: accurately trace & forecast costs
      - Tools to manage current & future costs --> tracking & forecast costs from current usage, analyse to optimize usage/costs
  - Security: provide solutions to meet your security needs
    + Different lovel of security control to choose
    + Network controls: DDoS protection, firewalls
  - Governance: standards & compliance enforcement --> help with tools provided by vendors ==> easier to meet corporate standards and/or compliance requirements
    + Cloud deployment require minimum standard: encryption of data, location restriction of resources deployment
    + Tools: standard templates, auditing tools, automated patching, etc.
  - Manage-ability: of the cloud vs. in the cloud (i.e. control vs. interact)
    + Manage. "**of**"" the cloud: tools to **control** cloud resources --> auto-scaling, monitoring, template-based deployment
    + Manage. "**in**"" the cloud: tools to **inter-act** with cloud resources --> variety of interaction tools