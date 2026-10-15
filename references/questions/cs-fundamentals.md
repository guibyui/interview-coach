# Computer Science Fundamentals Questions

## Contents
- How to use this file
- A. Complexity and Big-O
- B. Data structures
- C. Algorithms
- D. "Talk it through" problems
- E. Concurrency
- F. Memory and runtime
- G. Operating system basics
- H. Networking and the web
- I. Databases
- J. Security basics
- K. Engineering practice

## How to use this file
Pick a section the candidate chose (or rotate). Ask one question, run the normal loop, and use the key points to judge accuracy. For this topic, **precision matters**: a vague or slightly wrong definition is what interviewers mark down. Ask for a concrete example and the cost (time, space, or operational) in every answer.

A strong answer here: defines the concept in one or two sentences, gives the complexity or cost, gives a realistic use case, and mentions a limitation or when you would choose something else.

For "talk it through" problems, the process is also graded: clarify the input and constraints, state a brute-force approach and its cost, improve it, test with a small example and edge cases, and state final time and space complexity. Candidates should speak their reasoning out loud.

---

## A. Complexity and Big-O

1. What is Big-O notation, and why do we use it?
   - Describes how running time or memory grows as input size grows, ignoring constants and lower-order terms. Usually discussed for the worst case; best, average, and amortized cases exist too.
2. What is the difference between time and space complexity? Give an example of a trade-off between them.
   - Example: a hash set uses O(n) extra space to turn an O(n²) duplicate check into O(n); memoization trades memory for time.
3. What does amortized O(1) mean? Give an example.
   - Appending to a dynamic array: occasional O(n) resizes are spread across many cheap appends, so the average cost per append is O(1).
4. Rank O(1), O(log n), O(n), O(n log n), O(n²), and O(2ⁿ). Give an example of each.
   - O(1) hash lookup; O(log n) binary search; O(n) linear scan; O(n log n) efficient sorting; O(n²) nested loops over the same input; O(2ⁿ) naive recursive subset enumeration or naive Fibonacci.
5. When might an O(n²) algorithm beat an O(n log n) one in practice?
   - Small n, smaller constants, better cache behavior; insertion sort is used for tiny arrays inside real sort implementations. Big-O is about growth rate, not absolute speed.
6. What is the complexity of this code? (Show nested loops, a loop that halves the input, or a loop calling a sort.) Look for correct reasoning, not memorized answers.

## B. Data structures

1. Array versus linked list: compare access, insertion, deletion, and memory behavior.
   - Array: O(1) index access, O(n) insert or delete in the middle, contiguous memory with good cache locality. Linked list: O(n) access, O(1) insert or delete once you hold the node, pointer overhead, poor cache locality. In practice, dynamic arrays win more often than candidates expect.
2. How does a hash table work? What happens on collisions?
   - A hash function maps a key to a bucket index. Average O(1) insert, lookup, delete; worst case O(n) when many keys collide (some implementations convert long buckets to balanced trees). Collision handling: chaining (a list per bucket) or open addressing (probe for the next free slot). Load factor triggers resizing and rehashing. Keys must be hashable and, in practice, effectively immutable. Iteration order is not guaranteed in the general case.
3. Hash map versus balanced binary search tree: when would you choose each?
   - Hash map for fastest average lookup by exact key. Balanced BST (red-black, AVL) for ordered iteration, range queries, min/max, and floor/ceiling operations, with guaranteed O(log n).
4. What is a binary search tree and what goes wrong if it is unbalanced?
   - Left subtree values smaller, right larger; operations are O(height). Inserting sorted data into a naive BST creates a linked-list-shaped tree with O(n) operations. Self-balancing trees keep height O(log n).
5. What is a stack, and what is a queue? Give real uses.
   - Stack is LIFO: call stack, undo history, expression parsing, depth-first search. Queue is FIFO: task scheduling, breadth-first search, buffering, message queues. A deque supports both ends.
6. What is a heap or priority queue, and where is it used?
   - Binary heap gives O(1) peek of min or max, O(log n) insert and remove; building from n elements is O(n). Uses: scheduling, top-k problems, Dijkstra's algorithm, merging sorted streams.
7. What is a trie, and when is it useful?
   - A tree keyed by characters of a string; lookups cost O(length of the key). Good for prefix search and autocomplete; trade-off is memory.
8. Represent a graph: adjacency list versus adjacency matrix.
   - List: space O(V+E), good for sparse graphs and iterating neighbors. Matrix: space O(V²), O(1) edge check, good for dense graphs.
