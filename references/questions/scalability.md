# Scalability and Performance Questions

## Expected shape of a strong answer
**Multiple levers, not one.** Broad scaling and performance questions collapse into "add an index" or "add a cache." Push for at least three or four, and for the trade-off of each:
- **Measure first** (profiling, metrics, tracing, slow query logs) so effort goes to the real bottleneck.
- **Caching** (in-memory, Redis, CDN, HTTP caching) and what to cache: read-heavy, rarely changing data such as product details, pricing rules, shipping zones. Trade-off: staleness and invalidation.
- **Horizontal scaling** of stateless app servers behind a load balancer. Trade-off: statelessness requirements, session handling.
- **Vertical scaling:** simple and quick, but a ceiling and a single point of failure.
- **Database:** indexing, query optimization, read replicas (offload reads, but replication lag), connection pooling, partitioning and sharding (powerful but complex).
- **Asynchronous work:** queues and workers for anything that does not need to finish inside the request; scale workers independently.
- **Rate limiting, backpressure, and graceful degradation** for overload.

## Questions

### 1. How would you scale this application if traffic grew ten times overnight?
First ask what kind of application and where the load lands (page views versus database writes). Then walk the layers above. A strong candidate also separates **legitimate growth** from **an attack**: for a spike from an attack, use a CDN or WAF with an "under attack" mode, block or rate-limit abusive IPs, and tell the host; for legitimate growth, add capacity ahead of time with the host provider and scale out. Ask what they would do in the first hour versus the first month.

### 2. Walk me through the levers you would pull to make a slow API endpoint faster.
Key points: measure; look at the query before guessing; **EXPLAIN / query plan** to spot full table scans; add the right index (and its write-time cost); fix N+1 queries; select only needed columns; paginate (cursor-based beats large offsets); cache results; move work off the request path; denormalize for read-heavy paths, accepting the update complexity; compress and reduce payload size.

### 3. A query is slow. How do you diagnose it?
Key points: do not guess fixes. Reproduce, run EXPLAIN (ANALYZE where safe) and read the plan: sequential scan versus index scan, row estimates versus actual, join order, sorts spilling to disk. If already indexed, look at the structure of the query (joins, subqueries, CTEs, functions applied to indexed columns that block index use, SELECT *). Then consider pagination, caching, and denormalization.

### 4. Vertical versus horizontal scaling: when would you choose one over the other?
Key points: vertical is simple and fine early, with no code change, but has a hardware ceiling and a single point of failure; horizontal needs stateless services or shared state, load balancing, and handles failure better; databases are harder to scale horizontally than stateless app servers.

### 5. How do you decide what to cache, and where?
Key points: cache data that is read often, changes rarely, and is expensive to compute; levels: browser, CDN, application memory, shared cache such as Redis, database query cache; invalidation strategies (TTL, event-based, write-through); risks: stale data, cache stampede (mitigate with locking or request coalescing), cold-start load; never cache user-specific sensitive data in shared layers by accident.

### 6. What is a read replica, and when does it help versus add complexity?
Key points: a copy of the primary that serves reads; helps read-heavy workloads and reporting; complexity: replication lag (stale reads, read-your-own-writes problems), failover handling, routing logic; does not help write-heavy bottlenecks.

### 7. How would you handle a database that becomes a bottleneck as write volume grows?
Key points: first confirm the cause (locks, missing indexes, connection limits, slow disk); batch writes; queue writes and apply asynchronously where acceptable; reduce write amplification (fewer indexes, narrower rows); partition tables; shard by a good key (and the cost of cross-shard queries); move append-heavy or time-series data to a better-suited store; consider whether strong consistency is truly required everywhere.

### 8. What is a load balancer and how do you use one?
Key points: distributes traffic across identical instances; algorithms (round robin, least connections); health checks; sticky sessions versus stateless design; in practice use a managed one from the cloud provider; TLS termination; it can itself be a single point of failure unless redundant.

### 9. Polling versus WebSockets versus server-sent events: how do you choose?
Key points: the deciding question is how time-sensitive the data is versus the cost of holding connections open. Rule of thumb: if a two to three second delay makes the feature feel broken, lean on WebSockets (live chat, multiplayer games, collaborative editing, ride tracking, trading); if ten to sixty seconds is fine, lean on polling (order status pages, analytics dashboards), which is simpler and cheaper. Server-sent events fit one-way server-to-client streams. Large sites often poll for counters (views, likes) because the cost of persistent connections for everyone is not worth it. Scaling WebSockets: connections grouped into rooms or channels by what the user is viewing; multiple servers need a shared pub/sub layer (such as Redis) so a message published on one server reaches subscribers connected to others; each server forwards only to its local subscribers.

### 10. How do you design for failure at scale?
Key points: timeouts, retries with exponential backoff and jitter, circuit breakers, bulkheads, idempotency, graceful degradation, load shedding, health checks, redundancy across zones, backups and restore drills, and observability (metrics, logs, traces, alerts).

## Follow-up probes
- Where is the single point of failure?
- What breaks first?
- How would you test that it scales?
- What does this cost, and how would you control it?
- What would you monitor to know you are in trouble before users tell you?
