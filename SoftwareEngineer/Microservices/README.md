# Microservices

## The Big Picture

- Service: an independently deployable component (software that can be installed and run by itself).
  - Supports interoperability via message-based communication.
  - Can be invoked by software written in different languages.
- Microservices: small, autonomous services that work together.
  - Each service performs a specific business function.
  - Service size varies by business complexity, not line count.
  - Organizational impact: smaller independent teams instead of one large team for a monolith.
- Monolith:
  - Simpler to start.
  - Can become cumbersome as the system grows.

## Core Elements

- Split system into subdomains.
- Assign each subdomain to its own team based on complexity.
  - Each service can have its own versioning.
- Separate data stores per service.
  - Leads to eventual consistency instead of immediate consistency.
- User interface composition:
  - Present one unified system to users.
  - Built from components owned by multiple services.
  - Can be server-side or client-side composition.
- Inter-service communication styles:
  - Remote method invocation (synchronous or asynchronous).
  - Messaging/events via broker, channel, or bus (asynchronous, can buffer traffic spikes).
- API and contracts:
  - Define communication and invocation boundaries.
  - Include protocol, routines, and data structures.
  - Can vary by device/client type.
- Service registry:
  - Helps services discover each other (for example: Zookeeper, Eureka, Consul).
  - Services self-register at startup.
- CORS (Cross-Origin Resource Sharing):
  - Browsers restrict cross-origin scripts by default.
  - Microservices often run on different hosts, so proper CORS headers are required.
- Circuit breaker:
  - Temporarily blocks requests to failing downstream services.
  - Prevents cascading failures in upstream services.
  - States:
    - Closed: all requests pass through.
    - Open: requests are rejected after failure threshold is exceeded.
    - Half-open: after timeout, limited requests are allowed to test recovery.
  - Examples: Hystrix, JRugged.
- Gateway:
  - Sits between UI/clients and backend services.
  - Single entry point for auth, routing, service lookup, and policy.
  - Can perform API translation for different device/client formats.
  - Examples: Zuul, Netty, Finagle.

## Security

- Security has two concerns:
  - Authentication: who the caller is.
  - Authorization: what the caller is allowed to do.
- Often delegated to IAM systems at the gateway layer.
- Single Sign-On improves usability.
- Protocol examples: Kerberos, OpenID, OAuth 2.0.
- IAM product examples: Okta, Keycloak, Shiro.
- Access tokens carry user claims between services.
  - Each service validates token claims before accepting requests.
  - Example token format: JWT (JSON Web Token).

## DevOps and Operations

- Scalability:
  - Vertical scaling: increase CPU and RAM.
  - Horizontal scaling: add instances and use load balancing (for example: Ribbon, Meraki).
- Availability:
  - SPOF (Single Point of Failure): critical component whose failure breaks the system.
  - Typical hotspots: gateway, broker, registry, IAM.
  - Horizontal scaling reduces risk.
- Monitoring and dashboards:
  - Centralized health and resource visibility across services.
  - Examples: Kibana, Grafana, Splunk.
  - Health-check endpoint: HTTP endpoint exposing service health.
- Log aggregation:
  - Collect logs across services to understand overall behavior.
  - Examples: Logstash, Splunk, PaperTrail.
- Exception tracking:
  - Centralize exceptions for faster incident investigation.
- Performance metrics:
  - Measure and aggregate service metrics for reporting and alerting.
  - Examples: Dropwizard, Actuator, Prometheus.
- Auditing:
  - Track usage behavior to optimize and scale overloaded subsystems.
- Rate limiting:
  - Protect against DoS attacks.
  - Also supports API monetization tiers.
- Alerting:
  - Notify operators when configured thresholds/conditions are met.
- Distributed tracing:
  - Requests span multiple services, so end-to-end tracing is required.
  - Propagate a unique request ID across service boundaries.
  - Examples: Dapper, HTrace, Zipkin.

## Deployment

- Containers:
  - Package each service and its dependencies consistently from dev to test to prod.
  - Simplify scale up/down.
  - Examples: Docker, Rocket.
- Orchestration:
  - Manage many connected containers and their lifecycle.
  - Examples: Kubernetes, Mesos, Docker Swarm, Marathon.
- Continuous Delivery:
  - Automate Build -> Test -> Deploy directly from version control.
  - Examples: Jenkins, Asgard, Aminator.
- Environments:
  - Dev, Test, QA/Integration, Staging, Production.
  - Keep environment-specific config externalized (for example: database, Archaius, Consul, Decider).

## Pros and Cons

- In competitive markets, time-to-market is critical.
  - Microservices can improve delivery speed by dividing work into smaller batches.
  - Best fit when priorities are time-to-market, extensibility, replaceability, and scalability.

### 1. Business

- Pros: lower initial cost, open-source ecosystem, faster time-to-market.
- Cons: no single support vendor, no universal standard.

### 2. Technical

- Pros: flexible stack choices, better maintainability/extensibility, targeted performance optimization.
- Cons: hard integration testing, difficult distributed system design, data synchronization complexity.

### 3. Production

- Pros: portability, scalability, availability, infrastructure flexibility.
- Cons: CI/CD and monitoring complexity, harder environment setup.

## Business Concerns

- Organization:
  - Small teams are effective, but inter-team communication must be managed.
- Recruiting:
  - Easier due to market interest in microservices.
- Training:
  - Teams can be flexible with stacks and skill mix.
  - Risk: deep stack specialization can reduce cross-team mobility.
- Standards:
  - No single microservices standard; support model can be fragmented.
- Open source:
  - Strong ecosystem and tooling availability.
- Cost:
  - Can start small/cheap, then scale with demand.
- Time-to-market:
  - Smaller deliverables can accelerate feedback and revenue.

## Technical Concerns

- Service design/splitting:
  - Often difficult and context-specific.
  - Domain-Driven Design helps define boundaries.
- Technology choice:
  - Flexibility is a strength (right tool for the right job).
- User interface:
  - Individual service UIs can stay simple.
  - Aggregating into one coherent UX can be tricky.
- Distributed systems complexity:
  - Network failures are inevitable.
  - Patterns like circuit breaker are essential.
- Data stores:
  - Per-service stores improve independence.
  - Cross-service consistency is harder.
  - Avoid distributed transactions when possible.
- Performance:
  - Per-service optimization is possible.
  - Network/integration overhead can reduce overall throughput.
- Security:
  - More services mean more attack surface and failure points.
- Testing:
  - Unit/service tests are easier in isolation.
  - Integration test environments are complex.
  - Chaos testing helps validate resilience.
- Maintainability:
  - Smaller codebases are easier to understand and maintain.
- Extensibility:
  - New capability can be added via new services and contracts.

## Production Concerns

- DURS (Deploy, Update, Replace, Scale): independent per service.
- CI/CD:
  - Critical for early error detection.
  - Harder as subsystem count increases.
- Portability:
  - Containerization supports consistent CI/CD and runtime portability.
- Infrastructure:
  - Often hybrid across traditional IT, private cloud, and public cloud.
- Scalability:
  - Usually easier than monoliths when designed correctly.
- Availability:
  - Designed for resilience, but still requires graceful degradation patterns.
- Monitoring:
  - Mandatory due to subsystem count and inevitable failures.