9. Design an LRU cache with O(1) get and put.
   - Hash map from key to node, plus a doubly linked list ordered by recency; on access move the node to the front; on overflow evict from the tail.
10. How would you find the top k most frequent items in a large stream?
    - Count with a hash map, then a min-heap of size k for O(n log k), or bucket sort by frequency for O(n). For massive streams, approximate structures such as count-min sketch.
11. What is a Bloom filter?
    - A compact probabilistic set that can say "definitely not present" or "possibly present" (false positives, no false negatives). Used to avoid expensive lookups.
12. What is the difference between a set, a list, and a map? When do you reach for each?

## C. Algorithms

1. Compare merge sort, quicksort, and heap sort.
   - Merge sort: O(n log n) always, stable, O(n) extra space. Quicksort: O(n log n) average, O(n²) worst case (mitigated by random or median-of-three pivots), in place, typically not stable, fast in practice. Heap sort: O(n log n), in place, not stable, poorer cache behavior. Many standard libraries use hybrids (for example Timsort, which is stable and exploits existing order).
2. What does it mean for a sort to be stable, and why would you care?
   - Equal keys keep their relative order. Matters when sorting by multiple keys in passes (sort by name, then by department).
3. Explain binary search. What are the prerequisites and common bugs?
   - Requires sorted data (or a monotonic predicate); O(log n). Bugs: off-by-one on boundaries, infinite loops, integer overflow computing the midpoint in fixed-width languages, handling duplicates.
4. What is recursion, and what are its risks?
   - A function calling itself with a smaller problem, with a base case. Risks: missing base case, stack overflow on deep recursion, repeated work. Fix with iteration, an explicit stack, tail-call optimization where supported, or memoization.
5. Explain dynamic programming. How do you know a problem is a candidate?
   - Overlapping subproblems and optimal substructure. Memoization (top-down) caches results of recursive calls; tabulation (bottom-up) fills a table. Examples: Fibonacci, coin change, knapsack, longest common subsequence.
6. What is a greedy algorithm, and when does it work?
   - Makes the locally best choice at each step; correct only when the problem has the greedy-choice property (for example interval scheduling, Huffman coding, Dijkstra with non-negative weights). Counterexample: greedy coin change for arbitrary denominations.
7. BFS versus DFS: when to use each?
   - BFS explores level by level with a queue and finds shortest paths in unweighted graphs. DFS goes deep with a stack or recursion; suited to cycle detection, topological sort, connected components, and backtracking.
8. How do you find the shortest path in a weighted graph?
   - Dijkstra (non-negative weights, O((V+E) log V) with a heap); Bellman-Ford for negative edges; A* with a heuristic for pathfinding.
9. What is topological sorting and where is it used?
   - Ordering nodes of a directed acyclic graph so every dependency precedes its dependents: build systems, task scheduling, package installation. A cycle means no valid order.
10. What is divide and conquer? Name examples. (Merge sort, quicksort, binary search, fast exponentiation.)
11. What is backtracking? (Incrementally build a solution and abandon a path as soon as it violates constraints: N-queens, permutations, sudoku.)

## D. "Talk it through" problems

Ask the candidate to describe the approach out loud, with complexity. Writing code is optional unless they want it. Use the process described at the top of this file. Expected approaches:

1. **Two sum:** brute force O(n²); hash map of value to index gives O(n) time and O(n) space.
2. **Valid parentheses:** stack; push openers, pop and match on closers; valid if the stack is empty at the end. O(n).
3. **Reverse a linked list:** iterate with previous, current, next pointers; O(n) time, O(1) space.
4. **Detect a cycle in a linked list:** Floyd's two pointers (slow and fast); O(n) time, O(1) space. Alternative: a visited set at O(n) space.
5. **Merge two sorted lists or arrays:** two pointers; O(n+m).
6. **Find duplicates in an array:** hash set O(n); or sort first for O(n log n) with O(1) extra space.
7. **First non-repeating character in a string:** count frequencies, then scan; O(n).
8. **Group anagrams:** key by sorted characters or by a character-count signature; hash map of key to list.
9. **Number of islands in a grid:** BFS or DFS flood fill, marking visited cells; O(rows × cols).
10. **Longest substring without repeating characters:** sliding window with a hash map of last-seen positions; O(n).
11. **Check if a string is a palindrome:** two pointers from both ends; handle case and punctuation per the clarified rules.
12. **Merge overlapping intervals:** sort by start, then sweep and merge; O(n log n).
13. **Design a data structure with O(1) insert, delete, and get-random:** array plus hash map from value to index; swap with the last element on delete.

