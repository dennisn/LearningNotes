# Microservices

## The big picture
  - Service: an independently deployable component (e.g. software that can be installed by itself)
    + support interoperability via message-based communication: can be invoked via message, even by software of different language
  - Microservice: small, autonomous services that work together
    + a service perform a specific business function
    + can be big/small in code terms, depending on complexity of the function
    + Consequence: smaller, independent teams vs. one big team for the whole applications
  - Monilith: Simple, but cumbersome, especially when it grow large

## Elements
  - Split into sub-domains
  - Each handled by its own team, sized to its complexities
    + can have its own version
  - Data stores are separated
    + no longer immediate consistency, but eventual consistency: change in one sub-system may take time to propagate to others
  - User interface: use UI composition to give the feel of one system
    + each with its own set of components
    + can be done from server or client
  - Inter-service communications: different styles
    + Remote method invocation: Synchronous or Asynchronous
    + Message or event via a broker/channel/bus --> async, and may buffer to improve spike in load
  - API & contract: the point of communication/invokation of services
    + include data structure, routines & protocol
    + May be different per device types, to best fit their usage/capability
  - Service registry: allow service to know where other services are (e.g. Zookeeper, Eureka, Consul, etc.)
    + service starts by self-registered
  - Cross-origin Resource sharing (i.e. "cors"): normal Http don't allow script to talk to other server for security reason
    + Services would often be on different server --> need specific headers to do service call on different servers
  - Circuit Breaker: temporarily blocking requests to a failing service --> allow it to recover without being overwhelmed.
    + to avoid the domino effect when failures in services downstream causing failure in services upstream
    + Operate in 3 stats: 
      - Closed: all requests flow through
      - Open: all requests are rejected --> trigger when the no of failures exceeds a configured threshold
      - Half-open: after a timout period --> allows a limited number of requests to pass through to see if the service has recovered
    + Some service breakers: Hystrix, JRugged
  - Gateway: sit between the services and the UI
    + single entry point --> can do authentication, service look up, etc.
    + API translation: translate data to the appropriate format for each device type
    + Example: Zuul, Netty, Finagle

### Security
  - Security: authenticate (i.e. who is the caller) & authorization (i.e. is the caller allowed to call specific service)
    + Can be delegated to Identity & Access Management (IAM) system at service gateway
    + Single Sign-on: more convenient
    + Example: kerberos, OpenId, OAuth 2.0, etc.
    + Some well-known IAM: Okta, Keycloak, Shiro
  - Access Token: stores information about user, to exchange between services
    + Each service verify the token before accept the claims within
    + Example: JSON Web Token

### DevOps
  - Scalability
    + Vertical: Increase RAM & CPU (physical resources)
    + Horizontal: increase the no of instance + Load balancing (e.g. Ribbon, Meraki)
  - Availability: 
    + Single Point of Failure (SPOF): critical junction where failure will stop the system working
    + Example: Gateway, Broker, Registry, IAM
    + Horizontal scaling (e.g. increase the # of instances) --> reduce risk
  - Monitoring & Dashboard: centralised visual health, resources status of all subsystems
    + Example: Kibana, Grafana, Splunk
    + Health Check API: a HTTP endpoint for the health status of the service
  - Log aggregator: aggregate logs from each services to understand the overal behaviors
    + Example: LogStash, Splunk, PaperTrail
  - Exception Tracking: would be difficult to find in general logs --> should be collected in centralized exception tracking system
  - Performance metrics: measure service performance, which can be aggregate into centralized service for report & alert
    + Example: DropWizard, Actuator, Prometheus
  - Auditing: record usage behaviors --> optimised/scale subsystem over-utilised
  - Rate limiting: defend against DoS attacks
    + Also can be used to monetize our APIs: each rate range have different price 
  - Alerting: let admin. know when certain pre-configured conditions are met --> early warning of problems
  - Distributed tracing: requests span multiple services --> harder to identified & linked
    + Given a unique code for request, which is passed betwen service
    + Example: Dapper, HTrace, Zipkin

### Deployment
  - Containers: package each service & its dependencies --> ease of use from dev to test then prod
    + Also ease to scale up & down
    + Example: Docker, Rocket
  - Orchestrator: help with the management of multiple containers that depends/connect with each other
    + Example: Kubernetes, Mesos, Docker Swarm, Marathon
  - Continuous Delivery: ensure reliable delivery by automate the Build->Test->Deploy process directly from version control
    + Example: Jenkins, Asgard, Aminator
  - Environment: Dev, Test, QA, Staging & Prod
    + Each environment need different configuration --> externalize the configuration into external storage (e.g. Database, Archaius, Consul, Decider)

## Pros & Cons
  -
