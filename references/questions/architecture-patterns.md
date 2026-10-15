# Architecture Patterns and Trade-offs Questions

## Expected shape of a strong answer
Define the concept in a sentence, give a concrete example, then state **when it helps and when it adds unnecessary complexity**. The trade-off is the point. Ground in a real project where possible.

## Questions

### 1. Monolith versus microservices: when would you choose one over the other?
Key points: a monolith (ideally modular) is simpler to build, test, deploy, and debug; best for small teams and early products. Microservices allow independent deployment and scaling and team autonomy, but add network failures, distributed transactions, versioning, observability needs, and operational cost. Choose microservices for organizational scale or very different scaling profiles, not for fashion. A modular monolith is a common middle path.

### 2. What is event-driven architecture, and when does it help versus add unnecessary complexity?
Key points: components communicate by publishing and reacting to events; decouples producers from consumers, supports async work and fan-out, makes adding consumers easy. Costs: harder to trace and debug, eventual consistency, ordering and duplication issues, schema evolution of events. Overkill when a simple synchronous call would do.

### 3. Explain the adapter pattern (or strategy pattern) and give a real example.
Key points: **Adapter** wraps an incompatible interface so the rest of the system sees a consistent one (for example, normalizing events from different payment providers behind one interface). **Strategy** swaps interchangeable algorithms behind a common interface chosen at runtime (for example, different discount or shipping rules). Benefit: the rest of the system does not know which implementation is active, so changes stay local. Cost: an extra layer that is wasted if there will only ever be one implementation.

### 4. What is the difference between a message queue and a pub/sub system?
Key points: a **queue** delivers each message to one consumer (work distribution; competing workers); **pub/sub** delivers each message to every subscriber (broadcast). Event streams (such as Kafka) are a durable, replayable log where consumer groups track their own position, combining aspects of both. Choose by whether the work should be done once or whether many parties need to react, and whether replay is needed.

### 5. What is idempotency and why does it matter in distributed systems?
Key points: an operation can be performed multiple times with the same effect as once. Needed because networks and workers fail in ways that cause retries (a timeout does not mean the operation failed). Implementation: idempotency keys or unique IDs, check-then-record done **atomically** (unique constraint, conditional write, SET NX) so concurrent retries cannot both pass; natural idempotency for operations like "set status to shipped". Critical for payments, webhooks, and notifications.

### 6. Explain eventual consistency versus strong consistency, with examples of where each is acceptable.
Key points: strong consistency means every read sees the latest write (needed for account balances, inventory reservations, payments); eventual consistency means replicas converge over time (acceptable for like counts, feeds, search indexes, analytics). Mention the CAP trade-off in plain terms: during a network partition you choose between consistency and availability.

### 7. How would you design an API that can support multiple providers or data sources in the future without a full rewrite?
Key points: define your own domain interface and normalized events; write one adapter per provider; keep provider-specific details out of core logic; contract tests per adapter; configuration to select the active provider; avoid leaking provider IDs and error types upward.

### 8. REST versus GraphQL (and gRPC): how do you choose?
Key points: REST is simple, cacheable, widely understood; GraphQL lets clients request exactly the fields they need and reduces over- and under-fetching, at the cost of caching complexity, N+1 resolver problems, query cost control, and more server complexity; gRPC suits internal service-to-service calls with strict contracts and performance needs. Choose by clients, team familiarity, and caching needs.

### 9. How do you handle multi-tenancy in a SaaS product?
Key points: models: shared database with a tenant ID column on every row, schema per tenant, or database per tenant; trade-offs between isolation, cost, operational overhead, and noisy neighbors; enforce isolation centrally (row-level security, scoped repositories, tenant context in every query) rather than trusting every developer; encryption keys per tenant for sensitive data; per-tenant rate limits; testing for cross-tenant leaks.

### 10. What is a durable workflow, and when would you use a workflow engine instead of a queue with retries?
Key points: a durable workflow records the completion of every step, so after a crash it resumes from the last completed step instead of restarting; it models long-running, multi-step, dependent processes (hours or days), with timers, retries per step, human approval steps, and an audit history. A queue with retries is fine for independent single jobs; it does not remember overall position across a chain. Workflow engines have a learning curve (deterministic workflow code, separating orchestration from side effects in "activities").

### 11. How do you authenticate and authorize users in a web application?
Key points: authentication proves identity, authorization decides permissions; sessions with secure cookies versus tokens such as JWTs (trade-offs: revocation, size, storage); OAuth 2.0 and OpenID Connect for delegated login; role-based or attribute-based access control enforced server-side; least privilege; never trust the client.

### 12. How do you handle secrets and sensitive data?
Key points: secrets in a manager or environment, never in source control; rotate them; encrypt in transit (TLS) and at rest; envelope encryption with data keys wrapped by a key management service; for data that must be searchable yet encrypted, store a keyed hash (blind index) for lookups and the ciphertext for display; minimize what you store; audit access.

## Follow-up probes
- What would make you reverse this decision?
- What is the migration path from where you are to there?
- What does this cost the team operationally?
- How would you test the boundary between the two sides?
