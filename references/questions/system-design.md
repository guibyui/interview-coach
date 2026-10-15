# System Design Questions

## Contents
- Expected shape of a strong answer
- Questions with key points
- Follow-up probes

## Expected shape of a strong answer
1. **Clarify scope first.** Scale (users, requests, data volume, spikes), read/write ratio, latency needs, consistency needs, which features are in scope, and who the users are. Ask one clean question at a time rather than a compound question.
2. **Name the major components out loud.** Client, API, storage, queue, workers, cache, external providers.
3. **State one trade-off per component**, not just the choice.
4. **Address failure.** Retries with a cap, idempotency, dead-letter or human escalation, monitoring.
5. **Close with how it scales** and what you would build first versus later.

If the candidate dives straight into products or vendors, pull them back to architecture first.

## Questions

### 1. Design a notification system (email, SMS, push)
Good clarifications: volume, transactional versus marketing, latency expectations, user preferences and opt-out.
Key points:
- A notification service that decides which channels to use, behind a **queue** so mass sends do not block the caller.
- **Channel-specific workers or adapters**, so providers are swappable and failures are isolated.
- A **user preferences store** for opt-in and opt-out, and compliance for unsubscribe.
- **Different reliability per type:** transactional (password reset, order confirmation) must not be lost, so retries and monitoring matter; marketing or social notifications can be fire-and-forget. SMS costs more, so retry budgets differ by channel.
- **Retries with a cap**, and what happens after the cap (log, alert, escalate).
- **Idempotency:** a unique ID per notification, with check-and-mark done atomically (for example a conditional write or Redis SET with NX) so a retry after a timeout does not send a duplicate.
- Delivery status tracking and bounce handling.
Trade-off to elicit: retries give accuracy but add delay and duplicate risk; fire-and-forget gives speed but accepts loss.

### 2. Design a rate limiter for a public API
Good clarifications: per user, per API key, per IP, or global; scale; one node or distributed; what to do when a limit is hit.
Key points:
- Components: a counter, a fast shared store (such as Redis), and a decision point (middleware or gateway) that allows or rejects with a 429 and retry hints.
- Algorithm trade-offs: **fixed window** is simple but allows boundary bursts (double the limit around the window edge); **sliding window** is smoother but costs more memory or computation; **token bucket** allows controlled bursts and is a common default; leaky bucket smooths output.
- Distributed concerns: atomic increments, race conditions across servers, clock skew.
- Fail open or fail closed if the limiter store is down.

### 3. Design a file upload system (profile pictures and documents up to ~20MB, possibly with processing)
Key points:
- Store files in **object storage** (S3 or equivalent), and keep only metadata (path, owner, size, type, status) in the database.
- **Pre-signed URL pattern:** the API authorizes the upload and returns a short-lived signed URL; the browser uploads straight to storage, so large uploads do not tie up API servers.
- Validate type and size on both client and server; scan for malware if required; process asynchronously (a queue and workers for virus scanning, resizing, thumbnails) and mark files "pending" until processing finishes.
- Serve through a CDN; use private buckets with signed download URLs for sensitive documents.
- Failure cases: abandoned uploads (lifecycle rules to clean up), retries, resumable or multipart uploads for big files.

### 4. Design a search feature over millions of records
Key points:
- Start with what a plain database can do: indexes, pagination (cursor-based scales better than large offsets), caching of popular queries.
- Past that, fuzzy matching, typo tolerance, ranking, and full-text relevance need a **dedicated search index** (Elasticsearch, OpenSearch, Algolia, or Postgres full-text for smaller scale).
- The real trade-off: keeping the search index in sync with the primary database (event-driven updates or change data capture), accepting eventual consistency, and having a reindex path.
- Naming a third-party tool is fine in a senior interview as long as the trade-offs are explained.

### 5. Design comments, replies, and reactions for a platform with millions of posts
Key points:
- Comment table with a **parent_id** (null for top-level) for threading.
- Reaction counts kept in a **counter field** (or cached counter) rather than counting rows on every view; reactions themselves stored per user to prevent duplicates.
- Pagination for comments and for replies; load replies lazily.
- Deep threads make recursive queries expensive, so cap nesting depth in the UI (flatten after 2 or 3 levels) even if the data supports more; consider a materialized path or closure table if deep queries matter.
- Real-time updates: polling versus WebSockets (see the scalability file); scale WebSocket servers with rooms/channels per post and a pub/sub backplane such as Redis so every server can forward messages to its local subscribers.

### 6. Design a URL shortener handling millions of requests a day
Key points: key generation (counter with base62 encoding, hashing with collision handling, or pre-generated keys), read-heavy traffic (cache hot keys, CDN, 301 versus 302 redirects and their analytics implications), storage choice, expiration, abuse prevention, analytics collected asynchronously.

### 7. Design a leaderboard that updates in real time for millions of users
Key points: Redis sorted sets for ranking and range queries, write volume and sharding strategy, ties, approximate versus exact ranks at huge scale, periodic snapshots to durable storage, per-region or per-season boards.

### 8. Design a pipeline for uploading and processing large videos (thumbnails and multiple resolutions)
Good clarifications: max file size, formats, how fast results are needed, who views, A/B testing of thumbnails.
Key points: direct-to-storage upload (pre-signed or resumable), a queue and workers for transcoding into multiple renditions, thumbnail generation, status tracking and notifications, retries per rendition, idempotent jobs, CDN delivery with adaptive bitrate streaming, cost control (spot instances, only transcode renditions that are needed).

### 9. Design a durable multi-step workflow (for example, verifying a record against several external sources, where some steps take days)
Key points: a **workflow engine** (Temporal, Step Functions, or equivalent) or a persisted state machine; every step's completion is recorded so a crash resumes from the last completed step, not from the beginning; retries and timeouts per step; idempotent steps; human-in-the-loop steps; visibility and audit trail. Contrast with a plain queue and retries, which handles single jobs well but does not remember position across a long chain of dependent steps.

### 10. Design a system where an LLM reads documents and flags issues for human review
Key points: chunk the document and track progress per chunk so a failure only retries that chunk; validate model output against a schema and treat malformed output as a retryable error with a cap; escalate to a human after the cap; guard against confident but wrong answers with a second pass or second model and compare, or with confidence scores that prioritize human review; keep a human in the loop for high-stakes decisions; log and trace everything for audit.

## Follow-up probes
- What happens if a worker crashes halfway through?
- What if the same job is processed twice?
- How would you know something silently went wrong?
- What changes at 10 times the volume? 100 times?
- What is the first thing you would build, and what would you defer?
- Where would you put the cache and how would you invalidate it?
