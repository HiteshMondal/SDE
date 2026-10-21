# System Design Guide

A concise reference covering the core concepts used when designing large-scale, distributed systems.

---

# System Design Fundamentals

Before choosing technologies, clarify what the system must do and what constraints it must satisfy.

## Functional Requirements

Describe what users should be able to do.

Examples:
- Create an account
- Upload a file
- Send a message
- Search content
- Make a payment

## Non-Functional Requirements

Describe how the system should behave.

Common requirements:
- Scalability
- Availability
- Reliability
- Durability
- Consistency
- Latency
- Throughput
- Security
- Maintainability
- Cost efficiency

## Capacity Estimation

Estimate the expected system load before designing the architecture.

Important numbers:
- Daily Active Users (DAU)
- Monthly Active Users (MAU)
- Requests per second (RPS)
- Queries per second (QPS)
- Read/write ratio
- Average request size
- Storage per object
- Data retention period
- Peak traffic multiplier

### Basic Calculations

Average RPS:

Daily requests / 86,400

Peak RPS:

Average RPS × Peak Traffic Factor

Storage growth:

Objects per day × Average object size × Number of days

Bandwidth:

Requests per second × Average response size

## Latency

Time required to complete a request.

Consider:
- Network latency
- Load balancer latency
- Application processing time
- Cache latency
- Database latency
- External API latency

## Throughput

Amount of work a system can process per unit of time.

Examples:
- Requests/second
- Messages/second
- Transactions/second
- MB/second

## Availability

Percentage of time a system is operational.

Availability targets are commonly expressed using "nines":

- 99%
- 99.9%
- 99.99%
- 99.999%

Higher availability generally requires additional redundancy and cost.

## Reliability

Probability that a system performs correctly over a period of time.

Availability and reliability are related but not identical.

## Durability

Probability that stored data will not be lost.

For example, replicated object storage provides high durability.

## SLA, SLO, and SLI

### SLI
A measured indicator of system behavior.

Examples:
- Request latency
- Error rate
- Availability

### SLO
A target for an SLI.

Example:
- 99.9% of requests complete successfully
- p99 latency < 200 ms

### SLA
A formal agreement with users/customers defining service guarantees and consequences when they are not met.

---

# Scalability

The ability of a system to handle growing amounts of load by adding resources.

## Vertical Scaling
Adding more power (CPU, RAM, disk) to an existing machine.

- Simple to implement
- Has a hard upper limit
- Single point of failure remains

## Horizontal Scaling
Adding more machines to share the load.

- Nearly unlimited scaling potential
- Requires load balancing and data distribution
- Increases system complexity

---

# Load Balancing

Distributes incoming traffic across multiple servers to avoid overload on any single node.

### Common Algorithms
- Round Robin
- Least Connections
- IP Hash
- Weighted Round Robin

### Types of Load Balancers
- Layer 4 (Transport layer — routes based on IP/port)
- Layer 7 (Application layer — routes based on content, headers, URL)

---

# Caching

Storing frequently accessed data closer to the user or application to reduce latency and database load.

### Caching Strategies
- **Cache-Aside** — application checks cache first, loads from DB on miss
- **Write-Through** — writes go to cache and DB simultaneously
- **Write-Back** — writes go to cache first, DB updated asynchronously
- **Read-Through** — cache itself fetches data from DB on a miss

### Cache Eviction Policies
- LRU (Least Recently Used)
- LFU (Least Frequently Used)
- FIFO (First In First Out)

### Where to Cache
- Client-side (browser)
- CDN
- Application server (in-memory)
- Distributed cache (Redis, Memcached)

### Cache Invalidation

Keeping cached data synchronized with the source of truth is difficult.

Common approaches:
- TTL-based expiration
- Explicit invalidation
- Write-through updates
- Event-driven invalidation

### Cache Stampede

Occurs when many requests simultaneously miss an expired cache entry and overload the database.

Mitigations:
- Request coalescing
- Locking
- Early refresh
- Randomized TTL
- Stale-while-revalidate

### Cache Penetration

Requests repeatedly query data that does not exist.

Mitigations:
- Negative caching
- Bloom filters
- Input validation

### Cache Avalanche

Large numbers of cache entries expire simultaneously and create a sudden database load spike.

Mitigations:
- Randomized TTL
- Staggered expiration
- Cache warming

### Cache Consistency

Cached data can become stale.

Choose whether the application can tolerate:
- Strong consistency
- Eventual consistency
- Stale reads

---

# Search Systems

Search engines are optimized for text search and complex filtering.

## Inverted Index

Maps terms to the documents containing those terms.

Example:

"distributed" → Document 1, Document 5, Document 8

## Full-Text Search

Supports:
- Keyword search
- Tokenization
- Ranking
- Fuzzy matching
- Filtering

## Search Architecture

Primary Database
        ↓
Change/Data Pipeline
        ↓
Search Index
        ↓
Search API

The database remains the source of truth while the search index is optimized for queries.

## Search Index Trade-offs

Benefits:
- Fast search
- Complex filtering
- Relevance ranking

Costs:
- Additional infrastructure
- Indexing delay
- Eventual consistency
- Storage overhead

---

# Content Delivery Network (CDN)

A geographically distributed network of servers that caches static content (images, videos, JS/CSS) closer to users.

- Reduces latency
- Reduces load on origin servers
- Push CDN vs Pull CDN

---

# Object Storage

Used for large unstructured data such as:

- Images
- Videos
- Documents
- Backups
- Logs

Examples:
- Amazon S3
- Google Cloud Storage
- Azure Blob Storage

## Object Storage vs Database

Use object storage for large blobs/files.

Use databases for structured metadata and queryable records.

Typical architecture:

Client → Object Storage
        ↓
      Metadata DB

## Pre-Signed URLs

Allow clients to upload/download objects directly from object storage without routing large files through application servers.

Benefits:
- Reduces application-server bandwidth
- Improves scalability
- Supports large file uploads

## Lifecycle Management

Objects can automatically move between storage classes or be deleted based on age.

## Object Versioning

Keeps multiple versions of an object and helps recover from accidental deletion or overwrites.

---

# Proxies

## Forward Proxy
Sits between client and internet; hides client identity, used for filtering/access control.

## Reverse Proxy
Sits between clients and backend servers; used for load balancing, SSL termination, and caching.

---

# DNS

Domain Name System translates domain names into IP addresses.

Example:

example.com → IP address

## DNS Records

Common records:
- A — IPv4 address
- AAAA — IPv6 address
- CNAME — alias
- MX — mail server
- TXT — text/verification information

## DNS Load Balancing

DNS can return different IP addresses to distribute traffic.

## DNS Failover

DNS can direct users away from unhealthy infrastructure.

## DNS TTL

Time To Live determines how long DNS responses may be cached.

Lower TTL:
- Faster changes
- More DNS queries

Higher TTL:
- Better caching
- Slower propagation of changes

---

# Databases

## SQL Databases
Relational, structured schema, ACID compliant. Examples: PostgreSQL, MySQL.

- Best for structured data with relationships
- Strong consistency guarantees

## NoSQL Databases
Flexible schema, built for horizontal scale. Types:

- **Key-Value** (Redis, DynamoDB)
- **Document** (MongoDB)
- **Column-Family** (Cassandra)
- **Graph** (Neo4j)

### Indexing
Data structure (usually B-Tree or Hash) that speeds up read queries at the cost of slower writes and extra storage.

### Replication
Copying data across multiple nodes to improve availability and read throughput.

- Master-Slave Replication
- Master-Master Replication

### Sharding
Splitting a large database into smaller, faster, more manageable pieces called shards.

- Range-based sharding
- Hash-based sharding
- Directory-based sharding

---

# Database Design

Choosing a database is only part of database design. Data modeling and access patterns are equally important.

## Data Modeling

Start with the queries the system must support.

Ask:
- What data is stored?
- How is it queried?
- Which fields are frequently filtered?
- Which relationships exist?
- What is the read/write pattern?

## Normalization

Organizing relational data to reduce duplication and maintain consistency.

Common normal forms:
- 1NF
- 2NF
- 3NF

## Denormalization

Duplicating data intentionally to improve read performance.

Trade-offs:
- Faster reads
- More storage
- More complex writes
- Potentially stale duplicated data

## Transactions

A transaction groups multiple operations into a single logical unit.

### ACID

- **Atomicity** — all operations succeed or none do
- **Consistency** — data remains valid according to defined rules
- **Isolation** — concurrent transactions do not incorrectly interfere
- **Durability** — committed data survives failures

## Isolation Levels

Common levels:
- Read Uncommitted
- Read Committed
- Repeatable Read
- Serializable

Higher isolation generally provides stronger guarantees but can reduce concurrency.

## Database Constraints

Examples:
- Primary key
- Foreign key
- Unique constraint
- NOT NULL
- CHECK constraint

Constraints help protect data integrity.

## Composite Indexes

Indexes can contain multiple columns.

Column order matters and should match common query patterns.

## Query Optimization

Important techniques:
- Proper indexes
- Avoid unnecessary columns
- Avoid N+1 queries
- Analyze query execution plans
- Use pagination
- Cache expensive queries

## Read Replicas

Read replicas allow read traffic to be distributed across multiple database nodes.

Trade-off:
- Increased read capacity
- Possible replication lag

## Write Scaling

When a single database cannot handle write traffic:

- Partition/shard data
- Use batching
- Use asynchronous processing
- Optimize indexes
- Reduce unnecessary writes

## Hot Partitions / Hot Keys

A poor partition key can cause most traffic to reach one partition.

Examples:
- Celebrity user
- Popular product
- Current timestamp

Mitigations:
- Better partition keys
- Key salting
- Request distribution
- Caching

## Database Backup and Restore

Important concepts:
- Full backups
- Incremental backups
- Point-in-time recovery
- Backup retention
- Restore testing

A backup is useful only if it can actually be restored.

## Data Retention

Define:
- How long data is kept
- When it is archived
- When it is deleted
- Legal/compliance requirements

---

# Storage Systems

## Block Storage

Provides raw disk-like storage to machines.

Good for:
- Databases
- Virtual machine disks
- Low-level storage workloads

## File Storage

Provides shared files and directories.

Good for:
- Shared application files
- Network file systems

## Object Storage

Stores objects using keys and metadata.

Good for:
- Images
- Videos
- Backups
- Large files

## Local Storage

Data stored directly on a machine.

Very fast but usually tied to that machine and vulnerable to machine failure.

## Storage Trade-offs

Consider:
- Latency
- Throughput
- Durability
- Availability
- Cost
- Access pattern
- Object size
- Retention requirements

---

# CAP Theorem

A distributed system can only guarantee two of the following three at the same time:

- **Consistency** — every read receives the latest write
- **Availability** — every request receives a response
- **Partition Tolerance** — system continues to operate despite network failures

Since network partitions are unavoidable in distributed systems, the real trade-off is between Consistency and Availability.

---

# Consistency Patterns

- **Strong Consistency** — all reads reflect the latest write immediately
- **Eventual Consistency** — reads may return stale data temporarily, but converge eventually
- **Weak Consistency** — no guarantee when all nodes will be in sync

## Read-Your-Writes Consistency

A user should immediately see their own successful writes.

Useful for:
- Profile updates
- Posts
- Account settings

## Monotonic Reads

Once a user has seen a value, subsequent reads should not return an older value.

## Monotonic Writes

Writes from the same client should be applied in the order they were issued.

## Quorum Reads and Writes

A replicated system can require a certain number of replicas to acknowledge reads/writes.

Let:
- N = number of replicas
- W = number of write acknowledgements
- R = number of read acknowledgements

When:

R + W > N

read and write sets overlap, which can help provide stronger consistency guarantees depending on the system.

## Conflict Resolution

When multiple replicas accept writes independently, conflicts may occur.

Strategies:
- Last Write Wins
- Version vectors
- Application-level merging
- Conflict-free Replicated Data Types (CRDTs)

---

# Availability Patterns

## Failover
- **Active-Passive** — standby server takes over on failure
- **Active-Active** — multiple servers handle traffic simultaneously

## Replication for Availability
Ensures no single point of failure by keeping multiple copies of data/services running.

---

# Consistent Hashing

A technique used to distribute data evenly across nodes and minimize re-distribution when nodes are added or removed, commonly used in distributed caches and databases.

---

# Data Partitioning

Dividing data across multiple nodes to improve performance and scalability.

- Horizontal Partitioning (Sharding)
- Vertical Partitioning (splitting by columns/features)

---

# Message Queues

Enable asynchronous communication between services, improving decoupling and fault tolerance.

### Common Patterns
- **Point-to-Point** — one producer, one consumer
- **Publish-Subscribe** — one producer, multiple consumers

### Popular Tools
- Kafka
- RabbitMQ
- AWS SQS

### Message Delivery Guarantees

## At-Most-Once

A message is delivered zero or one time.

Possible message loss, but no duplicate processing.

## At-Least-Once

A message is delivered one or more times.

Messages can be duplicated, so consumers should be idempotent.

## Exactly-Once

The system attempts to process each logical message exactly once.

This is difficult to guarantee across distributed systems and often requires careful coordination.

## Message Ordering

Some applications require messages to be processed in order.

Ordering may be guaranteed:
- Per queue
- Per partition
- Per key

Global ordering is expensive at large scale.

## Dead Letter Queue (DLQ)

Stores messages that repeatedly fail processing.

Used for:
- Debugging
- Manual recovery
- Preventing poison messages from blocking processing

## Consumer Groups

Multiple consumers can work together to process messages.

Work is distributed so that each message is processed by one consumer within a consumer group.

## Retry Queue

Failed messages can be retried after a delay.

Use exponential backoff when appropriate.

## Poison Message

A message that repeatedly causes consumer processing to fail.

Move it to a DLQ after a retry threshold.

## Event-Driven Architecture

Services communicate by publishing events rather than directly calling each other.

Example:

Order Service
→ OrderCreated event
→ Payment Service
→ Inventory Service
→ Notification Service

Benefits:
- Loose coupling
- Asynchronous processing
- Independent scaling

Trade-offs:
- Eventual consistency
- More difficult debugging
- Duplicate events
- Ordering challenges

---

# API Gateway

A single entry point for client requests that routes them to appropriate backend services.

### Responsibilities
- Authentication and authorization
- Rate limiting
- Request routing
- Response aggregation

---

# API Design

APIs define how clients and services communicate.

## REST

Common HTTP methods:

- GET — read
- POST — create
- PUT — replace/update
- PATCH — partial update
- DELETE — delete

### Important HTTP Status Codes

- 200 — Success
- 201 — Created
- 204 — Success with no response body
- 400 — Bad Request
- 401 — Unauthenticated
- 403 — Forbidden
- 404 — Not Found
- 409 — Conflict
- 429 — Too Many Requests
- 500 — Internal Server Error
- 503 — Service Unavailable

## API Versioning

Examples:

- /api/v1/users
- /api/v2/users

Version APIs when breaking changes are introduced.

## Pagination

Used when returning large datasets.

Common approaches:
- Offset pagination
- Cursor-based pagination

Cursor pagination is generally better for large or frequently changing datasets.

## Filtering, Sorting, and Searching

APIs should support controlled parameters for:
- Filtering
- Sorting
- Searching
- Field selection

## Idempotent APIs

An operation is idempotent when repeating the same request produces the same intended result.

Important for:
- Payments
- Order creation
- Retryable requests
- Distributed systems

## API Timeouts

Every network request should have a timeout.

Never allow requests to wait indefinitely.

## Retries

Retries can recover from temporary failures.

Use:
- Exponential backoff
- Jitter
- Maximum retry limits

Avoid retrying non-retryable errors.

## Request and Response Contracts

Define:
- Request schema
- Response schema
- Validation rules
- Error format
- Versioning strategy

---

# Real-Time Communication

## Polling

Client repeatedly asks the server for updates.

Simple but can waste requests and increase latency.

## Long Polling

Server holds the request until new data is available or a timeout occurs.

## Server-Sent Events (SSE)

Server maintains a connection and pushes events to the client.

Good for:
- Notifications
- Live feeds
- Streaming updates

## WebSockets

Provides persistent bidirectional communication between client and server.

Good for:
- Chat
- Multiplayer games
- Collaborative applications
- Real-time dashboards

## WebSocket Scaling

When connections are distributed across multiple servers, messages may need a shared messaging layer.

Example:

Client A
→ WebSocket Server 1
→ Message Broker
→ WebSocket Server 2
→ Client B

## Presence

