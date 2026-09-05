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