Coaching: if they jump to code, ask for the brute force first. If they stay silent, prompt them to think aloud. Always ask for edge cases (empty input, single element, duplicates, very large input, negative numbers).

## E. Concurrency

1. Process versus thread.
   - A process has its own memory space; threads within a process share memory. Threads are lighter to create and communicate, but sharing memory causes data races; processes give isolation at higher cost.
2. Concurrency versus parallelism.
   - Concurrency is structuring work so multiple tasks make progress in overlapping time (possibly on one core); parallelism is literally running at the same instant on multiple cores.
3. What is a race condition? Give an example and fixes.
   - Outcome depends on the timing of unsynchronized access to shared state (two requests read a balance, both subtract, one update is lost). Fixes: locks or mutexes, atomic operations, transactions, optimistic concurrency control (version columns), immutability, avoiding shared state, queues that serialize access.
4. What is a deadlock? What conditions cause it, and how do you prevent it?
   - Two or more tasks wait on each other forever. Four conditions together: mutual exclusion, hold and wait, no preemption, circular wait. Prevention: always acquire locks in a consistent order, use timeouts, hold fewer locks for less time, use lock-free or higher-level structures. Related: livelock and starvation.
5. Mutex versus semaphore.
   - A mutex allows one holder at a time (ownership matters); a semaphore permits up to N concurrent holders and can be used for signaling or limiting concurrency (for example a connection pool).
6. What does "thread-safe" mean?
   - Behaves correctly when used from multiple threads without extra coordination by the caller. Achieved via synchronization, immutability, or thread confinement.
7. Explain async/await and the event loop.
   - Single-threaded cooperative concurrency for I/O-bound work: while one task waits for I/O the loop runs others. A CPU-heavy task blocks the loop and stalls everyone; offload to worker threads or processes. Async functions return promises/futures; awaiting yields control.
8. What is the GIL in CPython and how does it affect concurrency?
   - The Global Interpreter Lock lets only one thread execute Python bytecode at a time in the standard build, so CPU-bound Python threads do not run in parallel; I/O-bound threads still help. Use multiprocessing, native extensions, or async for I/O. Recent Python versions offer an optional free-threaded build, but standard builds still have the GIL.
9. Describe the producer-consumer pattern. (A shared bounded queue decouples producers and consumers; backpressure when full.)
10. Optimistic versus pessimistic locking. (Pessimistic locks the resource up front, safe for high contention but reduces throughput; optimistic checks a version at commit and retries on conflict, good for low contention.)

## F. Memory and runtime

1. Stack versus heap memory.
   - Stack: function call frames and local variables, fast, automatically reclaimed, limited size. Heap: dynamically allocated objects with longer lifetimes, managed manually or by a garbage collector.
2. How does garbage collection work? Compare reference counting and tracing.
   - Reference counting frees an object when its count hits zero but needs extra handling for cycles. Tracing collectors mark objects reachable from roots and sweep the rest. Trade-offs: pause times, throughput, memory overhead. Python uses reference counting plus a cycle detector; JVM and JavaScript engines use generational tracing collectors.
3. How can you leak memory in a garbage-collected language?
   - Lingering references: unbounded caches or maps, event listeners never removed, closures capturing large objects, global registries, timers never cleared.
4. Value versus reference semantics. What does "pass by reference" actually mean in your language? (Many languages pass references by value; mutating the object is visible to the caller, reassigning the parameter is not.)
5. Why does immutability help? (Safer sharing across threads, easier reasoning, cheap change detection; cost: allocation overhead, which persistent data structures mitigate.)
6. What is a pointer or reference, and what is a null/None pointer problem? (Dangling pointers, null dereference, and how optional types or null-safety features address them.)

## G. Operating system basics

1. What happens when a process makes a system call? (Switch from user mode to kernel mode to perform a privileged operation such as file or network I/O.)
2. What is a context switch and why is it costly? (Saving and restoring CPU state, cache and TLB effects.)
3. What is virtual memory and paging? (Each process sees its own address space mapped to physical memory by the OS; pages can be swapped to disk; page faults are expensive.)
4. Blocking versus non-blocking I/O, and what is epoll/kqueue-style multiplexing? (One thread watches many sockets and is notified when they are ready; the basis of event-driven servers.)
5. What is a file descriptor? (An integer handle for an open file, socket, or pipe; limits can cause "too many open files" errors.)
6. What is the difference between a process being CPU-bound and I/O-bound, and why does it matter for scaling choices?