Tracks whether users are:
- Online
- Offline
- Away

Presence information is usually stored in a fast distributed store with expiration/heartbeat mechanisms.

---

# Microservices vs Monolithic Architecture

## Monolithic
Single unified codebase and deployment unit.

- Simple to develop and deploy initially
- Harder to scale specific components independently

## Microservices
Application broken into small, independently deployable services.

- Independent scaling and deployment
- Increased operational complexity
- Requires service discovery, API gateway, and inter-service communication

## Service Discovery

Allows services to find the network location of other services dynamically.

### Client-Side Discovery

The client queries a service registry and selects an available service instance.

### Server-Side Discovery

The client sends requests to a load balancer/router, which discovers and routes to healthy service instances.

Common components:
- Service registry
- Health checks
- Load balancing
- Service registration/deregistration

Examples:
- Kubernetes Service
- Consul
- Eureka

---

# Rate Limiting

Controls the number of requests a client can make in a given time window to protect systems from abuse or overload.

### Common Algorithms
- Token Bucket
- Leaky Bucket
- Fixed Window Counter
- Sliding Window Log

---

# Resilience and Fault Tolerance

Distributed systems fail. Design systems assuming individual components will fail.

## Timeout

Stop waiting for an unhealthy or slow dependency after a defined period.

## Retry

Retry temporary failures using:
- Exponential backoff
- Jitter
- Maximum retry count

## Circuit Breaker

Stops sending requests to a failing dependency temporarily.

States:
- Closed — requests flow normally
- Open — requests fail fast
- Half-Open — test whether the dependency recovered

## Bulkhead

Isolates resources so failure in one component does not consume all system resources.

Examples:
- Separate thread pools
- Separate connection pools
- Per-service resource limits

## Backpressure

Prevents producers from overwhelming consumers.

Techniques:
- Queue limits
- Rate limiting
- Consumer throttling
- Dropping low-priority work

## Load Shedding

Intentionally rejects or drops non-critical requests when the system is overloaded.

## Graceful Degradation

Continue providing core functionality while temporarily disabling non-essential features.

Example:

If recommendations fail, the application can still show the user's content.

## Fail Fast

Detect failures quickly instead of consuming resources waiting for a dependency that is unlikely to recover.

## Cascading Failure

A failure in one service causes increasing load or failures in dependent services.

Prevent with:
- Timeouts
- Circuit breakers
- Bulkheads
- Rate limits
- Backpressure
- Load shedding

---

# Concurrency and Race Conditions

Multiple requests may modify the same data simultaneously.

## Race Condition

Occurs when the result depends on the timing/order of concurrent operations.

Example:

Two users attempt to purchase the last available item simultaneously.

## Optimistic Locking

Assume conflicts are rare.

Store a version number and reject an update if the version has changed.

## Pessimistic Locking

Lock a resource while it is being modified.

Useful when conflicts are frequent or correctness requires serialization.

## Atomic Operations

An operation that completes as one indivisible action.

Useful for:
- Counters
- Inventory
- Locks
- State transitions

## Race Condition Prevention

Techniques:
- Database transactions
- Atomic operations
- Locks
- Optimistic concurrency
- Idempotency
- Unique constraints

---

# Distributed System Concepts

### Leader Election
A process by which nodes in a cluster elect a single node to coordinate tasks (e.g., Raft, Paxos, ZooKeeper).

### Consensus Algorithms
Ensure multiple nodes agree on a single data value, even in the presence of failures.

- Paxos
- Raft

### Heartbeats
Periodic signals sent between nodes to detect failures.

### Idempotency
Ensures that performing the same operation multiple times produces the same result, important for retries in distributed systems.

---

# Distributed Transactions

Transactions spanning multiple services or databases are difficult because there is no single local transaction boundary.

## Two-Phase Commit (2PC)

Coordinates multiple participants through two phases:

1. Prepare
2. Commit

Provides strong coordination but can be slow and introduces availability/coordination problems.

## Saga Pattern

Breaks a distributed transaction into a sequence of local transactions.

If a later operation fails, compensating actions undo the effects of previous operations.

Example:

Create Order
→ Reserve Inventory
→ Charge Payment
→ Confirm Order

If payment fails:

Release Inventory
→ Cancel Order

### Choreography

Services react to events without a central coordinator.

### Orchestration

A central orchestrator tells each service what operation to perform.

## Outbox Pattern

Writes a database change and an event record in the same local transaction.

A separate process publishes the event to the message broker.

Helps avoid:

Database updated successfully
but
Event publishing failed

## Distributed Lock

Used when multiple processes must coordinate access to shared resources.

Distributed locks require careful handling of:
- Lock expiration
- Ownership
- Failure
- Clock issues
- Split-brain scenarios

---

# Security Basics

- Authentication vs Authorization
- HTTPS/TLS for data in transit
- Encryption at rest for sensitive data
- Input validation and rate limiting to prevent abuse

## Security Architecture

### Authentication

Verifies who the user is.

Common mechanisms:
- Session-based authentication
- JWT
- OAuth 2.0
- OpenID Connect

### Authorization

Determines what an authenticated user is allowed to do.

Common models:
- RBAC — Role-Based Access Control
- ABAC — Attribute-Based Access Control

### Secrets Management

Never hard-code:
- Passwords
- API keys
- Database credentials
- Encryption keys

Use a dedicated secrets-management system.

### Key Rotation

Credentials and encryption keys should be rotated periodically and compromised keys should be revocable.

### Principle of Least Privilege

Give users and services only the permissions they actually need.

### DDoS Protection

Use layers such as:
- CDN
- WAF
- Rate limiting
- Traffic filtering
- Autoscaling

### WAF

Web Application Firewall filters malicious HTTP traffic.

Can help protect against common web attacks.

### Encryption

#### In Transit
Use TLS/HTTPS.

#### At Rest
Encrypt stored data and backups.

### Audit Logging

Record security-sensitive actions such as:
- Login attempts
- Permission changes
- Administrative actions
- Financial operations

---

# Monitoring and Observability

### Three Pillars
- **Logging** — recording discrete events
- **Metrics** — numerical measurements over time (CPU, latency, error rate)
- **Tracing** — tracking a request's journey across services

### Common Tools
- Prometheus + Grafana (metrics)
- ELK Stack (logging)
- Jaeger/Zipkin (tracing)

## Golden Signals

Monitor:

- Latency
- Traffic
- Errors
- Saturation

## RED Method

For request-driven services:

- Rate
- Errors
- Duration

## USE Method

For infrastructure resources:

- Utilization
- Saturation
- Errors

## Alerting

Alerts should be based on user-impacting symptoms whenever possible.

Examples:
- High error rate
- High p99 latency
- Queue backlog
- Low availability

Avoid excessive alerts that create alert fatigue.

## Distributed Tracing

A trace follows a request across multiple services.

Example:

Client
→ API Gateway
→ User Service
→ Database
→ Recommendation Service
→ Cache

Trace IDs allow the complete request path to be correlated.

## Correlation ID

A unique identifier propagated through services to connect logs belonging to the same request.

---

# Deployment and Infrastructure

## Containers

Package an application together with its dependencies.

Benefits:
- Consistent environments
- Portability
- Isolation
- Fast deployment

Docker is a common container technology.

## Container Orchestration

Manages containers across multiple machines.

Responsibilities:
- Scheduling
- Service discovery
- Health checks
- Scaling
- Rolling deployments
- Self-healing

Kubernetes is a common orchestration platform.

## Autoscaling

Automatically changes infrastructure capacity based on demand.

Scale based on:
- CPU
- Memory
- Request rate
- Queue depth
- Custom metrics

### Horizontal Autoscaling

Add/remove instances.

### Vertical Autoscaling

Increase/decrease resources of existing instances.

## Health Checks

### Liveness Check

Determines whether an application should be restarted.

### Readiness Check

Determines whether an application is ready to receive traffic.

## Deployment Strategies

### Rolling Deployment

Gradually replace old instances with new ones.

### Blue-Green Deployment

Run old and new versions simultaneously and switch traffic between them.

### Canary Deployment

Send a small percentage of traffic to the new version before increasing rollout.

### Feature Flags

Enable or disable functionality without redeploying the application.

## Infrastructure as Code

Define infrastructure using configuration/code.

Benefits:
- Reproducibility
- Version control
- Automation
- Easier disaster recovery

## CI/CD

### Continuous Integration

Automatically build and test changes.

### Continuous Delivery/Deployment

Automatically deliver or deploy validated changes.

Typical pipeline:

Code
→ Build
→ Unit Tests
→ Integration Tests
→ Security Checks
→ Deploy
→ Monitor

---

# Disaster Recovery

Design for failures larger than a single server.

## RTO

Recovery Time Objective:

How quickly the system must be restored after a failure.

## RPO

Recovery Point Objective:

How much data loss is acceptable, measured in time.

Example:

RPO = 5 minutes

means the business may tolerate losing up to approximately five minutes of recent data.

## Backup Strategies

- Full backup
- Incremental backup
- Differential backup
- Point-in-time recovery

## Disaster Recovery Strategies

### Backup and Restore

Restore infrastructure from backups after a disaster.

Lowest cost but slower recovery.

### Pilot Light

Keep a minimal version of critical infrastructure running.

### Warm Standby

Maintain a scaled-down secondary environment.

### Active-Passive

Primary handles traffic; secondary waits for failover.

### Active-Active

Multiple regions/environments serve traffic simultaneously.

## Disaster Recovery Testing

Regularly test:
- Backup restoration
- Database recovery
- Region failover
- Dependency failure
- Data recovery

Untested disaster recovery plans should not be considered reliable.

---

# Production Application Build Guide: From Empty Computer to Live Product

An end-to-end blueprint for building, testing, containerizing, deploying, and operating a production-grade, client-ready application starting from a blank computer. The guide follows one **reference project** so every tool and code snippet has a concrete place. Replace the project details with the client's own.

> Boilerplate only. Snippets show structure and wiring, not complete business logic. Versions of tools change quickly; pin the versions the team actually tests and re-verify installation commands against the official documentation before use.

---

## Reference Project and Architecture

### Reference Project

**TaskFlow** — a multi-tenant task management web application sold to business clients.

| Item | Decision |
|---|---|
| Users | Teams inside client organizations |
| Core features | Sign up and log in, organizations, projects, tasks, comments, file attachments, email notifications |
| Clients | Browser (responsive web app) |
| Expected scale at launch | 5,000 users, 500 concurrent, growth to 100,000 users |
| Availability target | 99.9% monthly |
| Data sensitivity | Business data, personal data (names, emails) |
| Compliance | GDPR-style privacy, audit trail |

### Technology Stack Used in This Guide

| Layer | Choice | Common Alternatives |
|---|---|---|
| Frontend | React + TypeScript (Vite) | Next.js, Vue, Angular, Svelte |
| Backend API | Node.js + TypeScript (Fastify) | Python (FastAPI, Django), Java (Spring Boot), Go, C# (.NET), Ruby on Rails |
| Database | PostgreSQL | MySQL, SQL Server, MongoDB (when documents fit) |
| Cache / queue backend | Redis | Memcached, RabbitMQ, SQS |
| Object storage | S3 / Azure Blob | GCS, MinIO |
| Container | Docker | Podman |
| Orchestration | Kubernetes (EKS / AKS) | ECS Fargate, Azure Container Apps, App Service |
| Infrastructure as Code | Terraform | OpenTofu, Pulumi, AWS CDK, Bicep |
| CI/CD | GitHub Actions + Argo CD | GitLab CI, Jenkins, Azure DevOps, CircleCI |
| Observability | OpenTelemetry, Prometheus, Grafana, Loki | Datadog, New Relic, CloudWatch, Azure Monitor |
| Cloud | AWS (primary), Azure equivalents shown | GCP |

#### Language Selection Guidance

| Situation | Preferred Language | Reason |
|---|---|---|
| Web product with a small full-stack team | TypeScript (frontend and backend) | One language, shared types, large ecosystem |
| Data and ML heavy | Python | Libraries, notebooks, quick iteration |
| High-throughput services, infra tooling | Go | Small static binaries, concurrency |
| Enterprise integration, large codebases | Java / Kotlin or C# | Mature tooling, strong typing, vendor support |
| Mobile apps | Swift / Kotlin, or React Native / Flutter | Native or cross-platform |
| Systems, performance critical | Rust / C++ | Memory control |

Choose based on team skills, hiring market, ecosystem, and operational maturity. Avoid more than two backend languages in the first version.

### Target Architecture

```text
                        Users (Browser)
                             |
                    DNS (Route 53 / Azure DNS)
                             |
                CDN + WAF (CloudFront / Azure Front Door)
                   /                         \
        Static Frontend                    API traffic
     (S3 / Blob Storage)                       |
                                   Load Balancer (ALB / App Gateway)
                                               |
                              +----------------+----------------+
                              |       Kubernetes Cluster        |
                              |  api pods (stateless)  x N      |
                              |  worker pods (queue consumers)  |
                              +----------------+----------------+
                                    |          |          |
                             PostgreSQL     Redis     Object Storage
                           (managed, multi-AZ) (cache)  (attachments)
                                               |
                                       Queue (SQS / Service Bus)
                                               |
                                  Email provider (SES / SendGrid)

   Cross-cutting: Secrets Manager / Key Vault, IAM, CloudWatch / Azure Monitor,
                  Prometheus + Grafana, OpenTelemetry traces, Sentry, CI/CD, IaC
```

### Delivery Lifecycle

```text
Discover -> Plan -> Set up tools -> Version control -> Scaffold -> Build features
   -> Test -> Containerize -> Provision infrastructure (IaC) -> CI/CD -> Deploy staging
   -> UAT / load / security tests -> Deploy production -> Domain + TLS -> Monitor
   -> Operate, support, improve -> Hand over / maintain
```

---

## Requirements and Planning

### Requirement Gathering with the Client

| Activity | Output |
|---|---|
| Stakeholder interviews | Goals, users, constraints, success metrics |
| User story mapping | Backlog of stories grouped by epics |
| Scope definition | MVP scope, explicit out-of-scope list |
| Constraints discovery | Budget, deadline, compliance, existing systems, preferred cloud |
| Acceptance criteria | Testable statements per story |
| Contract artifacts | Statement of Work, assumptions, change-request process, SLA |

User story and acceptance criteria format:

```text
As a <role>, I want <capability>, so that <benefit>.

Acceptance criteria (Given / When / Then):
  Given I am a logged-in member of project P
  When I create a task with a title and due date
  Then the task appears in P's task list and an audit entry is recorded
```

### Requirements Document Skeleton

```markdown
# TaskFlow Requirements

## Goals and Success Metrics
## Users and Roles (Owner, Admin, Member, Viewer)
## Functional Requirements (by epic)
## Non-Functional Requirements
- Availability 99.9%, p95 API latency < 300 ms, RPO 15 min, RTO 1 hour
- Browser support, accessibility (WCAG 2.2 AA), localization
- Security: MFA support, encryption in transit and at rest, audit log
## Integrations (SSO, email, payment, webhooks)
## Data Retention and Privacy Rules
## Out of Scope
## Risks and Assumptions
```

### Capacity Estimation for the Reference Project

```text
Users: 100,000 registered, 20% daily active -> 20,000 DAU
Actions per DAU per day: 50 requests -> 1,000,000 requests/day
Average RPS: 1,000,000 / 86,400 ≈ 12 RPS
Peak factor 10x: ≈ 120 RPS
Read/write ratio: 90/10

Storage:
  Tasks: 20,000 DAU * 5 new tasks/day * 1 KB * 365 ≈ 36 GB/year (before indexes)
  Attachments: 20,000 * 0.2 files/day * 2 MB * 365 ≈ 2.9 TB/year (object storage)

Bandwidth: 120 RPS * 50 KB response ≈ 6 MB/s at peak
```

Conclusion: a small cluster, one primary PostgreSQL instance with a read replica, and Redis are enough. Avoid microservices, sharding, and multi-region until measurements demand them.

### Architecture Decision Records

Capture each significant decision in `docs/adr/`.

```markdown
# ADR-0003: Use PostgreSQL as the primary database

Status: Accepted    Date: 2026-10-21    Deciders: Tech Lead, CTO

## Context
Relational data with transactional consistency requirements.

## Decision
PostgreSQL on a managed service (RDS / Azure Database for PostgreSQL).

## Consequences
+ Mature ecosystem, ACID, JSONB when flexible fields are needed
- Horizontal write scaling requires partitioning later

## Alternatives Considered
MySQL, MongoDB, DynamoDB
```

