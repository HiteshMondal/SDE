# System Design Guide

A concise reference covering the core concepts used when designing large-scale, distributed systems.

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

---

# Content Delivery Network (CDN)

A geographically distributed network of servers that caches static content (images, videos, JS/CSS) closer to users.

- Reduces latency
- Reduces load on origin servers
- Push CDN vs Pull CDN

---

# Proxies

## Forward Proxy
Sits between client and internet; hides client identity, used for filtering/access control.

## Reverse Proxy
Sits between clients and backend servers; used for load balancing, SSL termination, and caching.

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

---

# API Gateway

A single entry point for client requests that routes them to appropriate backend services.

### Responsibilities
- Authentication and authorization
- Rate limiting
- Request routing
- Response aggregation

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

---

# Rate Limiting

Controls the number of requests a client can make in a given time window to protect systems from abuse or overload.

### Common Algorithms
- Token Bucket
- Leaky Bucket
- Fixed Window Counter
- Sliding Window Log

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

# Security Basics

- Authentication vs Authorization
- HTTPS/TLS for data in transit
- Encryption at rest for sensitive data
- Input validation and rate limiting to prevent abuse

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
