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

## Cloud benefits