### Project Management Setup

| Item | Recommendation |
|---|---|
| Work tracking | Jira, Linear, GitHub Projects, Azure Boards |
| Process | Scrum with two-week sprints, or Kanban for support-heavy teams |
| Backlog hierarchy | Initiative > Epic > Story > Task |
| Definition of Ready | Story has acceptance criteria, design, estimate, no unresolved dependency |
| Definition of Done | Code reviewed, tests pass, deployed to staging, docs updated, no critical bugs, acceptance verified |
| Documentation | Confluence, Notion, or Markdown in the repository |
| Communication | Team chat, daily standup, sprint demo with client, weekly status report |

### Cost and Budget Planning

| Cost Area | Notes |
|---|---|
| Cloud infrastructure | Compute, database, storage, data transfer, load balancer, NAT gateway, logging |
| SaaS tooling | Source hosting, CI minutes, monitoring, error tracking, email provider |
| People | Developers, DevOps/SRE, QA, design, PM |
| Licenses | IDEs, databases, security scanners |
| Support and maintenance | Post-launch retainer, on-call |

Create cloud budget alerts before building anything (see cloud account setup).

### Legal and Compliance Checklist

- Privacy policy, terms of service, cookie consent
- Data processing agreement and data residency requirements
- Open-source license review for every dependency (avoid unintended copyleft obligations)
- Payment data: use a hosted provider (Stripe, Adyen) to stay out of PCI scope
- Health or financial data: determine HIPAA, SOC 2, ISO 27001, or regional requirements early
- Accessibility obligations for public-facing products

---

## Development Machine Setup

### Operating System Choice

| OS | Approach |
|---|---|
| Linux (Ubuntu LTS) | Closest to production; native Docker and tooling |
| macOS | Strong developer experience; install Homebrew |
| Windows | Use **WSL2** with Ubuntu for backend and DevOps work (see the Windows documentation for WSL details) |

Keep the project inside the Linux filesystem on WSL2 (`~/projects`), not under `/mnt/c`, for performance.

### Base Tools on Ubuntu or WSL2

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential curl wget git unzip jq ca-certificates gnupg make

# Git identity
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global pull.rebase true

# Node.js via nvm (version manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
source ~/.bashrc
nvm install --lts
node -v && npm -v
corepack enable            # enables pnpm / yarn shims
```

> Review any `curl | bash` installer before running it, and prefer package-manager or signed-release installs for production-grade machines.

### Docker, Kubernetes and Cloud CLIs

```bash
# Docker Engine (Ubuntu): follow https://docs.docker.com/engine/install/ubuntu/
sudo usermod -aG docker $USER && newgrp docker
docker run --rm hello-world

# Kubernetes tools
# kubectl, helm, kind, k9s: use official install docs or package managers
kubectl version --client
helm version
kind version

# Infrastructure as Code
# Terraform (or OpenTofu): install from the official repository
terraform -version