## H. Networking and the web

1. What happens when you type a URL into a browser and press Enter?
   - DNS resolution (caches, resolvers); TCP connection (handshake); TLS handshake for HTTPS; HTTP request; load balancer or CDN; application server and database; response; browser parses HTML, fetches CSS, JS, and images, builds the DOM, and renders. A strong answer mentions caching at several layers.
2. TCP versus UDP.
   - TCP is connection-oriented and reliable: ordering, retransmission, flow and congestion control. UDP is connectionless with no delivery guarantees but lower latency and overhead; used for DNS, streaming, games, and as the base for QUIC.
3. HTTP methods: which are safe and which are idempotent?
   - Safe: GET, HEAD, OPTIONS. Idempotent: GET, HEAD, PUT, DELETE, OPTIONS. POST is neither safe nor idempotent by default; PATCH is not guaranteed idempotent. This matters for retries.
4. HTTP status code families, and the difference between 401 and 403, 502, 503, and 504.
   - 2xx success, 3xx redirects, 4xx client errors, 5xx server errors. 401: not authenticated (missing or invalid credentials). 403: authenticated but not allowed. 429: rate limited. 502: bad response from an upstream; 503: service unavailable or overloaded; 504: upstream timeout.
5. HTTP/1.1 versus HTTP/2 versus HTTP/3.
   - HTTP/2 multiplexes many requests over one connection, with header compression; HTTP/3 runs over QUIC (on UDP) to avoid head-of-line blocking at the transport layer and speed up connection setup.
6. How does HTTPS/TLS work at a high level?
   - Server presents a certificate signed by a trusted authority; client verifies it; the two negotiate session keys via asymmetric cryptography (key exchange); the actual traffic is encrypted with fast symmetric encryption.
7. What are common DNS record types? (A, AAAA, CNAME, MX, TXT, NS.) What is DNS TTL and why does it matter for migrations?
8. What is a CDN and what should (and should not) go through it?
9. What is CORS and why does it exist?
   - Browsers enforce the same-origin policy; CORS is a set of server response headers that explicitly allow cross-origin requests from browser code. It is a browser protection, not server-side access control for non-browser clients.
10. Cookies versus localStorage versus sessionStorage. (Cookies are sent automatically with requests and can be HttpOnly, Secure, SameSite; web storage is script-accessible only and vulnerable to XSS theft.)
11. What are WebSockets and how do they differ from HTTP polling? (A persistent bidirectional connection established via an HTTP upgrade; server can push. See the scalability file for when to choose them.)
12. Layer 4 versus layer 7 load balancing. (L4 routes on IP and port; L7 understands HTTP and can route on path, headers, cookies.)
13. What is the difference between latency and bandwidth, and why can more bandwidth not fix a latency problem?

## I. Databases

1. What does ACID mean?
   - Atomicity (all or nothing), Consistency (constraints preserved), Isolation (concurrent transactions do not interfere improperly), Durability (committed data survives crashes).
2. What are transaction isolation levels and the anomalies they prevent?
   - Per the SQL standard: read uncommitted, read committed, repeatable read, serializable. Anomalies: dirty read (reading uncommitted data), non-repeatable read (same row differs between reads), phantom read (new rows match a repeated query). Higher isolation means fewer anomalies but more locking or retries. Real databases implement these differently (for example snapshot-based approaches), so know your database's actual behavior.
3. How does a database index work, and what does it cost?
   - Commonly a B-tree: ordered structure that turns full scans into O(log n) lookups and supports range queries. Costs: extra storage and slower writes. A composite index on (a, b, c) helps queries on a, on a and b, and on a, b, and c (leftmost prefix), but not on b alone. Indexes are skipped when functions are applied to the column, on low-selectivity columns, or with leading wildcards in LIKE.
4. How do you diagnose a slow query? (EXPLAIN / query plan; look for sequential scans, bad join order, missing indexes, large sorts; fix the query or add the right index; verify.)
5. What is the N+1 query problem and how do you fix it? (One query for a list plus one per item; fix with joins, eager loading, or batching.)
6. Normalization versus denormalization.
   - Normalization removes redundancy and update anomalies (1NF to 3NF); denormalization duplicates data to speed reads at the cost of more complex, riskier writes. Use denormalization deliberately for read-heavy paths.