# Cloud CLIs
# AWS CLI v2: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
aws --version
# Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version
```

### Windows Setup Summary

```cmd
wsl --install -d Ubuntu-24.04
winget install --id Git.Git -e
winget install --id Microsoft.VisualStudioCode -e
winget install --id Docker.DockerDesktop -e
winget install --id Microsoft.WindowsTerminal -e
```

Then follow the Ubuntu steps inside WSL2.

### Editor and Code Quality Tooling

| Tool | Purpose |
|---|---|
| VS Code + extensions (ESLint, Prettier, Docker, Kubernetes, Terraform, GitLens, REST Client, Remote - WSL / Dev Containers) | Editor |
| ESLint | Static analysis |
| Prettier | Formatting |
| EditorConfig | Consistent whitespace across editors |
| TypeScript `strict` mode | Type safety |
| Pre-commit hooks | Enforce checks before commit |
| Dev Containers | Identical dev environment for every team member |

```ini
# .editorconfig
root = true
[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
insert_final_newline = true
trim_trailing_whitespace = true
```

### SSH Keys and Commit Signing

```bash
ssh-keygen -t ed25519 -C "you@example.com"
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub            # add to GitHub / GitLab / Azure DevOps

# Sign commits with the SSH key
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

### Environment Verification

```bash
git --version; node -v; docker --version; kubectl version --client
terraform -version; aws --version; az version | head -n 3
```

---

## Version Control and Collaboration

### Repository Initialization

```bash
mkdir -p ~/projects/taskflow && cd ~/projects/taskflow
git init
printf "# TaskFlow\n" > README.md
git add . && git commit -m "chore: initial commit"
```

### Repository Layout (Monorepo)

A monorepo keeps frontend, backend, and infrastructure versioned together and simplifies atomic changes. Use separate repositories only when teams, release cycles, or access rules differ strongly.

```text
taskflow/
├── apps/
│   ├── api/                  Backend service (TypeScript)
│   └── web/                  Frontend (React)
├── packages/
│   └── shared/               Shared types, validation schemas
├── infra/
│   ├── terraform/            Cloud infrastructure (modules, environments)
│   ├── k8s/                  Kubernetes manifests / Helm chart / Kustomize overlays
│   └── docker/               Dockerfiles, compose files
├── docs/
│   ├── adr/                  Architecture decision records
│   ├── runbooks/             Operational procedures
│   └── api/                  OpenAPI specification
├── scripts/                  Dev and ops helper scripts (bash)
├── .github/
│   ├── workflows/            CI/CD pipelines
│   ├── CODEOWNERS
│   └── pull_request_template.md
├── .editorconfig  .gitignore  .gitattributes
├── Makefile                  Common tasks: make setup, make test, make up
└── README.md
```

### Remote Hosting

| Platform | Strengths |
|---|---|
| GitHub | Largest ecosystem, Actions, Dependabot, code scanning |
| GitLab | Integrated CI/CD, self-hosting option |
| Azure Repos / DevOps | Native with Azure Pipelines and Boards |
| Bitbucket | Atlassian integration |

```bash
git remote add origin git@github.com:acme/taskflow.git
git push -u origin main
```

### Branching Strategy

| Strategy | Description | Best For |
|---|---|---|
| Trunk-based development | Short-lived branches (hours to 2 days), merge to `main` often, feature flags hide unfinished work | Teams with strong CI and testing (recommended) |
| GitHub Flow | Feature branch > pull request > merge to `main` > deploy | Most web products |
| Git Flow | `develop`, `release/*`, `hotfix/*` branches | Versioned or scheduled releases |

Branch naming: `feat/TF-123-task-comments`, `fix/TF-210-login-redirect`, `chore/update-deps`.

### Commit Convention (Conventional Commits)

```text
feat(api): add task comment endpoint
fix(web): prevent double submit on login
docs: add runbook for database failover
chore(deps): bump fastify
refactor, test, ci, perf, build

BREAKING CHANGE: in footer for incompatible changes
```

Benefits: automated changelogs and semantic version bumps.

### Pull Requests and Code Review

```markdown
<!-- .github/pull_request_template.md -->
## Summary
## Related ticket
## Type: feature / fix / refactor / docs / infra
## Testing done
## Screenshots (UI changes)
## Checklist
- [ ] Tests added or updated
- [ ] No secrets or credentials
- [ ] Documentation updated
- [ ] Migration is backward compatible
- [ ] Feature flag / rollback plan noted
```

```text
# .github/CODEOWNERS
/apps/api/       @acme/backend-team
/apps/web/       @acme/frontend-team
/infra/          @acme/devops-team
/docs/adr/       @acme/tech-leads
```

Review standards:

- At least one approval, two for infrastructure and security-sensitive code.
- Reviewers check correctness, tests, security, readability, and operational impact.
- Pull requests stay small (under about 400 changed lines); large work is split.
- Reviews are answered within one working day.
- Authors merge only after required checks pass.

### Branch Protection Rules for `main`

- Require pull request and approvals; dismiss stale approvals on new commits
- Require status checks (lint, unit tests, build, security scan)
- Require signed commits and linear history (squash merge)
- Restrict direct pushes and force pushes
- Require code owner review
- Include administrators in the rules

### Git Hooks

```bash
npm install -D husky lint-staged
npx husky init
echo "npx lint-staged" > .husky/pre-commit
```

```json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md,yml}": ["prettier --write"]
  }
}
```

### Ignore Files and Secret Hygiene

```text
# .gitignore
node_modules/
dist/
.env
.env.*
!.env.example
*.pem
*.tfstate
*.tfstate.*
.terraform/
coverage/
.DS_Store
```

```text
# .gitattributes
* text=auto
*.sh text eol=lf
Dockerfile text eol=lf
```

- Never commit secrets. Scan with `gitleaks` in pre-commit and CI.
- If a secret leaks, **rotate it immediately**; removing it from history is not enough.
- Provide `.env.example` with placeholders only.

### Versioning and Releases

```bash
git tag -a v1.4.0 -m "Release 1.4.0"
git push origin v1.4.0
```

Use **Semantic Versioning**: `MAJOR.MINOR.PATCH` (breaking, feature, fix). Tools such as `release-please` or `semantic-release` automate changelogs and tags from Conventional Commits.

---

## Project Scaffolding

### Application Layers

```text
HTTP route  ->  Controller / handler  ->  Service (business logic)  ->  Repository (data access)  ->  Database
     |                  |                        |
 validation        auth + mapping          transactions, rules
```

Keep business rules in the service layer, never in route handlers or SQL strings spread across the code. This keeps logic testable and replaceable.

### Backend Initialization

```bash
mkdir -p apps/api && cd apps/api
npm init -y
npm install fastify @fastify/cors @fastify/helmet @fastify/rate-limit @fastify/jwt zod pg pino
npm install -D typescript tsx vitest @types/node @types/pg eslint prettier
npx tsc --init --strict --target ES2022 --module NodeNext --moduleResolution NodeNext --outDir dist --rootDir src
mkdir -p src/{routes,services,repositories,plugins,config} tests
```

```json
{
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc -p .",
    "start": "node dist/server.js",
    "test": "vitest run",
    "lint": "eslint ."
  }
}
```

### Configuration Management

Follow the **Twelve-Factor App**: configuration comes from the environment; the same image runs in every environment.

```ts
// src/config/env.ts
import { z } from 'zod';

const schema = z.object({
  NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  REDIS_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  CORS_ORIGIN: z.string().url(),
});

export const env = schema.parse(process.env);   // fails fast at startup if misconfigured
```

```bash
# .env.example  (committed)  -- real values live in a secret manager
NODE_ENV=development
PORT=3000
DATABASE_URL=postgres://taskflow:taskflow@localhost:5432/taskflow
REDIS_URL=redis://localhost:6379
JWT_SECRET=change-me-to-a-long-random-string-at-least-32-chars
CORS_ORIGIN=http://localhost:5173
```

### Server Entry Point

```ts
// src/server.ts
import Fastify from 'fastify';
import helmet from '@fastify/helmet';
import cors from '@fastify/cors';
import rateLimit from '@fastify/rate-limit';
import { env } from './config/env.js';
import { pool } from './plugins/db.js';

const app = Fastify({
  logger: { level: 'info' },          // structured JSON logs
  genReqId: () => crypto.randomUUID() // correlation ID per request
});

await app.register(helmet);
await app.register(cors, { origin: env.CORS_ORIGIN, credentials: true });
await app.register(rateLimit, { max: 100, timeWindow: '1 minute' });

// Liveness: process is up. Readiness: dependencies are reachable.
app.get('/health/live', async () => ({ status: 'ok' }));
app.get('/health/ready', async (_req, reply) => {
  try { await pool.query('select 1'); return { status: 'ready' }; }
  catch { return reply.code(503).send({ status: 'unavailable' }); }
});

// app.register(authRoutes,  { prefix: '/api/v1/auth' });
// app.register(taskRoutes,  { prefix: '/api/v1/tasks' });

// Consistent error envelope
app.setErrorHandler((err, req, reply) => {
  req.log.error({ err }, 'request failed');
  const status = err.statusCode ?? 500;
  reply.code(status).send({
    error: { code: err.code ?? 'INTERNAL_ERROR', message: status < 500 ? err.message : 'Unexpected error', requestId: req.id },
  });
});

// Graceful shutdown so Kubernetes rolling updates drop no requests
for (const sig of ['SIGTERM', 'SIGINT'] as const) {
  process.on(sig, async () => { await app.close(); await pool.end(); process.exit(0); });
}

await app.listen({ port: env.PORT, host: '0.0.0.0' });
```

### API Design and Contract

- Version the API: `/api/v1/...`.
- Use nouns and HTTP verbs; return correct status codes (see API Design section earlier).
- Use cursor pagination for lists, idempotency keys for creating payments and orders.
- Write an **OpenAPI** specification first or generate it from schemas; publish it for frontend and client integrations.

```yaml
# docs/api/openapi.yaml (excerpt)
openapi: 3.1.0
info: { title: TaskFlow API, version: 1.0.0 }
paths:
  /api/v1/tasks:
    get:
      summary: List tasks
      parameters:
        - { name: cursor, in: query, schema: { type: string } }
        - { name: limit,  in: query, schema: { type: integer, maximum: 100 } }
      responses:
        '200': { description: OK }
    post:
      summary: Create task
      responses:
        '201': { description: Created }
        '400': { description: Validation error }
```

### Logging Conventions

- JSON logs to stdout (the platform collects them).
- Every log line carries `requestId`, `userId` (when known), `orgId`, and `service`.
- Never log passwords, tokens, full card numbers, or personal data beyond need.
- Levels: `error` (needs attention), `warn`, `info` (business events), `debug` (disabled in production).

### Makefile for Team Consistency

```makefile
.PHONY: setup up down test lint migrate

setup:      ; npm ci && cp -n .env.example .env
up:         ; docker compose up -d --build
down:       ; docker compose down
test:       ; npm test --workspaces
lint:       ; npm run lint --workspaces
migrate:    ; npm run migrate --workspace apps/api
```

---

## Database

### Database Selection

| Need | Choice |
|---|---|
| Relational data with transactions | PostgreSQL (default), MySQL, SQL Server |
| Flexible documents, rapid schema change | MongoDB, DynamoDB, Cosmos DB |
| Caching, counters, sessions, rate limits | Redis |
| Full-text search | OpenSearch / Elasticsearch, or PostgreSQL full-text search at small scale |
| Analytics | Warehouse (BigQuery, Snowflake, Redshift), not the OLTP database |

Start with one PostgreSQL database. Add specialized stores only when a measured need appears.

### Local Database with Docker Compose

```yaml
# infra/docker/compose.yaml
services:
  db:
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: taskflow
      POSTGRES_PASSWORD: taskflow
      POSTGRES_DB: taskflow
    ports: ["5432:5432"]
    volumes: [dbdata:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U taskflow"]
      interval: 5s
      retries: 10
  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
  api:
    build: { context: ../.., dockerfile: infra/docker/api.Dockerfile }
    env_file: ../../apps/api/.env
    depends_on: { db: { condition: service_healthy } }
    ports: ["3000:3000"]
  web:
    build: { context: ../.., dockerfile: infra/docker/web.Dockerfile }
    ports: ["5173:80"]
volumes:
  dbdata:
```

```bash
docker compose -f infra/docker/compose.yaml up -d
psql postgres://taskflow:taskflow@localhost:5432/taskflow
```

### Schema Design Boilerplate

```sql
-- migrations/001_init.sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;

CREATE TABLE organizations (
  id         uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name       text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE users (
  id            uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  email         text NOT NULL UNIQUE,
  password_hash text NOT NULL,
  full_name     text NOT NULL,
  is_active     boolean NOT NULL DEFAULT true,
  created_at    timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE memberships (
  org_id  uuid NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  user_id uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  role    text NOT NULL CHECK (role IN ('owner','admin','member','viewer')),
  PRIMARY KEY (org_id, user_id)
);

CREATE TABLE projects (
  id         uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id     uuid NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  name       text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE tasks (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id  uuid NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
  title       text NOT NULL,
  status      text NOT NULL DEFAULT 'todo' CHECK (status IN ('todo','doing','done')),
  assignee_id uuid REFERENCES users(id),
  due_date    date,
  version     integer NOT NULL DEFAULT 1,          -- optimistic locking
  created_at  timestamptz NOT NULL DEFAULT now(),
  updated_at  timestamptz NOT NULL DEFAULT now()
);

-- Indexes follow the queries, not the other way around
CREATE INDEX idx_tasks_project_status ON tasks (project_id, status);
CREATE INDEX idx_tasks_assignee_due   ON tasks (assignee_id, due_date);

CREATE TABLE audit_log (
  id         bigserial PRIMARY KEY,
  org_id     uuid NOT NULL,
  actor_id   uuid,
  action     text NOT NULL,
  entity     text NOT NULL,
  entity_id  uuid,
  metadata   jsonb,
  created_at timestamptz NOT NULL DEFAULT now()
);
```

Design principles: UUID or sequence primary keys, `timestamptz` for time, constraints in the database (not only in code), `org_id` on tenant data for isolation, soft delete only when required.

### Migrations

Schema changes are versioned code, applied automatically and in order.

| Ecosystem | Tools |
|---|---|
| Node | Prisma Migrate, Drizzle Kit, Knex, node-pg-migrate |
| Python | Alembic, Django migrations |
| Java | Flyway, Liquibase |
| .NET | EF Core Migrations |
| Language-neutral | Flyway, Atlas, dbmate |

```bash
npm install -D node-pg-migrate
npx node-pg-migrate create init
npx node-pg-migrate up      # apply
npx node-pg-migrate down    # roll back one step (dev only)
```

Zero-downtime migration practice (**expand and contract**):

```text
Expand:    add new nullable column / new table (old code still works)
Deploy:    release code that writes both and reads the new column
Backfill:  migrate old data in batches
Contract:  after a full release cycle, drop the old column
```

Never ship a migration that is incompatible with the currently running application version.

### Connection Pooling

```ts
// src/plugins/db.ts
import pg from 'pg';
import { env } from '../config/env.js';

export const pool = new pg.Pool({
  connectionString: env.DATABASE_URL,
  max: 10,                      // per pod; total = max * pods must stay below the database limit
  idleTimeoutMillis: 30_000,
  connectionTimeoutMillis: 5_000,
  ssl: env.NODE_ENV === 'production' ? { rejectUnauthorized: true } : false,
});
```

When many pods exist, put a pooler (**PgBouncer** or **RDS Proxy**) between the application and PostgreSQL.

### Caching with Redis (Cache-Aside)

```ts
async function getProject(id: string) {
  const key = `project:${id}`;
  const cached = await redis.get(key);
  if (cached) return JSON.parse(cached);

  const { rows } = await pool.query('select * from projects where id = $1', [id]);
  if (rows[0]) await redis.set(key, JSON.stringify(rows[0]), 'EX', 300 + Math.floor(Math.random() * 60)); // TTL jitter
  return rows[0] ?? null;
}
// Invalidate on update: await redis.del(`project:${id}`)
```

### Seed Data

```bash
npm run seed        # dev and staging only: sample organizations, users, tasks
```

Production never receives fake seed data. Use separate seeds for reference data (roles, countries) that production needs.

### Backups and Restore

```bash
pg_dump --format=custom --file=taskflow.dump "$DATABASE_URL"
pg_restore --clean --if-exists --dbname="$DATABASE_URL" taskflow.dump
```

Production practice: managed automated backups with **point-in-time recovery**, cross-region snapshot copies, encrypted backups, and **quarterly restore drills** documented in a runbook.

### Production Database Checklist

- Managed service (Amazon RDS / Aurora PostgreSQL, Azure Database for PostgreSQL Flexible Server)
- Multi-AZ (zone-redundant) for high availability
- Private subnets only; no public access; security group allows only the application
- Encryption at rest (KMS / Key Vault) and TLS in transit
- Automated backups, point-in-time recovery, deletion protection
- Parameter tuning, `pg_stat_statements`, slow query log
- Read replica for reporting and heavy reads
- Alarms: CPU, free storage, connections, replication lag, deadlocks

---

## Authentication and Authorization

### Build versus Buy

| Option | Examples | Use When |
|---|---|---|
| Managed identity provider | Amazon Cognito, Auth0, Clerk, Firebase Auth, Microsoft Entra External ID | Fastest and safest for most products |
| Self-hosted identity server | Keycloak, Ory, Authentik | Data control or cost at scale |
| Enterprise SSO | Entra ID, Okta, Google Workspace (SAML / OIDC) | Selling to organizations |
| Custom implementation | Argon2 + JWT/session as below | Simple needs, learning, or strict constraints |

Identity is high-risk code. Prefer a managed provider unless there is a clear reason not to, and in any case use standards: **OAuth 2.0** and **OpenID Connect**.

### Password Storage

```ts
import argon2 from 'argon2';

const hash = await argon2.hash(password);          // salted, memory-hard
const ok   = await argon2.verify(hash, attempt);
```

Rules: never store plaintext or reversible passwords; use Argon2id or bcrypt; enforce length (12+) over complexity; check against breached-password lists; rate-limit login; return identical messages for unknown email and wrong password.

### Session and Token Strategy

| Approach | Details |
|---|---|
| Server session | Random session ID in an `HttpOnly`, `Secure`, `SameSite` cookie; session data in Redis. Easy revocation |
| JWT access + refresh tokens | Short-lived access token (5-15 min), rotating refresh token stored `HttpOnly`; revoke by token family |
| Token storage in browser | Prefer `HttpOnly` cookies over `localStorage` (protects against XSS theft) |

```ts
// Issue tokens after verifying credentials
const accessToken = app.jwt.sign({ sub: user.id, org: orgId, role }, { expiresIn: '10m' });
reply.setCookie('refresh', refreshToken, {
  httpOnly: true, secure: true, sameSite: 'strict', path: '/api/v1/auth', maxAge: 60 * 60 * 24 * 14,
});
```

### OAuth 2.0 and OpenID Connect Login

```text
Browser -> App: click "Sign in with Google / Microsoft"
App -> Provider: redirect with client_id, redirect_uri, scope=openid email profile, state, PKCE code_challenge
Provider -> User: authenticate and consent
Provider -> App: redirect back with authorization code
App -> Provider: exchange code (+ PKCE verifier) for ID token and access token
App: validate ID token signature, issuer, audience, expiry, nonce; create or link local user; start session
```

Always use the **Authorization Code flow with PKCE**; avoid the implicit flow.

### Multi-Factor Authentication

- TOTP authenticator apps (RFC 6238), WebAuthn / passkeys (strongest), SMS only as a fallback.
- Offer recovery codes; require MFA for admins.

### Authorization (RBAC)

```ts
const permissions = {
  owner:  ['project:create', 'project:delete', 'task:write', 'task:read', 'member:manage'],
  admin:  ['project:create', 'task:write', 'task:read', 'member:manage'],
  member: ['task:write', 'task:read'],
  viewer: ['task:read'],
} as const;

export const requirePermission = (perm: string) => async (req: any, reply: any) => {
  const allowed = permissions[req.user.role as keyof typeof permissions]?.includes(perm as never);
  if (!allowed) return reply.code(403).send({ error: { code: 'FORBIDDEN' } });
};

// app.delete('/projects/:id', { preHandler: [app.authenticate, requirePermission('project:delete')] }, handler);
```

Tenant isolation: **every query filters by `org_id`** derived from the authenticated token, never from client input. Consider PostgreSQL Row Level Security as a second line of defense.

### API Security Baseline (OWASP Top 10 Alignment)

| Risk | Control |
|---|---|
| Broken access control | Server-side authorization on every endpoint, deny by default, ownership checks |
| Injection | Parameterized queries, schema validation (`zod`), no string-built SQL |
| Cryptographic failures | TLS everywhere, strong hashing, KMS-managed keys |
| Insecure design | Threat modeling, abuse-case tests |
| Security misconfiguration | `helmet`, hardened images, minimal permissions, no debug in production |
| Vulnerable components | Dependabot, `npm audit`, image scanning |
| Authentication failures | MFA, rate limits, lockout, secure session cookies |
| Integrity failures | Signed images, protected CI, lockfiles |
| Logging failures | Security events logged, alerts on anomalies |
| SSRF | Allow-list outbound targets, block metadata IP ranges |
| CSRF | `SameSite` cookies, CSRF tokens for cookie-authenticated state changes |
| CORS | Explicit origin allow-list, never `*` with credentials |

### Secrets Handling

| Environment | Where Secrets Live |
|---|---|
| Local | `.env` (ignored by Git) |
| CI | CI secret store, preferably **OIDC federation** instead of long-lived keys |
| Cloud | AWS Secrets Manager / SSM Parameter Store, Azure Key Vault |
| Kubernetes | External Secrets Operator syncing from the cloud secret manager (do not commit Kubernetes `Secret` YAML with real values) |

Rotate secrets on a schedule and on staff changes.

---

## Frontend

### Initialization

```bash
cd apps
npm create vite@latest web -- --template react-ts
cd web && npm install
npm install react-router-dom @tanstack/react-query zod
```

### Structure and Practices

```text
apps/web/src/
├── api/          typed API client (generated from OpenAPI)
├── components/   reusable UI
├── features/     feature folders (tasks, projects, auth)
├── hooks/
├── routes/
├── lib/
└── main.tsx
```

| Concern | Practice |
|---|---|
| Server state | TanStack Query (caching, retries, invalidation) |
| Routing | React Router with route guards for authenticated pages |
| Configuration | Build-time variables `VITE_API_URL`; never place secrets in frontend code |
| Accessibility | Semantic HTML, keyboard navigation, WCAG 2.2 AA checks (axe) |
| Performance | Code splitting, image optimization, caching headers, Core Web Vitals monitoring |
| Error handling | Error boundaries, user-friendly messages, Sentry reporting |
| Security | Escape output (React does by default), Content Security Policy, avoid `dangerouslySetInnerHTML` |
| Build output | Static files in `dist/`, hosted on object storage + CDN or NGINX |

```ts
// src/api/client.ts
export async function api<T>(path: string, init: RequestInit = {}): Promise<T> {
  const res = await fetch(`${import.meta.env.VITE_API_URL}${path}`, {
    credentials: 'include',
    headers: { 'Content-Type': 'application/json', ...init.headers },
    ...init,
  });
  if (!res.ok) throw new Error((await res.json()).error?.message ?? res.statusText);
  return res.json();
}
```

---

## Testing

### Testing Strategy

```text
            /\            Few    End-to-end (browser, full stack)      slow, brittle, high confidence
           /  \
          /----\          Some   Integration / API / contract tests    real DB, real Redis
         /      \
        /--------\        Many   Unit tests                            fast, isolated
       /__________\
   Static analysis, type checks, linting, security scans (every commit)
```

| Test Type | Tools (TypeScript stack) | Runs |
|---|---|---|
| Static analysis | TypeScript, ESLint, Prettier | Editor, pre-commit, CI |
| Unit | Vitest / Jest | Every commit |
| Integration | Vitest + Supertest + Testcontainers | Every pull request |
| Contract | Pact, OpenAPI validators | Every pull request |
| End-to-end | Playwright, Cypress | Pull request (smoke), nightly (full) |
| Load / performance | k6, Locust, JMeter | Before release, scheduled |
| Security | Dependency scan, SAST (CodeQL, Semgrep), container scan (Trivy), DAST (OWASP ZAP) | Every pull request, nightly |
| Accessibility | axe, Lighthouse CI | Pull request |
| Infrastructure | `terraform validate`, Checkov, Terratest, `kubeconform` | Every infra change |
| UAT | Client acceptance in staging | Each release |

### Unit Test Boilerplate

```ts
// tests/task.service.test.ts
import { describe, it, expect, vi } from 'vitest';
import { createTask } from '../src/services/task.service.js';

describe('createTask', () => {
  it('rejects empty titles', async () => {
    await expect(createTask({ title: '', projectId: 'p1' }, { userId: 'u1' })).rejects.toThrow('title');
  });

  it('stores the task and writes an audit entry', async () => {
    const repo = { insert: vi.fn().mockResolvedValue({ id: 't1' }) };
    const audit = { record: vi.fn() };
    const task = await createTask({ title: 'Ship', projectId: 'p1' }, { userId: 'u1' }, { repo, audit });
    expect(task.id).toBe('t1');
    expect(audit.record).toHaveBeenCalledOnce();
  });
});
```

### Integration Test with a Real Database (Testcontainers)

```ts
import { PostgreSqlContainer } from '@testcontainers/postgresql';
import request from 'supertest';
import { buildApp } from '../src/app.js';

let container: Awaited<ReturnType<PostgreSqlContainer['start']>>;
beforeAll(async () => {
  container = await new PostgreSqlContainer('postgres:17-alpine').start();
  process.env.DATABASE_URL = container.getConnectionUri();
  // run migrations here
});
afterAll(async () => { await container.stop(); });

it('creates and lists tasks', async () => {
  const app = await buildApp();
  await request(app.server).post('/api/v1/tasks').set('Authorization', `Bearer ${token}`).send({ title: 'A' }).expect(201);
  const res = await request(app.server).get('/api/v1/tasks').set('Authorization', `Bearer ${token}`).expect(200);
  expect(res.body.items).toHaveLength(1);
});
```

### End-to-End Test Boilerplate (Playwright)

```bash
cd apps/web && npm init playwright@latest
```

```ts
import { test, expect } from '@playwright/test';

test('user can sign in and create a task', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('demo@example.com');
  await page.getByLabel('Password').fill(process.env.E2E_PASSWORD!);
  await page.getByRole('button', { name: 'Sign in' }).click();
  await page.getByRole('button', { name: 'New task' }).click();
  await page.getByLabel('Title').fill('Write docs');
  await page.getByRole('button', { name: 'Save' }).click();
  await expect(page.getByText('Write docs')).toBeVisible();
});
```

### Load Test Boilerplate (k6)

```js
// tests/load/tasks.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 },   // ramp up
    { duration: '10m', target: 100 },  // steady
    { duration: '2m', target: 0 },
  ],
  thresholds: { http_req_duration: ['p(95)<300'], http_req_failed: ['rate<0.01'] },
};

export default function () {
  const res = http.get(`${__ENV.BASE_URL}/api/v1/tasks`, { headers: { Authorization: `Bearer ${__ENV.TOKEN}` } });
  check(res, { 'status 200': (r) => r.status === 200 });
  sleep(1);
}
```

```bash
k6 run -e BASE_URL=https://staging.taskflow.example.com -e TOKEN=$TOKEN tests/load/tasks.js
```

### Security Testing Commands

```bash
npm audit --audit-level=high                       # dependency vulnerabilities
gitleaks detect --source .                         # leaked secrets
trivy image taskflow-api:latest                    # container image vulnerabilities
trivy fs --scanners vuln,secret,misconfig .        # repository scan
checkov -d infra/terraform                         # IaC misconfigurations
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://staging.taskflow.example.com   # DAST
```

### Quality Gates

| Gate | Typical Threshold |
|---|---|
| Unit test pass rate | 100% |
| Coverage on changed code | 80% or higher (coverage is a signal, not a goal) |
| Critical / high vulnerabilities | Zero unaccepted |
| Linting and type errors | Zero |
| p95 latency in load test | Within the SLO |
| Accessibility violations | No serious or critical |
| Flaky test policy | Quarantine and fix within the sprint |

### Test Environments and Data

| Environment | Purpose | Data |
|---|---|---|
| Local | Development | Seed data, containers |
| CI | Automated checks | Ephemeral containers |
| Dev / Preview | Shared integration, per-pull-request preview | Synthetic |
| Staging | Production-like, UAT, load tests | Anonymized or synthetic; never raw production personal data |
| Production | Live | Real |

### User Acceptance Testing and Sign-off

- Provide the client a staging URL, test accounts, and a scenario checklist mapped to acceptance criteria.
- Log defects with severity (blocker, major, minor) and agree on exit criteria.
- Record written sign-off before production release.
- Run a smoke test checklist immediately after each production deployment.

---

## Debugging

### Local Debugging (VS Code)

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "API (tsx)",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "npx",
      "runtimeArgs": ["tsx", "src/server.ts"],
      "cwd": "${workspaceFolder}/apps/api",
      "envFile": "${workspaceFolder}/apps/api/.env",
      "skipFiles": ["<node_internals>/**"]
    },
    {
      "name": "Attach to container",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "address": "localhost",
      "localRoot": "${workspaceFolder}/apps/api",
      "remoteRoot": "/app"
    }
  ]
}
```

Techniques: breakpoints and logpoints, conditional breakpoints, watch expressions, step through, reproduce with a failing test first, `git bisect` to find the commit that introduced a bug.

```bash
git bisect start && git bisect bad && git bisect good v1.3.0
# test each step, then: git bisect good | git bisect bad ; finish with: git bisect reset
```

### Container Debugging

```bash
docker ps -a                                   # status, exit codes
docker logs -f --tail 100 taskflow-api
docker exec -it taskflow-api sh                # shell inside the running container
docker inspect taskflow-api | jq '.[0].State'
docker stats                                   # CPU and memory
docker compose logs -f api db
docker run --rm -it --entrypoint sh taskflow-api:latest   # inspect the image without running the app
```

### Kubernetes Debugging

```bash
kubectl get pods -n taskflow -o wide
kubectl describe pod <pod> -n taskflow          # events: scheduling, image pull, probe failures
kubectl logs <pod> -n taskflow --previous       # logs from the crashed container
kubectl logs -l app=api -n taskflow --tail=100 -f
kubectl get events -n taskflow --sort-by=.lastTimestamp
kubectl exec -it <pod> -n taskflow -- sh
kubectl port-forward svc/api 3000:80 -n taskflow
kubectl top pods -n taskflow
kubectl debug -it <pod> -n taskflow --image=busybox --target=api   # ephemeral debug container
kubectl rollout status deploy/api -n taskflow
kubectl rollout undo deploy/api -n taskflow
```

| Pod State | Meaning | First Checks |
|---|---|---|
| `Pending` | Not scheduled | Insufficient CPU/memory, node selectors, taints, PVC not bound (`describe pod`) |
| `ImagePullBackOff` | Cannot pull the image | Wrong tag or registry credentials, ECR/ACR permissions |
| `CrashLoopBackOff` | Container keeps exiting | `logs --previous`, missing env vars or secrets, failing migrations |
| `OOMKilled` | Memory limit exceeded | Raise limit, fix leak, check `kubectl top` |
| `Running` but not Ready | Readiness probe failing | Probe path, dependencies (database), startup time |
| `Evicted` | Node under pressure | Resource requests, node disk and memory |
| `CreateContainerConfigError` | Missing ConfigMap or Secret | Check names and keys |

### Network Debugging

Work down the path: client > DNS > CDN/WAF > load balancer > ingress/gateway > service > pod > dependency.

```bash
dig +short api.taskflow.example.com            # DNS resolves?
curl -v https://api.taskflow.example.com/health/ready     # TLS, headers, status
curl -I --resolve api.taskflow.example.com:443:203.0.113.10 https://api.taskflow.example.com   # test a specific IP
openssl s_client -connect api.taskflow.example.com:443 -servername api.taskflow.example.com    # certificate chain
nc -zv db.internal 5432                        # port reachable from this host?
traceroute api.taskflow.example.com
ss -tulnp                                      # what is listening locally
sudo tcpdump -i any port 3000 -nn              # raw packets (use with care)
kubectl run net-debug --rm -it --image=nicolaka/netshoot -- bash     # in-cluster tools
```

| Symptom | Likely Layer |
|---|---|
| `NXDOMAIN` | DNS record missing or wrong hosted zone |
| Certificate warning | Wrong certificate, expired, name mismatch, incomplete chain |
| `502 Bad Gateway` | Load balancer cannot reach healthy targets (health check path, security group, port) |
| `503` | No healthy backends, overload, rolling update gap |
| `504` | Backend too slow, timeout mismatch between layers |
| Connection timeout from pod to database | Security group, NACL, route table, wrong subnet |
| Works locally, fails in cluster | Env vars, secrets, DNS, network policy, resource limits |

### Database Debugging

```sql
EXPLAIN (ANALYZE, BUFFERS) SELECT * FROM tasks WHERE project_id = '...' AND status = 'todo';

SELECT query, calls, mean_exec_time, rows
FROM pg_stat_statements ORDER BY mean_exec_time DESC LIMIT 10;

SELECT pid, state, wait_event_type, now() - query_start AS running, query
FROM pg_stat_activity WHERE state <> 'idle' ORDER BY running DESC;

SELECT * FROM pg_locks WHERE NOT granted;      -- blocked queries
```

### Production Debugging Principles

- Debug from **telemetry**: logs, metrics, traces, error tracking; do not attach debuggers to production.
- Use the **request ID** to follow one request across services.
- Reproduce in staging with a copy of the same image and configuration.
- Prefer **feature flags and rollbacks** over hot patches.
- Make small, reversible changes; record every action in the incident channel.

### Systematic Debugging Loop

```text
Observe symptom -> Define scope and time window -> Check recent changes (deploys, config, traffic)
-> Form hypothesis -> Test with the cheapest experiment -> Confirm root cause
-> Fix or roll back -> Verify -> Write postmortem and add a test or alert
```

### Error Tracking

Use Sentry (or similar) in both frontend and backend: stack traces, release tagging, source maps, user impact counts, and alert rules.

```ts
import * as Sentry from '@sentry/node';
Sentry.init({ dsn: process.env.SENTRY_DSN, environment: process.env.NODE_ENV, release: process.env.GIT_SHA });
```

---

## Containerization with Docker

### Backend Dockerfile (Multi-Stage, Non-Root)

```dockerfile
# infra/docker/api.Dockerfile
# Use the current Node LTS and pin the exact tag or digest your team tests
FROM node:24-alpine AS deps
WORKDIR /app
COPY package*.json ./
COPY apps/api/package*.json apps/api/
RUN npm ci --workspace apps/api --include-workspace-root

FROM deps AS build
COPY . .
RUN npm run build --workspace apps/api

FROM node:24-alpine AS runtime
ENV NODE_ENV=production
WORKDIR /app
RUN addgroup -S app && adduser -S app -G app
COPY --from=deps  /app/node_modules ./node_modules
COPY --from=build /app/apps/api/dist ./dist
COPY apps/api/package.json ./
USER app
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://127.0.0.1:3000/health/live || exit 1
CMD ["node", "dist/server.js"]
```

### Frontend Dockerfile

```dockerfile
FROM node:24-alpine AS build
WORKDIR /app
COPY . .
RUN npm ci && npm run build --workspace apps/web

FROM nginx:alpine
COPY infra/docker/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/apps/web/dist /usr/share/nginx/html
EXPOSE 80
```

```nginx
# infra/docker/nginx.conf
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;
  location / { try_files $uri /index.html; }                       # single-page app routing
  location /assets/ { expires 1y; add_header Cache-Control "public, immutable"; }
  add_header X-Content-Type-Options nosniff;
}
```

### .dockerignore

```text
node_modules
dist
.git
.env
.env.*
coverage
*.md
infra/terraform
```

### Build, Run, Tag, Push

```bash
docker build -f infra/docker/api.Dockerfile -t taskflow-api:dev .
docker run --rm -p 3000:3000 --env-file apps/api/.env taskflow-api:dev

# Tag with the Git commit for traceability; avoid relying on "latest"
GIT_SHA=$(git rev-parse --short HEAD)
docker tag taskflow-api:dev 123456789012.dkr.ecr.eu-west-1.amazonaws.com/taskflow-api:$GIT_SHA

aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.eu-west-1.amazonaws.com
docker push 123456789012.dkr.ecr.eu-west-1.amazonaws.com/taskflow-api:$GIT_SHA
```

Azure equivalent:

```bash
az acr login --name taskflowacr
docker tag taskflow-api:dev taskflowacr.azurecr.io/taskflow-api:$GIT_SHA
docker push taskflowacr.azurecr.io/taskflow-api:$GIT_SHA
```

### Image Hygiene and Security

- Use minimal base images (alpine, slim, distroless); run as a non-root user.
- Pin base image versions or digests; rebuild regularly to pick up patches.
- Never bake secrets into images or build arguments; use runtime configuration.
- Scan images (`trivy`, registry scanning) and sign them (`cosign`); generate an SBOM (`syft`).
- Make filesystems read-only where possible and drop Linux capabilities in Kubernetes.
- Keep one process per container; log to stdout and stderr.

### Portability

| Practice | Benefit |
|---|---|
| OCI-compliant images | Run on Docker, containerd, Podman, any cloud |
| Multi-architecture builds (`docker buildx build --platform linux/amd64,linux/arm64`) | Run on x86 and ARM (Apple Silicon, AWS Graviton) |
| Configuration from environment variables | Same image in every environment |
| Stateless containers | Replace, scale, and move freely |
| Dev/prod parity via Docker Compose | Fewer "works on my machine" issues |

---

## Networking

### Request Path

```text
Browser -> DNS -> CDN/WAF -> Load balancer (TLS termination) -> Ingress/Gateway -> Service -> Pod
                                                                                        |
                                                                    Database / Cache / Queue (private subnets)
```

### Virtual Private Cloud Design

```text
VPC 10.0.0.0/16
├── Public subnets  (10.0.0.0/24, 10.0.1.0/24 ... one per availability zone)
│     Load balancers, NAT gateways
├── Private app subnets  (10.0.10.0/24, 10.0.11.0/24 ...)
│     Kubernetes nodes / containers
└── Private data subnets (10.0.20.0/24, 10.0.21.0/24 ...)
      Databases, caches (no internet route)
```

| Component | Role |
|---|---|
| Internet Gateway | Public subnet access to the internet |
| NAT Gateway | Outbound internet for private subnets (costly; plan for it) |
| Route tables | Control traffic paths per subnet |
| Security groups | Stateful per-resource firewalls; allow only what is needed (database accepts only from app security group) |
| Network ACLs | Stateless subnet-level filters |
| VPC endpoints / Private Link | Reach S3, ECR, Secrets Manager privately, avoiding NAT cost and exposure |
| Bastion / Session Manager | Administrative access without opening SSH to the internet |
| VPN / Direct Connect / ExpressRoute | Connect to client or corporate networks |

Allocate CIDR ranges that do not overlap with other VPCs or client networks to keep peering and VPN options open.

### Security Group Rules (Example)

| Resource | Inbound Allowed From |
|---|---|
| Load balancer | `0.0.0.0/0` on 443 (80 redirects to 443) |
| Application nodes/pods | Load balancer security group on app port |
| PostgreSQL | Application security group on 5432 |
| Redis | Application security group on 6379 |
| Administration | None from the internet; use SSM / private VPN |

### DNS

| Record | Example | Use |
|---|---|---|
| `A` / `AAAA` | `app.example.com -> IP` | Direct address |
| `CNAME` | `www -> app.example.com` | Alias to another name |
| `ALIAS` / Azure alias | `example.com -> CloudFront / ALB` | Apex domain pointing to cloud resources |
| `MX`, `TXT` (SPF, DKIM, DMARC) | Email delivery and anti-spoofing | Mail |
| `TXT` | Domain verification | Certificates, SaaS |

Use a low TTL (60 to 300 seconds) before planned migrations, then raise it afterwards.

### TLS Certificates

| Option | Use |
|---|---|
| AWS Certificate Manager / Azure managed certificates | Free, auto-renewed, attach to load balancer or CDN |
| Let's Encrypt (`certbot`, `cert-manager` in Kubernetes) | Automated and free for self-managed endpoints |
| Commercial CA | EV/OV requirements, specific enterprise needs |

Enforce HTTPS, redirect HTTP to HTTPS, enable HSTS, support TLS 1.2 and 1.3 only.

### Load Balancing and Traffic Entry

| Need | AWS | Azure |
|---|---|---|
| HTTP(S) routing | Application Load Balancer | Application Gateway / Front Door |
| TCP/UDP | Network Load Balancer | Azure Load Balancer |
| Global CDN + WAF | CloudFront + AWS WAF | Front Door + WAF |
| In Kubernetes | AWS Load Balancer Controller (ALB), or a Gateway API implementation | Application Gateway for Containers, or a Gateway API implementation |

> The community **Ingress NGINX** controller has been retired by the Kubernetes project. For new clusters use the **Gateway API** with a maintained implementation (for example Envoy Gateway, Cilium, Traefik, NGINX Gateway Fabric) or the cloud provider's native controller. Verify the current recommendation in the Kubernetes documentation.

### Reverse Proxy Boilerplate (NGINX, for VM-based or non-Kubernetes setups)

```nginx
upstream api { server 127.0.0.1:3000; keepalive 32; }

server {
  listen 443 ssl http2;
  server_name api.taskflow.example.com;
  ssl_certificate     /etc/ssl/fullchain.pem;
  ssl_certificate_key /etc/ssl/privkey.pem;

  location / {
    proxy_pass http://api;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_read_timeout 30s;
  }
}
```

### CDN and Caching Headers

- Static assets: `Cache-Control: public, max-age=31536000, immutable` with hashed file names.
- `index.html`: `no-cache` so new releases appear immediately.
- API responses: `private, no-store` unless explicitly cacheable.
- Invalidate the CDN after deployment only for non-hashed paths.

### Network Security Layers

CDN with WAF and rate limiting, DDoS protection (AWS Shield, Azure DDoS Protection), private subnets for data, least-privilege security groups, Kubernetes **NetworkPolicies** that deny by default, mutual TLS between services if a service mesh is adopted, and egress control for sensitive workloads.

---

## Cloud Provider Setup

### Account Structure and Governance

| Practice | AWS | Azure |
|---|---|---|
| Multi-account / subscription separation | AWS Organizations with separate accounts for `dev`, `staging`, `prod`, `shared-services`, `security` | Management groups with separate subscriptions |
| Central sign-in | IAM Identity Center (SSO) | Microsoft Entra ID |
| Guardrails | Service Control Policies, AWS Config | Azure Policy, Defender for Cloud |
| Audit | CloudTrail (all regions, immutable bucket) | Activity Log, Entra audit logs |
| Cost tags | Required tags: `project`, `env`, `owner` | Same via Azure Policy |

First actions on a new account:

- Protect the **root / global administrator** account with MFA, store it in a vault, and never use it for daily work.
- Create named administrator access through SSO; no shared logins and no long-lived access keys for people.
- Enable audit logging and billing **budgets with alerts** before creating resources.
- Choose a **region** by latency to users, data residency, service availability, and price.

### CLI Configuration

```bash
# AWS (SSO based)
aws configure sso
aws sso login --profile taskflow-dev
aws sts get-caller-identity --profile taskflow-dev

# Azure
az login
az account set --subscription "taskflow-prod"
az account show
```

### Budget Alert Example

```bash
aws budgets create-budget --account-id 123456789012 --budget file://budget.json --notifications-with-subscribers file://notifications.json
```

### Service Mapping

| Capability | AWS | Azure |
|---|---|---|
| Virtual machines | EC2 | Virtual Machines |
| Managed Kubernetes | EKS | AKS |
| Serverless containers | ECS Fargate | Azure Container Apps |
| Web app platform | Elastic Beanstalk, Lightsail | App Service |
| Functions | Lambda | Azure Functions |
| Container registry | ECR | ACR |
| Managed PostgreSQL | RDS / Aurora PostgreSQL | Azure Database for PostgreSQL Flexible Server |
| Cache | ElastiCache (Redis / Valkey) | Azure Cache for Redis |
| Object storage | S3 | Blob Storage |
| CDN | CloudFront | Front Door / Azure CDN |
| DNS | Route 53 | Azure DNS |
| Load balancer | ALB / NLB | Application Gateway / Load Balancer |
| Queue / messaging | SQS, SNS, EventBridge | Service Bus, Event Grid |
| Secrets | Secrets Manager, SSM Parameter Store | Key Vault |
| Identity for users | Cognito | Entra External ID |
| Identity for workloads | IAM roles (IRSA / EKS Pod Identity) | Managed identities, Workload Identity |
| Monitoring | CloudWatch, X-Ray | Azure Monitor, Application Insights |
| IaC (native) | CloudFormation, CDK | Bicep, ARM templates |
| CI/CD (native) | CodePipeline, CodeBuild | Azure DevOps Pipelines |
| WAF | AWS WAF | Azure WAF |
| Key management | KMS | Key Vault / Managed HSM |

---

## Infrastructure as Code

Infrastructure is defined in version-controlled code so environments are reproducible, reviewable, and recoverable. Never build production by clicking in the console; use the console only to explore.

### Terraform Layout

```text
infra/terraform/
├── modules/
│   ├── network/        VPC, subnets, routes, endpoints
│   ├── eks/            Kubernetes cluster and node groups
│   ├── database/       RDS PostgreSQL
│   ├── cache/          ElastiCache
│   └── frontend/       S3, CloudFront, ACM, Route 53
├── envs/
│   ├── dev/    main.tf  variables.tf  terraform.tfvars  backend.tf
│   ├── staging/
│   └── prod/
└── README.md
```

Separate directories (or separate state files) per environment limit the blast radius of a mistake.

### Remote State and Provider Boilerplate

```hcl
# envs/prod/backend.tf
terraform {
  required_version = ">= 1.10"
  required_providers {
    aws = { source = "hashicorp/aws", version = "~> 6.0" }
  }
  backend "s3" {
    bucket       = "acme-tfstate-prod"
    key          = "taskflow/prod/terraform.tfstate"
    region       = "eu-west-1"
    encrypt      = true
    use_lockfile = true          # S3-native state locking (older setups used a DynamoDB lock table)
  }
}