7. Inner join versus left join versus full outer join. (Which rows survive and where NULLs appear.)
8. SQL versus NoSQL: how do you choose?
   - Choose by access patterns, consistency needs, and scale, not by trend. Relational: strong consistency, joins, ad hoc queries, mature tooling. Document stores: flexible schema, natural fit for aggregate-shaped data, easier horizontal scaling, but you lose joins and often cross-document transactions. Many systems use both.
9. Primary key versus foreign key versus unique constraint. Natural versus surrogate keys.
10. Replication versus partitioning versus sharding. (Replication copies data for availability and read scaling; partitioning splits a table within a database; sharding splits data across servers by a key, with cross-shard query and rebalancing costs.)
11. What is the CAP theorem? (During a network partition a distributed system must choose between consistency and availability. Partition tolerance is not optional in practice. Mention that many systems trade latency against consistency even without partitions.)
12. What are database migrations and how do you run them safely? (Versioned schema changes; expand-and-contract for zero downtime: add new column, deploy code that writes both, backfill, switch reads, remove the old column.)
13. What is connection pooling and why does it matter? (Reusing a limited number of database connections; opening one per request does not scale and can exhaust the server.)

## J. Security basics

1. Hashing versus encryption versus encoding.
   - Hashing is one-way (integrity, password storage). Encryption is reversible with a key (confidentiality). Encoding (such as base64) is just representation and provides no security.
2. How should passwords be stored?
   - Never in plain text. Use a slow, salted, purpose-built hash (Argon2, bcrypt, or scrypt) with a unique salt per password. Fast general-purpose hashes (MD5, SHA-256 alone) are unsuitable.
3. Symmetric versus asymmetric encryption. (One shared key, fast, bulk data versus a public/private key pair, key exchange and signatures; HTTPS uses both.)
4. What is SQL injection and how do you prevent it? (Untrusted input changes the query structure. Use parameterized queries or prepared statements; validate input; least-privilege database accounts. Escaping by hand is fragile.)
5. What is XSS and how do you prevent it? (Attacker-controlled script runs in another user's browser. Encode output for its context, avoid inserting raw HTML, use a Content Security Policy, HttpOnly cookies limit what a script can steal.)
6. What is CSRF and how do you prevent it? (A malicious site makes the user's browser send an authenticated request. Use SameSite cookies, anti-CSRF tokens, and check the origin.)
7. Authentication versus authorization. Sessions versus JWTs. (See the architecture file.)
8. What is the principle of least privilege? Give an example.
9. How do you handle secrets and dependencies safely? (Secrets manager, rotation, never in source control; pin and audit dependencies, watch for supply-chain risk.)
10. What are the OWASP Top 10 categories you encounter most? (Broken access control, injection, cryptographic failures, insecure design, security misconfiguration, vulnerable components, and others; they should be able to name a few and describe how they defend.)
11. How would you protect an API from abuse? (Authentication, authorization, input validation, rate limiting, logging and alerting, WAF, least-privilege keys.)

## K. Engineering practice

1. Git: merge versus rebase. (Merge preserves history with a merge commit; rebase rewrites commits onto a new base for a linear history; never rebase shared public history.)
2. What happens in a good CI/CD pipeline? (Build, lint, test, security scan, artifact, deploy to staging, automated checks, gated production deploy, rollback plan.)
3. Blue/green versus canary deployments versus feature flags. (Switch traffic between two full environments; release to a small slice first and watch metrics; decouple deploy from release.)
4. Logs, metrics, and traces: what is each for? (Logs: discrete events for debugging; metrics: aggregated numbers for alerting and trends; traces: follow a request across services.)
5. What is technical debt and how do you decide when to pay it down? (Deliberate shortcuts with an interest cost; pay down when it slows delivery or raises risk; track it, tie it to business impact.)
6. How do you version an API without breaking clients? (Additive changes, deprecation periods, versioned endpoints or headers, contract tests, expand-and-contract.)
7. What makes code review effective? (Small changes, clear descriptions, focus on correctness, design, and tests rather than style that tools can enforce; kind and specific comments.)

## Follow-up probes
- What is the worst case, and when does it happen?
- What would you change if the data did not fit in memory?
- How would you test that?
- What would break first at 100 times the size?
- Can you give a real example where you hit this?