provider "aws" {
  region = var.region
  default_tags { tags = { project = "taskflow", env = "prod", managed_by = "terraform" } }
}
```

State contains sensitive values: store it in a private, versioned, encrypted bucket with restricted access.

### Resource Boilerplate (Excerpts)

```hcl
# modules/network/main.tf
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  tags = { Name = "${var.name}-vpc" }
}

resource "aws_subnet" "private_app" {
  for_each          = toset(var.azs)
  vpc_id            = aws_vpc.main.id
  availability_zone = each.value
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, 10 + index(var.azs, each.value))
}

# modules/database/main.tf
resource "aws_db_instance" "postgres" {
  identifier                  = "${var.name}-pg"
  engine                      = "postgres"
  instance_class              = var.instance_class
  allocated_storage           = 50
  storage_encrypted           = true
  multi_az                    = true
  db_subnet_group_name        = aws_db_subnet_group.this.name
  vpc_security_group_ids      = [aws_security_group.db.id]
  manage_master_user_password = true            # password stored in Secrets Manager
  backup_retention_period     = 14
  deletion_protection         = true
  skip_final_snapshot         = false
  publicly_accessible         = false
}
```

### Terraform Workflow

```bash
terraform fmt -recursive
terraform init
terraform validate
terraform plan -out=tfplan        # always review the plan
terraform apply tfplan
terraform destroy                 # non-production only
```

In teams: run `plan` automatically on every pull request, post the plan as a comment, require review, and run `apply` only from the pipeline on `main` with an approval gate for production.

### Policy and Drift

| Practice | Tool |
|---|---|
| Static checks for misconfiguration | Checkov, tfsec / Trivy, Terraform `validate` |
| Policy as code | Open Policy Agent (Conftest), Sentinel |
| Cost estimation in pull requests | Infracost |
| Drift detection | Scheduled `terraform plan` that alerts when it is not empty |
| Alternatives | OpenTofu, Pulumi, AWS CDK, Bicep (Azure), Crossplane |

---

## Kubernetes

### When Kubernetes Fits

| Choose Kubernetes When | Consider Simpler Options When |
|---|---|
| Many services, multiple teams, need portability, custom scaling, or complex networking | One or two services, small team, standard web workloads |
| Team has or will build platform skills | No one can operate a cluster |

Simpler managed alternatives: **AWS ECS on Fargate**, **Azure Container Apps**, **Azure App Service**, **Google Cloud Run**. They remove node management and are often the right first production choice. The container image stays identical, so migrating to Kubernetes later is practical.

### Cluster Options

| Purpose | Options |
|---|---|
| Local development | `kind`, `minikube`, `k3d`, Docker Desktop Kubernetes |
| Managed cloud | Amazon EKS, Azure AKS, Google GKE |
| Self-managed | `kubeadm`, k3s, Rancher (higher operating cost) |

```bash
kind create cluster --name taskflow
kubectl cluster-info --context kind-taskflow
kind load docker-image taskflow-api:dev --name taskflow
```

### Core Objects

| Object | Purpose |
|---|---|
| Namespace | Isolation and quota boundary (per environment or team) |
| Pod | Smallest runnable unit |
| Deployment | Declarative rollout of stateless pods |
| Service | Stable virtual IP and DNS name for pods |
| Gateway / HTTPRoute (Gateway API) or Ingress | External HTTP routing |
| ConfigMap / Secret | Configuration / sensitive data |
| HorizontalPodAutoscaler | Scale pods by CPU, memory, or custom metrics |
| PodDisruptionBudget | Keep minimum pods during node maintenance |
| NetworkPolicy | Pod-level firewall |
| ServiceAccount, Role, RoleBinding | Kubernetes RBAC and workload identity |
| Job / CronJob | Batch and scheduled tasks (e.g. migrations) |
| StatefulSet, PersistentVolume | Stateful workloads (prefer managed databases instead) |

### Deployment Manifest Boilerplate

```yaml
# infra/k8s/base/api-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: taskflow
  labels: { app: api }
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate: { maxSurge: 1, maxUnavailable: 0 }
  selector:
    matchLabels: { app: api }
  template:
    metadata:
      labels: { app: api }
    spec:
      serviceAccountName: api
      securityContext: { runAsNonRoot: true, seccompProfile: { type: RuntimeDefault } }
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: topology.kubernetes.io/zone
          whenUnsatisfiable: ScheduleAnyway
          labelSelector: { matchLabels: { app: api } }
      containers:
        - name: api
          image: 123456789012.dkr.ecr.eu-west-1.amazonaws.com/taskflow-api:GIT_SHA
          ports: [{ containerPort: 3000 }]
          envFrom:
            - configMapRef: { name: api-config }
            - secretRef:    { name: api-secrets }     # created by External Secrets Operator
          resources:
            requests: { cpu: 250m, memory: 256Mi }
            limits:   { memory: 512Mi }
          readinessProbe: { httpGet: { path: /health/ready, port: 3000 }, initialDelaySeconds: 5, periodSeconds: 5 }
          livenessProbe:  { httpGet: { path: /health/live,  port: 3000 }, initialDelaySeconds: 15, periodSeconds: 10 }
          startupProbe:   { httpGet: { path: /health/live,  port: 3000 }, failureThreshold: 30, periodSeconds: 2 }
          securityContext: { allowPrivilegeEscalation: false, readOnlyRootFilesystem: true, capabilities: { drop: [ALL] } }
          lifecycle: { preStop: { exec: { command: ["sleep", "10"] } } }   # let load balancer drain
---
apiVersion: v1
kind: Service
metadata: { name: api, namespace: taskflow }
spec:
  selector: { app: api }
  ports: [{ port: 80, targetPort: 3000 }]
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata: { name: api, namespace: taskflow }
spec:
  scaleTargetRef: { apiVersion: apps/v1, kind: Deployment, name: api }
  minReplicas: 3
  maxReplicas: 20
  metrics:
    - type: Resource
      resource: { name: cpu, target: { type: Utilization, averageUtilization: 65 } }
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata: { name: api, namespace: taskflow }
spec:
  minAvailable: 2
  selector: { matchLabels: { app: api } }
```

### External Traffic with Gateway API

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata: { name: public, namespace: infra }
spec:
  gatewayClassName: <your-gateway-class>      # provided by the installed controller
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      hostname: api.taskflow.example.com
      tls: { mode: Terminate, certificateRefs: [{ name: api-tls }] }
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata: { name: api, namespace: taskflow }
spec:
  parentRefs: [{ name: public, namespace: infra }]
  hostnames: ["api.taskflow.example.com"]
  rules:
    - matches: [{ path: { type: PathPrefix, value: / } }]
      backendRefs: [{ name: api, port: 80 }]
```

On EKS, many teams instead use the **AWS Load Balancer Controller** with an `Ingress` (`ingressClassName: alb`) or Gateway resources so an AWS Application Load Balancer fronts the cluster and uses an ACM certificate.

### Configuration and Secrets

```yaml
apiVersion: v1
kind: ConfigMap
metadata: { name: api-config, namespace: taskflow }
data:
  NODE_ENV: production
  CORS_ORIGIN: https://app.taskflow.example.com
---
# External Secrets Operator pulls values from AWS Secrets Manager / Azure Key Vault
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata: { name: api-secrets, namespace: taskflow }
spec:
  refreshInterval: 1h
  secretStoreRef: { name: aws-secrets, kind: ClusterSecretStore }
  target: { name: api-secrets }
  dataFrom: [{ extract: { key: taskflow/prod/api } }]
```

### Migrations as Jobs

```yaml
apiVersion: batch/v1
kind: Job
metadata: { name: migrate-GIT_SHA, namespace: taskflow }
spec:
  backoffLimit: 1
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: migrate
          image: 123456789012.dkr.ecr.eu-west-1.amazonaws.com/taskflow-api:GIT_SHA
          command: ["npm", "run", "migrate"]
          envFrom: [{ secretRef: { name: api-secrets } }]
```

### Helm and Kustomize

| Tool | Use |
|---|---|
| Helm | Package manager and templating; ideal for third-party charts and parameterized apps |
| Kustomize | Overlays on plain YAML (`base/` + `overlays/staging`, `overlays/prod`) |

```bash
helm create taskflow                                  # chart skeleton
helm lint ./taskflow
helm upgrade --install api ./taskflow -n taskflow --create-namespace -f values-prod.yaml --atomic --timeout 5m
helm rollback api 1 -n taskflow
```

```text
infra/k8s/
├── base/                 deployment.yaml service.yaml hpa.yaml kustomization.yaml
└── overlays/
    ├── staging/          kustomization.yaml (replicas, image tag, hostnames)
    └── prod/
```

### Cluster Platform Add-ons

| Add-on | Purpose |
|---|---|
| Metrics Server | Required for HPA and `kubectl top` |
| Cluster Autoscaler or Karpenter | Add and remove nodes automatically |
| Gateway / ingress controller | Entry point (see networking) |
| cert-manager | Automated TLS certificates |
| External Secrets Operator | Secret synchronization |
| External DNS | Creates DNS records from Kubernetes resources |
| Prometheus + Grafana, Loki, OpenTelemetry Collector | Observability |
| Argo CD or Flux | GitOps delivery |
| Kyverno or OPA Gatekeeper | Policy enforcement |
| Network policy engine (Calico, Cilium) | Network segmentation |

### Managed Cluster Creation

```bash
# AWS EKS (quick start with eksctl; production clusters are defined in Terraform)
eksctl create cluster --name taskflow-prod --region eu-west-1 --version <supported-version> \
  --nodegroup-name general --node-type m6i.large --nodes 3 --nodes-min 3 --nodes-max 10 --managed
aws eks update-kubeconfig --name taskflow-prod --region eu-west-1

# Azure AKS
az group create -n taskflow-rg -l westeurope
az aks create -g taskflow-rg -n taskflow-prod --node-count 3 --enable-managed-identity --enable-oidc-issuer --enable-workload-identity --generate-ssh-keys
az aks get-credentials -g taskflow-rg -n taskflow-prod
```

Choose a Kubernetes version inside the provider's supported window and plan upgrades several times a year.

### Teams and Access Control in Kubernetes

| Practice | Details |
|---|---|
| Namespaces per environment or team | `taskflow-staging`, `taskflow-prod`, `platform` |
| RBAC | Developers: read and exec in staging; production read-only; deploys happen via GitOps |
| Resource quotas and LimitRanges | Prevent one team starving others |
| Pod Security Standards | Enforce `restricted` profile |
| Workload identity | Pods use IAM roles (IRSA / EKS Pod Identity) or Azure Workload Identity instead of static keys |
| Audit | Enable control plane audit logs |

### GitOps with Argo CD

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata: { name: taskflow-prod, namespace: argocd }
spec:
  project: default
  source:
    repoURL: https://github.com/acme/taskflow-deploy.git
    targetRevision: main
    path: overlays/prod
  destination: { server: https://kubernetes.default.svc, namespace: taskflow }
  syncPolicy:
    automated: { prune: true, selfHeal: true }
    syncOptions: [CreateNamespace=true]
```

Pipeline builds an image, updates the image tag in the deployment repository through a pull request, and Argo CD reconciles the cluster to match Git. Rollback is `git revert`.

---

## CI/CD Pipeline

### Pipeline Stages

```text
Pull request:   lint -> type check -> unit tests -> integration tests -> build -> security scans -> preview deploy (optional)
Merge to main:  build image -> scan -> push to registry -> deploy to staging -> smoke + e2e tests
Release:        approval -> deploy to production (canary/rolling) -> smoke tests -> monitor -> auto-rollback on failure
```

### Continuous Integration Workflow (GitHub Actions)

```yaml
# .github/workflows/ci.yml
name: ci
on:
  pull_request:
  push: { branches: [main] }

permissions: { contents: read }

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:17-alpine
        env: { POSTGRES_PASSWORD: postgres, POSTGRES_DB: taskflow_test }
        ports: ["5432:5432"]
        options: >-
          --health-cmd "pg_isready -U postgres" --health-interval 5s --health-retries 10
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 'lts/*', cache: npm }
      - run: npm ci
      - run: npm run lint --workspaces
      - run: npm run build --workspaces
      - run: npm test --workspaces
        env: { DATABASE_URL: postgres://postgres:postgres@localhost:5432/taskflow_test }
      - run: npm audit --audit-level=high

  security:
    runs-on: ubuntu-latest
    permissions: { contents: read, security-events: write }
    steps:
      - uses: actions/checkout@v4
      - uses: github/codeql-action/init@v3
        with: { languages: javascript-typescript }
      - uses: github/codeql-action/analyze@v3
```

Pin third-party actions to a full commit SHA in production pipelines and keep them updated with Dependabot.

### Build and Push Workflow with OIDC (No Stored Cloud Keys)

```yaml
# .github/workflows/build-push.yml
name: build-and-push
on:
  push: { branches: [main] }

permissions:
  id-token: write          # request an OIDC token for cloud authentication
  contents: read

jobs:
  image:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789012:role/github-actions-ecr-push
          aws-region: eu-west-1
      - id: ecr
        uses: aws-actions/amazon-ecr-login@v2
      - uses: docker/setup-buildx-action@v3
      - uses: docker/build-push-action@v6
        with:
          context: .
          file: infra/docker/api.Dockerfile
          push: true
          tags: ${{ steps.ecr.outputs.registry }}/taskflow-api:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
      - name: Scan image
        uses: aquasecurity/trivy-action@master      # pin to a version or SHA in real pipelines
        with:
          image-ref: ${{ steps.ecr.outputs.registry }}/taskflow-api:${{ github.sha }}
          severity: CRITICAL,HIGH
          exit-code: '1'
```

The IAM role's trust policy allows only this repository and branch to assume it, replacing long-lived access keys.

### Deployment Workflow (GitOps Update)

```yaml
# .github/workflows/deploy-staging.yml (excerpt)
- name: Update image tag in deployment repo
  run: |
    git clone https://x-access-token:${{ secrets.DEPLOY_REPO_TOKEN }}@github.com/acme/taskflow-deploy.git
    cd taskflow-deploy/overlays/staging
    kustomize edit set image taskflow-api=${{ steps.ecr.outputs.registry }}/taskflow-api:${{ github.sha }}
    git commit -am "deploy: api ${{ github.sha }}" && git push
```

For non-GitOps setups, the final step is `helm upgrade --install --atomic` or `kubectl apply` using a cluster role limited to the target namespace.

### Environments and Approvals

| Environment | Trigger | Approval | Data |
|---|---|---|---|
| Preview (per pull request) | Pull request opened | None | Synthetic |
| Staging | Every merge to `main` | None | Synthetic or anonymized |
| Production | Tag or manual promote | Required reviewers (tech lead + product owner) | Live |

Use GitHub Environments with protection rules, required reviewers, wait timers, and environment-scoped secrets.

### Deployment Strategies

| Strategy | Mechanism | Use |
|---|---|---|
| Rolling | Replace pods gradually (Kubernetes default) | Standard releases |
| Blue-green | Two full environments, switch traffic | Fast rollback, major changes |
| Canary | 1-5% of traffic to new version, then increase (Argo Rollouts, Flagger) | High-risk or high-traffic changes |
| Feature flags | Deploy dark, enable per user or percentage (LaunchDarkly, Unleash, OpenFeature) | Decouple deploy from release |
| Database changes | Expand-and-contract migrations | Zero-downtime upgrades |

### Rollback Procedure

```bash
kubectl rollout undo deployment/api -n taskflow              # imperative rollback
helm rollback api <revision> -n taskflow                     # Helm
git revert <commit> && git push                              # GitOps rollback
```

Rollback rules: always keep the previous image available; migrations must be backward compatible so the old version can run against the new schema; define automatic rollback triggers (error rate or latency threshold after deployment).

### Alternative CI/CD Platforms

| Platform | Notes |
|---|---|
| GitLab CI (`.gitlab-ci.yml`) | Integrated registry, environments, auto DevOps |
| Azure DevOps Pipelines (`azure-pipelines.yml`) | Natural fit for Azure and Microsoft shops |
| Jenkins | Self-hosted, very flexible, higher maintenance |
| CircleCI, Buildkite, Bitbucket Pipelines | Hosted/hybrid options |
| AWS CodePipeline / CodeBuild | Native AWS integration |

---

## Deploying to AWS

### Provisioning and Deployment Sequence

The order below respects dependencies: networking first, data stores next, compute and delivery last.

```text
Account and guardrails -> Remote state bucket -> VPC and subnets -> VPC endpoints
-> KMS keys and Secrets Manager entries -> ECR repositories -> RDS PostgreSQL + ElastiCache
-> EKS cluster (or ECS) + node groups -> Cluster add-ons (controllers, cert-manager, External Secrets)
-> S3 + CloudFront for the frontend -> ACM certificates -> Route 53 records
-> CI/CD roles (OIDC) -> Deploy application -> Run migrations -> Smoke tests -> Monitoring and alerts
```

```bash
# Bootstrap (once per environment)
aws s3api create-bucket --bucket acme-tfstate-prod --region eu-west-1 \
  --create-bucket-configuration LocationConstraint=eu-west-1
aws s3api put-bucket-versioning --bucket acme-tfstate-prod --versioning-configuration Status=Enabled

# Infrastructure
cd infra/terraform/envs/prod
terraform init && terraform plan -out=tfplan && terraform apply tfplan

# Cluster access and application deployment
aws eks update-kubeconfig --name taskflow-prod --region eu-west-1
kubectl get nodes
kubectl apply -k infra/k8s/overlays/prod        # or let Argo CD sync from Git
kubectl rollout status deploy/api -n taskflow
```

### Static Frontend on S3 and CloudFront

```bash
npm run build --workspace apps/web
aws s3 sync apps/web/dist s3://taskflow-web-prod --delete --cache-control "public,max-age=31536000,immutable" --exclude index.html
aws s3 cp apps/web/dist/index.html s3://taskflow-web-prod/index.html --cache-control "no-cache"
aws cloudfront create-invalidation --distribution-id E123EXAMPLE --paths "/index.html"
```

The S3 bucket stays private; CloudFront reads it through an origin access control. Configure a custom error response that returns `index.html` for client-side routes.

### Simpler AWS Paths

| Option | Fits |
|---|---|
| ECS on Fargate + ALB + RDS | Containers without cluster operations |
| Elastic Beanstalk | Quick managed web apps |
| Lightsail | Very small projects and demos |
| Lambda + API Gateway + DynamoDB | Spiky or low-traffic event-driven backends |

### Production Hardening on AWS

- Multi-AZ for RDS and node groups; at least two NAT gateways or VPC endpoints for critical paths
- IAM roles with least privilege, `IRSA` / Pod Identity for pods
- AWS WAF on CloudFront and ALB, Shield for DDoS, GuardDuty and Security Hub enabled
- CloudTrail, Config, and VPC Flow Logs enabled to a protected log account
- S3 Block Public Access, encryption by default, versioning for important buckets

---

## Deploying to Azure

### Equivalent Provisioning Sequence

```bash
az group create -n taskflow-prod-rg -l westeurope

# Registry and network
az acr create -g taskflow-prod-rg -n taskflowacr --sku Standard
az network vnet create -g taskflow-prod-rg -n taskflow-vnet --address-prefix 10.0.0.0/16

# Database and cache
az postgres flexible-server create -g taskflow-prod-rg -n taskflow-pg --version 17 \
  --tier GeneralPurpose --sku-name Standard_D2ds_v5 --high-availability ZoneRedundant --public-access None
az redis create -g taskflow-prod-rg -n taskflow-redis --sku Standard --vm-size c1 -l westeurope

# Secrets
az keyvault create -g taskflow-prod-rg -n taskflow-kv --enable-rbac-authorization true

# Compute (Kubernetes) with registry access
az aks create -g taskflow-prod-rg -n taskflow-aks --node-count 3 --attach-acr taskflowacr \
  --enable-managed-identity --enable-oidc-issuer --enable-workload-identity --generate-ssh-keys
az aks get-credentials -g taskflow-prod-rg -n taskflow-aks

# Frontend
az storage account create -g taskflow-prod-rg -n taskflowweb --sku Standard_LRS
az staticwebapp create -g taskflow-prod-rg -n taskflow-web      # or Storage static website + Front Door
```

Define the same resources in Terraform (`azurerm` provider) or Bicep for repeatability.

### Simpler Azure Paths

| Option | Fits |
|---|---|
| Azure Container Apps | Serverless containers with scale-to-zero, built-in ingress and Dapr/KEDA |
| Azure App Service | Web apps and APIs with deployment slots for blue-green |
| Azure Static Web Apps | Frontend plus lightweight APIs |
| Azure Functions | Event-driven backends |

```bash
az containerapp env create -g taskflow-prod-rg -n taskflow-env -l westeurope
az containerapp create -g taskflow-prod-rg -n taskflow-api --environment taskflow-env \
  --image taskflowacr.azurecr.io/taskflow-api:$GIT_SHA --target-port 3000 --ingress external \
  --min-replicas 2 --max-replicas 10 --registry-server taskflowacr.azurecr.io
```

---

## Getting a Live Public URL

### Fast Temporary URLs for Demos

| Method | Command or Service | Notes |
|---|---|---|
| Tunnel to local machine | `cloudflared tunnel --url http://localhost:5173`, `ngrok http 5173` | Temporary, for demos and webhooks testing only |
| Frontend platforms | Vercel, Netlify, Cloudflare Pages, GitHub Pages | Git push gives a public HTTPS URL |
| Backend platforms | Render, Railway, Fly.io, Heroku-style PaaS | Container or source deploy with managed TLS |
| Cloud default URLs | CloudFront `d111111abcdef8.cloudfront.net`, ALB DNS name, Azure `*.azurewebsites.net`, `*.azurecontainerapps.io` | Works immediately, no domain needed |

These are suitable for staging and client previews; production should use the client's own domain.

### Custom Domain on AWS (Production)

```text
Register domain (Route 53 or any registrar)
 -> Create hosted zone for example.com in Route 53
 -> Set the registrar's name servers to the four Route 53 NS values (skip if registered in Route 53)
 -> Request ACM certificates:
      CloudFront certificate in us-east-1 for app.example.com
      Regional certificate for api.example.com (ALB region)
 -> Add the DNS validation CNAME records ACM provides (Route 53 can create them automatically)
 -> Attach the certificate to CloudFront and to the ALB / Gateway
 -> Create alias records:  app.example.com -> CloudFront,  api.example.com -> ALB
 -> Verify propagation and HTTPS
```

```bash
dig +short NS example.com
dig +short app.example.com
curl -I https://app.example.com
curl -s https://api.example.com/health/ready
```

On Kubernetes, **External DNS** and **cert-manager** can create DNS records and certificates automatically from route annotations or resources.

### Custom Domain on Azure

```bash
az network dns zone create -g taskflow-prod-rg -n example.com
# Point registrar nameservers to the zone's NS values, then:
az network dns record-set cname set-record -g taskflow-prod-rg -z example.com -n app -c <frontdoor-endpoint>.azurefd.net
# Add the custom domain in Front Door / App Service / Container Apps; use a managed certificate
```

### Environment URL Convention

| Environment | Frontend | API |
|---|---|---|
| Production | `app.example.com` | `api.example.com` |
| Staging | `staging.example.com` | `api.staging.example.com` |
| Development | `dev.example.com` | `api.dev.example.com` |
| Preview | `pr-123.preview.example.com` | same host with path or subdomain |

### Go-Live Verification

- DNS resolves from multiple locations; certificate is valid and chain complete (SSL Labs test)
- HTTP redirects to HTTPS; HSTS and security headers present
- Login, core user journey, file upload, and email sending work with real data paths
- Monitoring dashboards show traffic; alerts reach the on-call channel
- Search engine settings (`robots.txt`, canonical URLs) match intent (block staging from indexing)
- Transactional email domain records (SPF, DKIM, DMARC) validated

---

## Scalability

### Scaling Principles

| Principle | Application |
|---|---|
| Stateless services | Keep sessions in Redis or tokens; store files in object storage; any pod can serve any request |
| Scale horizontally first | HPA and node autoscaling; vertical scaling for databases until replicas and partitioning are needed |
| Measure before optimizing | Load test, profile, and read metrics |
| Remove work from the request path | Queues and background workers |
| Cache at several layers | Browser, CDN, application (Redis), database buffers |
| Protect dependencies | Timeouts, retries with jitter, circuit breakers, bulkheads, rate limits, load shedding |

### Scaling Each Layer

| Layer | Techniques |
|---|---|
| Edge | CDN, WAF, compression, HTTP/2 or 3 |
| Application | Replicas behind a load balancer, HPA on CPU or request rate, KEDA for queue-driven scaling |
| Database | Right indexes, query optimization, connection pooling, read replicas, caching, partitioning large tables, sharding as last resort |
| Cache | Redis cluster, eviction policy, avoid stampede (TTL jitter, request coalescing) |
| Files | Pre-signed URLs so uploads and downloads bypass application servers |
| Search | Dedicated search engine fed from the database through a change pipeline |
| Async work | Queue + workers with idempotent consumers, dead letter queue |

### Background Job Boilerplate

```ts
// Producer: enqueue instead of doing slow work inline
await queue.add('send-notification-email', { userId, taskId }, { attempts: 5, backoff: { type: 'exponential', delay: 2000 } });

// Consumer (worker deployment scales independently from the API)
new Worker('send-notification-email', async (job) => {
  const already = await redis.set(`done:${job.id}`, '1', 'NX', 'EX', 86400);   // idempotency guard
  if (!already) return;
  await sendEmail(job.data);
}, { connection: redis, concurrency: 10 });
```

### Pre-Signed Upload Flow

```text
Client -> API: request upload URL (file name, type, size)
API: validate permissions and limits, create attachment record (status = pending)
API -> Client: pre-signed S3 URL (short expiry)
Client -> S3: upload directly
S3 -> Queue/Lambda: object-created event -> virus scan -> mark attachment ready
```

### Scale Milestones

| Stage | Typical Architecture |
|---|---|
| Prototype to hundreds of users | One service, one PostgreSQL, managed platform (App Service, Fargate, PaaS) |
| Thousands of users | 2-3 replicas across zones, Redis cache, CDN, background worker, monitoring |
| Tens of thousands | Autoscaling, read replica, queue-based processing, search service, careful indexing |
| Hundreds of thousands | Database partitioning, dedicated services for hot domains, multi-AZ everything, performance budgets |
| Millions and global | Multi-region active-active or active-passive, sharding, regional caches, event streaming (see earlier chapters on CAP, consistent hashing, and sharding) |

### Capacity and Load Loop

```text
Define SLOs -> load test staging with production-like data -> find the bottleneck (CPU, DB, locks, network)
-> fix or scale the bottleneck -> repeat -> set autoscaling limits and alerts -> retest before big events
```

---

## Portability and Vendor Neutrality

| Technique | Outcome |
|---|---|
| Containers (OCI images) | Same artifact on any runtime |
| Twelve-Factor configuration | No environment-specific code |
| Infrastructure as Code with modules per capability | Cloud-specific code isolated in modules |
| Open standards and engines (PostgreSQL, Redis/Valkey, S3-compatible API, OpenTelemetry, OIDC, OpenAPI) | Replaceable providers |
| Abstraction at boundaries (storage, queue, email interfaces) | Swap providers without touching business logic |
| Kubernetes as common platform | Similar deployment on EKS, AKS, GKE, or on-premises |
| Data export tooling and backups in open formats | Exit path for data |
| Local parity through Docker Compose and Dev Containers | Same behavior on laptop, CI, and cloud |

```ts
// Storage abstraction: business code depends on the interface only
export interface FileStorage {
  createUploadUrl(key: string, contentType: string): Promise<string>;
  createDownloadUrl(key: string): Promise<string>;
  delete(key: string): Promise<void>;
}
// Implementations: S3Storage, AzureBlobStorage, LocalDiskStorage (tests, dev), MinioStorage
```

Balance portability against cost: using a managed database or queue deliberately (lock-in) is often cheaper than building everything portable. Record each such decision in an ADR.

---

## Observability and Operations

### Telemetry Stack

| Signal | Tools | What to Capture |
|---|---|---|
| Logs | CloudWatch Logs, Azure Monitor, Loki, ELK/OpenSearch | Structured JSON with request ID |
| Metrics | Prometheus + Grafana, CloudWatch, Azure Monitor | RED (rate, errors, duration) for services, USE for resources, business metrics |
| Traces | OpenTelemetry + Jaeger / Tempo / X-Ray / Application Insights | End-to-end request path |
| Errors | Sentry | Exceptions with releases |
| Uptime | Synthetic checks (Pingdom, UptimeRobot, CloudWatch Synthetics) | External availability and journeys |
| Real user monitoring | Web vitals | Frontend performance |

### OpenTelemetry Boilerplate

```ts
// src/telemetry.ts — load before the application
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-http';

new NodeSDK({
  serviceName: 'taskflow-api',
  traceExporter: new OTLPTraceExporter({ url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT }),
  instrumentations: [getNodeAutoInstrumentations()],
}).start();
```

### Service Level Objectives and Alerts

| SLI | SLO | Alert |
|---|---|---|
| Availability (successful requests) | 99.9% monthly | Error-budget burn rate alerts (fast and slow burn) |
| Latency p95 | < 300 ms | p95 above target for 10 minutes |
| Queue age | < 60 seconds | Oldest message age above limit |
| Database | CPU < 70%, free storage > 20%, replication lag < 5 s | Threshold alerts |
| Certificates | Not expiring within 21 days | Expiry alert |
| Cost | Within monthly budget | Budget alert at 50/80/100% |

Alert on **user-visible symptoms**, route by severity (page vs ticket), and attach a **runbook link** to every alert.

### On-Call and Incident Management

| Practice | Details |
|---|---|
| Rotation | Weekly rotation with primary and secondary; tools: PagerDuty, Opsgenie, Grafana OnCall |
| Severity levels | Sev1 (outage), Sev2 (major degradation), Sev3 (minor) with response times |
| Roles | Incident commander, communications lead, operations lead |
| Communication | Dedicated incident channel, status page (Statuspage, Better Stack) |
| Postmortem | Blameless, within 5 working days, timeline, root cause, action items tracked to completion |
| Runbooks | Step-by-step guides in `docs/runbooks/` (database failover, rollback, certificate renewal, queue backlog, cache flush) |

```markdown
# Runbook: API error rate high
Trigger: 5xx rate > 2% for 5 minutes
1. Check deployment history: was something released in the last hour? If yes, roll back.
2. Check dependency dashboards: database CPU/connections, Redis, queue depth.
3. Check pod health: kubectl get pods, describe, logs --previous.
4. Scale out if CPU-bound: kubectl scale deploy/api --replicas=<n>
5. Escalate to the database owner if connections are exhausted.
6. Post updates every 30 minutes in #incident; open a postmortem after resolution.
```

---

## Security Hardening

| Layer | Controls |
|---|---|
| Identity | SSO and MFA for all humans, least-privilege roles, no shared accounts, regular access reviews, break-glass admin account |
| Network | Private subnets, security groups by role, WAF, DDoS protection, NetworkPolicies, egress restrictions |
| Application | Input validation, output encoding, authorization checks, rate limiting, secure headers, dependency updates |
| Data | Encryption at rest and in transit, KMS key policies, field-level encryption for highly sensitive data, retention and deletion jobs |
| Supply chain | Lockfiles, Dependabot, SBOM, signed images, protected branches, pinned CI actions, ephemeral CI credentials |
| Runtime | Non-root containers, read-only filesystems, seccomp, admission policies, runtime detection (Falco, GuardDuty) |
| Monitoring and response | Central audit logs, alerts on privilege changes and anomalies, incident response plan, regular drills |
| Assurance | Annual penetration test, vulnerability management SLA (critical within days), responsible disclosure page (`/.well-known/security.txt`) |
| Compliance | SOC 2, ISO 27001, GDPR, HIPAA, PCI as required; map controls to evidence collected automatically |

---

## Backup, Disaster Recovery and Business Continuity

| Item | TaskFlow Target |
|---|---|
| RPO | 15 minutes (PostgreSQL point-in-time recovery) |
| RTO | 1 hour (infrastructure rebuilt from Terraform, data restored from backup) |
| Strategy | Warm standby or pilot light in a second region for critical clients; backup and restore otherwise |

Practice:

- Automated database backups plus cross-region copies; object storage versioning and replication for attachments
- Infrastructure fully recreatable from Git (Terraform, Kubernetes manifests, container images in a replicated registry)
- Secrets and DNS recoverability documented; keep DNS TTL low enough for failover
- **Game days**: restore the database into a clean environment and rebuild the stack from scratch at least twice a year; record time taken

---

## Team Management and Collaboration

### Roles and Responsibilities

| Role | Responsibilities |
|---|---|
| Product owner / project manager | Priorities, scope, client communication, acceptance |
| Tech lead / architect | Architecture, standards, ADRs, code review quality |
| Backend developers | APIs, data model, background jobs |
| Frontend developers | UI, accessibility, performance |
| DevOps / SRE / platform engineer | CI/CD, infrastructure, reliability, observability |
| QA / test engineer | Test strategy, automation, release verification |
| UX / UI designer | Flows, design system, usability testing |
| Security engineer / champion | Threat modeling, reviews, scans, incident support |
| Support / customer success | Triage, feedback, documentation for users |

Small teams combine roles; the responsibilities still need named owners.

### Ways of Working

| Area | Practice |
|---|---|
| Cadence | Two-week sprints: planning, daily standup (15 minutes), review/demo, retrospective |
| Backlog | Refined weekly, prioritized by value and risk, estimates are relative |
| Work in progress | Limit WIP; finish before starting |
| Code ownership | CODEOWNERS plus rotation so knowledge is shared |
| Pairing and mobbing | For complex or risky work and onboarding |
| Technical debt | Reserve capacity (for example 15-20%) each sprint |
| Documentation | Treated as part of Done; decisions in ADRs; diagrams as code (Mermaid, PlantUML) |
| Communication | Written first, asynchronous by default, decisions recorded where the work lives |

### Environments and Access Policy

| Environment | Developers | DevOps | Production Data |
|---|---|---|---|
| Local / dev | Full | Full | No |
| Staging | Deploy through pipeline, read logs | Full | Anonymized only |
| Production | Read-only dashboards and logs, no direct database writes | Break-glass with approval and audit | Yes, tightly controlled |

Changes reach production only through the pipeline. Emergency access is time-limited, logged, and reviewed.

### Onboarding a New Developer

```bash
git clone git@github.com:acme/taskflow.git && cd taskflow
make setup          # install dependencies, copy .env.example
make up             # start database, cache, api, web via Docker Compose
make test           # verify the environment
open http://localhost:5173
```

Provide: README with architecture overview, access checklist (source control, cloud SSO, chat, tracker, monitoring), a first "good starter" ticket, and a buddy for the first two weeks. Use **Dev Containers** or a Nix/devbox setup so every machine behaves identically.

### Release Management

| Practice | Details |
|---|---|
| Versioning | Semantic Versioning; Git tags trigger production release |
| Changelog | Generated from Conventional Commits |
| Release notes | Human-readable summary for clients |
| Release schedule | Continuous for web apps, with a freeze only around critical client events |
| Hotfix | Branch from the production tag, minimal fix, fast review, deploy, then merge back to `main` |
| Change management | Risky changes announced with rollback plan and communicated maintenance windows |

### Client Management

| Activity | Practice |
|---|---|
| Expectations | Written scope, assumptions, and acceptance criteria |
| Visibility | Sprint demos, shared board, weekly status report with risks |
| Changes | Change-request process with impact on time and cost before acceptance |
| Feedback | Staging environment accessible to the client; consolidated feedback rounds |
| Sign-off | Formal acceptance at milestones and before go-live |
| Support | SLA with severity levels, response times, support hours, escalation contacts |

### Handover Package

- Source code repositories with admin rights transferred to the client organization
- Infrastructure code, architecture diagrams, ADRs, runbooks, and API documentation
- Cloud accounts owned by the client, access and billing set up in their name
- Secrets rotated and ownership documented; domain and DNS ownership confirmed
- Credentials vault entries, monitoring and alert access, on-call contacts
- Training sessions and recorded walkthroughs; maintenance and support agreement

---

## Go-Live and Operations Checklist

### Production Readiness Review

```text
Functionality
[ ] All acceptance criteria verified and signed off by the client
[ ] Critical user journeys covered by automated end-to-end tests
Quality
[ ] Unit, integration, and e2e suites green; no open blocker or critical bugs
[ ] Load test meets SLOs at 2x expected peak
[ ] Accessibility and browser checks complete
Security
[ ] Dependency, image, and IaC scans clean (or risks accepted in writing)
[ ] Secrets in a secret manager; no secrets in repositories or images
[ ] MFA enforced for admins; least-privilege roles reviewed
[ ] Penetration test findings addressed
[ ] Backups encrypted; privacy policy and terms published
Infrastructure
[ ] Everything created through IaC; state stored remotely
[ ] Multi-AZ database, tested restore from backup
[ ] Autoscaling configured with sensible limits; budgets and cost alerts set
[ ] TLS valid, HTTP redirects to HTTPS, WAF enabled
Delivery
[ ] CI/CD deploys to staging and production; rollback tested
[ ] Database migrations backward compatible
Operations
[ ] Dashboards, alerts, and on-call rota live; runbooks written
[ ] Status page and support channel ready
[ ] Incident communication plan agreed with the client
Launch
[ ] DNS TTL lowered ahead of cutover; rollback plan for DNS and release
[ ] Smoke test and monitoring review after release
[ ] Launch communication sent; hypercare period staffed
```

### Post-Launch Operations

| Area | Practice |
|---|---|
| Hypercare | Elevated monitoring and support for the first 1 to 2 weeks |
| Maintenance cadence | Monthly dependency and base image updates; quarterly cluster/runtime upgrades; track runtime end-of-life dates |
| Cost optimization | Right-size instances, use savings plans or reservations for steady load, schedule non-production environments off outside working hours, clean up unused resources, monitor data transfer and NAT costs |
| Security | Regular vulnerability management, access reviews, secret rotation, penetration tests |
| Reliability | Review SLOs monthly, run game days, act on postmortem items |
| Product | Collect analytics and user feedback, prioritize the backlog, retire unused features |
| Decommissioning | Documented process for deleting data, resources, DNS records, and access when a service or client relationship ends |

### Summary of the Complete Flow

```text
Understand the client and constraints  ->  Choose a stack and write ADRs
-> Install the toolchain on the empty machine  ->  Git repository with protections and conventions
-> Scaffold API + web + database locally with Docker Compose  ->  Build features with tests first
-> Authentication, authorization, security baseline  ->  Containerize and scan images
-> Define cloud infrastructure as code  ->  CI pipeline: lint, test, scan, build, push
-> Deploy to Kubernetes (or a simpler managed platform) through GitOps  ->  Domain, DNS, TLS, CDN
-> Observability, alerts, runbooks  ->  UAT, load, and security testing  ->  Go-live checklist
-> Operate, scale, optimize, and hand over  ->  Iterate
```

---

# Case Studies: Designing Popular Systems

## Designing YouTube

### Requirements
- Users can upload, store, and stream videos
- Support search, recommendations, comments, and likes
- Handle massive scale of concurrent viewers

### Process
- **Upload Flow**: Client uploads raw video to an object store (e.g., S3) via a resumable upload API. Metadata (title, description, owner) is saved in a database.
- **Video Processing**: A transcoding pipeline (queue-based, using workers) converts the raw video into multiple resolutions and formats (adaptive bitrate streaming via HLS/DASH).
- **Storage**: Processed video chunks are stored in blob storage and distributed via a CDN for low-latency delivery.
- **Streaming**: Clients fetch video manifests and stream chunks progressively, with adaptive bitrate switching based on network conditions.
- **Metadata Database**: Use a scalable NoSQL/SQL hybrid store for video metadata, view counts, and comments.
- **Search & Recommendations**: Use an inverted index (e.g., Elasticsearch) for search, and a separate recommendation service using collaborative filtering or ML models.
- **Scaling**: Horizontal scaling of upload/transcoding workers, CDN caching for popular videos, and read replicas for metadata queries.

## Designing Netflix

### Requirements
- Stream video content reliably to millions of users globally
- Personalized recommendations
- High availability with minimal buffering

### Process
- **Content Ingestion**: Licensed content is uploaded, transcoded into multiple resolutions/bitrates, and encrypted with DRM.
- **Storage & Distribution**: Encoded content is pushed to CDN edge servers (Netflix uses its own CDN — Open Connect) placed close to ISPs to reduce latency.
- **Playback**: Client requests the nearest edge server; adaptive streaming adjusts quality based on bandwidth in real time.
- **Microservices Architecture**: Independent services handle user profiles, billing, recommendations, and playback, communicating via APIs and message queues.
- **Recommendation Engine**: Uses viewing history and ML models, computed offline and served through a fast key-value store.
- **Resilience**: Circuit breakers (e.g., Hystrix) prevent cascading failures; chaos engineering (Chaos Monkey) tests fault tolerance proactively.
- **Scaling**: Global multi-region deployment with active-active data replication for high availability.

## Designing Facebook (Social Media Feed)

### Requirements
- Users can post, like, comment, and follow/friend others
- Real-time news feed generation
- Massive read-heavy traffic

### Process
- **Post Creation**: Write goes to a primary database and is queued for feed fan-out processing.
- **Feed Generation Approaches**:
  - **Fan-out on Write** — feed entries are pre-computed and pushed to each follower's feed store at post time (fast reads, costly for users with huge follower counts).
  - **Fan-out on Read** — feed is generated dynamically when a user requests it (cheaper writes, slower reads).
  - **Hybrid Approach** — fan-out on write for normal users, fan-out on read for celebrities/high-follower accounts.
- **Storage**: Graph database or adjacency-list model for friend/follow relationships; sharded databases for posts.
- **Caching**: Heavy use of distributed caches (e.g., Memcached) for feeds, profiles, and friend lists.
- **Notifications**: Asynchronous event-driven system using message queues to notify users of likes, comments, and tags.
- **Scaling**: Horizontal sharding by user ID, CDN for media content, and read replicas for feed queries.

## Designing an Online Discussion Forum (e.g., Reddit-style)

### Requirements
- Users can create posts/threads, comment, upvote/downvote
- Support nested comment threads and sorting (top, new, hot)
- Handle high read traffic with moderate write traffic

### Process
- **Post & Comment Storage**: Use a relational or document database with a parent-child relationship model for nested comments.
- **Voting System**: Store vote counts in a fast key-value store (e.g., Redis) and periodically sync aggregated counts to the main database to avoid write contention.
- **Ranking Algorithm**: Compute "hot" or "top" scores using a formula combining vote count and time decay, recalculated periodically or on-read.
- **Caching**: Cache frequently accessed threads and comment trees to reduce database load.
- **Search**: Use an inverted index (e.g., Elasticsearch) for searching posts and comments.
- **Moderation**: Event-driven pipeline for spam detection and rule-based or ML-based content moderation.
- **Scaling**: Partition data by community/subforum, use read replicas for heavy read traffic, and CDN for static/media content.

## Designing a Chat System

### Requirements

- One-to-one messaging
- Group messaging
- Online/offline presence
- Message delivery
- Message history
- Read receipts
- Push notifications

### Architecture

Client
↓
Load Balancer
↓
WebSocket Servers
↓
Message Service
↓
Message Queue
↓
Message Storage

### Key Challenges

- Maintaining millions of persistent connections
- Routing messages to the correct connection
- Offline message delivery
- Message ordering
- Duplicate messages
- Presence tracking

### Important Concepts

- WebSockets
- Connection routing
- Message queues
- Idempotency
- Partitioning
- Presence service
- Push notifications

## Designing a Payment System

### Requirements

- Create payment
- Prevent duplicate charges
- Track payment state
- Handle retries
- Support refunds
- Maintain audit history

### Architecture

Client
↓
API Gateway
↓
Payment Service
↓
Payment Database
↓
Payment Provider

Use:
- Idempotency keys
- Transactions
- State machines
- Retry policies
- Webhooks
- Audit logs

### Payment State Machine

Created
→ Processing
→ Succeeded

or

Created
→ Processing
→ Failed

or

Succeeded
→ Refunded

## Designing a Notification System

### Requirements

- Email
- SMS
- Push notifications
- User preferences
- Retry failed delivery
- High throughput

### Architecture

Application
↓
Notification API
↓
Message Queue
↓
Notification Workers
↓
Email/SMS/Push Providers

Use asynchronous processing so the main application does not wait for external notification providers.

## Designing a URL Shortener

### Requirements

- Create short URL
- Redirect to original URL
- High read traffic
- Low latency
- Analytics

### Architecture

Client
↓
Load Balancer
↓
URL Service
↓
Cache
↓
Database

Use:
- Base62 IDs
- Distributed cache
- Database replication
- Horizontal scaling

## Designing a Rate Limiter

### Requirements

- Limit requests per user/IP/API key
- Distributed deployment
- Low latency
- Accurate enough under concurrency

### Algorithms

- Token Bucket
- Leaky Bucket
- Fixed Window
- Sliding Window

### Distributed Implementation

Client
↓
API Gateway
↓
Rate Limiter
↓
Distributed Redis
↓
Backend

Important considerations:
- Atomic increments
- Expiration
- Race conditions
- Distributed consistency

---

# System Design Trade-offs

There is rarely a universally correct architecture.

## SQL vs NoSQL

SQL:
- Strong relational modeling
- Transactions
- Structured schema

NoSQL:
- Flexible schema
- Horizontal scaling
- High-scale access patterns

## Synchronous vs Asynchronous

Synchronous:
- Simple request/response
- Immediate result
- Tighter coupling

Asynchronous:
- Better decoupling
- Better resilience
- Higher complexity
- Eventual consistency

## Cache vs Database

Cache:
- Faster
- Temporary
- Potentially stale

Database:
- Source of truth
- Durable
- Usually slower

## Monolith vs Microservices

Monolith:
- Simpler
- Easier to operate initially
- Harder to independently scale

Microservices:
- Independent scaling/deployment
- Better team/service isolation
- More operational complexity

## Consistency vs Availability

Strong consistency:
- More predictable reads
- More coordination

Eventual consistency:
- Better availability/scalability in many distributed systems
- Temporary stale reads

## Read Optimization vs Write Optimization

Read-heavy systems:
- Caching
- Read replicas
- Denormalization
- Materialized views

Write-heavy systems:
- Partitioning
- Batching
- Asynchronous processing
- Log-based architectures

---

# Scenario-Based Questions and Answers

### Q: How would you design a URL shortener like Bit.ly?
**A:** Use a hash function (e.g., base62 encoding of an auto-incrementing ID) to generate short codes. Store the mapping of short code to original URL in a key-value database for fast lookups. Add a caching layer (Redis) for frequently accessed URLs, and use a CDN plus horizontal scaling with load balancers to handle high read traffic.

### Q: How would you design a system to handle millions of concurrent users on a chat application?
**A:** Use WebSockets for persistent real-time connections, a message queue (e.g., Kafka) to handle message delivery asynchronously, and a distributed database for storing chat history. Use horizontal scaling with sticky sessions or a connection-routing layer, and a presence service to track online/offline status.

### Q: Your database is experiencing high read load. How do you fix it without changing hardware?
**A:** Introduce a caching layer (Redis/Memcached) for frequently read data, add read replicas to distribute read queries, and consider denormalizing data or using materialized views for expensive queries.

### Q: How would you ensure no duplicate payment is processed if a client retries a failed request?
**A:** Implement idempotency keys — the client sends a unique key with each payment request, and the server checks if that key was already processed before executing the transaction again.

### Q: How would you design a system that must remain available even if an entire data center goes down?
**A:** Deploy the system across multiple geographically distributed data centers with active-active replication, use a global load balancer with health checks to route traffic away from the failed region, and ensure data is asynchronously replicated across regions with conflict resolution strategies for eventual consistency.

### Q: How do you prevent a single misbehaving client from overwhelming your API?
**A:** Apply rate limiting at the API Gateway level (e.g., token bucket algorithm) per client/IP/API key, return HTTP 429 responses when limits are exceeded, and use circuit breakers to protect downstream services from cascading failures.

### Q: How would you design a system for real-time analytics on streaming data (e.g., tracking live user activity)?
**A:** Ingest events through a message queue like Kafka, process the stream using a stream-processing engine (e.g., Apache Flink or Spark Streaming), store aggregated results in a fast-read database (e.g., Cassandra or a time-series DB), and expose results through a dashboard with periodic or real-time updates.
