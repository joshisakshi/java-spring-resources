# Backend / Infra Interview Prep — FAANG / MAANG Track

> A focused, end-to-end revision guide for the 9 topics below, written at the depth typically probed in SDE-1 / SDE-2 interviews at FAANG/MAANG-tier companies (Amazon, Meta, Google, Microsoft, Netflix, Uber, etc.). Each section covers: core concepts → deep-dive internals → common interview Q&A → gotchas/follow-ups → a quick cheat-sheet table.

**Topics:**
1. [Kafka](#1-kafka)
2. [Redis (Deep Dive)](#2-redis-deep-dive)
3. [Docker & Docker Compose](#3-docker--docker-compose)
4. [SQL Performance & Indexing](#4-sql-performance--indexing)
5. [System Design for SDE-1](#5-system-design-for-sde-1)
6. [Microservices](#6-microservices)
7. [AWS Basics (EC2, S3, RDS, IAM)](#7-aws-basics-ec2-s3-rds-iam)
8. [Kubernetes (Basics)](#8-kubernetes-basics)
9. [CI/CD (GitHub Actions / Jenkins)](#9-cicd-github-actions--jenkins)

---

## 1. Kafka

### 1.1 What problem does Kafka solve?
Kafka is a **distributed event streaming platform** — a durable, partitioned, replicated commit log that decouples producers from consumers and supports very high throughput (millions of messages/sec) with replayability. It's not just a message queue: messages aren't deleted on consumption, they're retained per a policy, so multiple independent consumers can re-read history.

### 1.2 Core Architecture
- **Broker**: a Kafka server. A cluster has multiple brokers.
- **Topic**: a named stream of records, split into **partitions** for parallelism.
- **Partition**: an ordered, immutable, append-only log. Order is guaranteed *only within a partition*, not across a topic.
- **Offset**: a per-partition, monotonically increasing ID for each record. Consumers track offsets to know where they are.
- **Replication factor**: each partition is replicated across N brokers. One replica is the **leader** (handles all reads/writes); others are **followers** that replicate from the leader.
- **ISR (In-Sync Replicas)**: the set of replicas fully caught up with the leader. Only ISR members are eligible for leader election.
- **Controller**: one broker elected to manage partition leadership and cluster metadata.
- **ZooKeeper vs KRaft**: Older Kafka used ZooKeeper for metadata/coordination. Since KIP-500, Kafka can run in **KRaft mode** (Kafka's own Raft-based metadata quorum), removing the ZooKeeper dependency — this is now the default in modern Kafka (3.x+) and a popular interview topic ("why did Kafka remove ZooKeeper?").

### 1.3 Producers
- Choose a partition via: explicit key (hash of key → partition, guarantees same-key ordering), round-robin (no key), or custom partitioner.
- **acks** setting controls durability vs latency tradeoff:
  - `acks=0`: fire and forget, fastest, can lose data.
  - `acks=1`: leader writes to its log, no follower confirmation — can lose data if leader fails before replication.
  - `acks=all` (`-1`): all in-sync replicas must acknowledge — strongest durability.
- **Idempotent producer** (`enable.idempotence=true`): assigns a producer ID + sequence number per partition so brokers can dedupe retried writes → exactly-once *per partition, per producer session*.
- **Batching & compression**: producers batch records (`linger.ms`, `batch.size`) and can compress (gzip/snappy/lz4/zstd) to boost throughput.

### 1.4 Consumers
- Consumers belong to a **consumer group**; Kafka assigns each partition to exactly one consumer within the group (so a group can parallelize reads across partitions; partitions > consumers means some consumers idle, consumers > partitions means some consumers get nothing).
- **Rebalancing**: triggered when a consumer joins/leaves/crashes; partitions are reassigned. Causes a brief pause. Newer "cooperative sticky" assignors minimize disruption vs the old "eager" rebalance protocol that revoked *all* partitions every time.
- **Offset commits**: can be automatic (`enable.auto.commit=true`, risk of message loss or duplication) or manual (commit after successful processing — gives more control over delivery semantics).

### 1.5 Delivery Semantics
| Semantic | How it's achieved | Risk |
|---|---|---|
| At-most-once | Commit offset before processing | Message loss on crash |
| At-least-once | Commit offset after processing (default safe choice) | Duplicate processing possible |
| Exactly-once | Idempotent producer + transactional API (`transactional.id`), or idempotent consumer logic | More complex, some throughput cost |

**Exactly-once semantics (EOS)** across producer→topic→consumer is achieved via Kafka transactions: producer writes to multiple partitions/topics atomically, and consumers configured with `isolation.level=read_committed` only see committed transactional writes.

### 1.6 Retention & Compaction
- **Time/size-based retention**: records deleted after `retention.ms` or when partition exceeds `retention.bytes` — independent of whether they've been consumed.
- **Log compaction** (`cleanup.policy=compact`): keeps only the latest value per key, useful for "changelog" use cases (e.g., rebuilding state, CDC, KTable in Kafka Streams).

### 1.7 Common Interview Questions
1. **Why is Kafka so fast?** Sequential disk I/O (append-only log), zero-copy transfer (`sendfile`), batching, OS page cache reliance instead of per-message JVM heap allocation, partition-level parallelism.
2. **How does Kafka guarantee ordering?** Only within a partition. To guarantee order for a given entity, key messages by that entity's ID so they always land in the same partition.
3. **What happens if a leader broker dies?** The controller detects it (via session timeout) and elects a new leader from the ISR. Producers/consumers refresh metadata and reconnect to the new leader.
4. **How do you handle a "poison pill" message that keeps crashing a consumer?** Use a dead-letter topic / DLQ pattern, catch and skip with logging, or move to a retry topic with backoff.
5. **Kafka vs RabbitMQ vs SQS?** Kafka: high-throughput log, replay, ordered partitions, pull-based. RabbitMQ: smart broker, flexible routing (exchanges), push-based, better for complex routing/lower latency per message. SQS: fully managed, simpler, at-least-once, no strict ordering (unless FIFO queue), good for decoupling without operating infra.
6. **How would you design a system to avoid duplicate processing (idempotent consumer)?** Store a processed-message ID (or business key) in a dedupe table/cache with a TTL; check-before-process; or rely on idempotent writes downstream (e.g., upsert by key).
7. **How many partitions should a topic have?** Driven by target throughput ÷ per-partition throughput, and by max consumer parallelism needed. More partitions = more parallelism but more overhead (file handles, rebalance cost, end-to-end latency for `acks=all`).
8. **What's the difference between Kafka Streams and a regular consumer?** Kafka Streams is a client library for stateful stream processing (joins, windowed aggregations, KTables) built on top of consumer/producer APIs, with local state stores backed by changelog topics for fault tolerance.

### 1.8 Gotchas / Follow-ups
- Rebalance storms from misconfigured `session.timeout.ms` / `max.poll.interval.ms` (consumer looks dead because processing took too long between polls).
- Under-replicated partitions when a broker is slow/down — watch `UnderReplicatedPartitions` metric.
- "Exactly-once" is end-to-end only if every hop (produce → process → produce/sink) participates in the transaction/idempotency story — a non-transactional side effect (e.g., calling an external API) breaks the guarantee.

### 1.9 Cheat Sheet
| Concept | One-liner |
|---|---|
| Partition | Unit of parallelism + ordering boundary |
| Replication | Durability; leader handles I/O, followers replicate |
| ISR | Replicas eligible for leader election |
| Consumer group | Enables parallel, load-balanced consumption |
| acks=all | Strongest write durability |
| Idempotent producer | Dedupes retries at producer level |
| Log compaction | Keep-latest-value-per-key retention |
| KRaft | Kafka's own consensus, replacing ZooKeeper |

---

## 2. Redis (Deep Dive)

### 2.1 What is Redis?
An in-memory data structure store used as a cache, database, message broker, and streaming engine. Single-threaded for command execution (per core, with I/O threading added in Redis 6+ for network I/O only), which is why atomicity of single commands is "free" — no locking needed within one command.

### 2.2 Data Structures (know the complexity & use case for each)
| Type | Key ops | Complexity | Typical use case |
|---|---|---|---|
| String | GET/SET, INCR | O(1) | Caching, counters, distributed locks |
| List | LPUSH/RPUSH, LRANGE | O(1) push, O(N) range | Queues, recent-activity feeds |
| Hash | HSET/HGET | O(1) | Storing objects (e.g., user profile fields) |
| Set | SADD, SINTER | O(1) add, O(N) for set ops | Tags, unique visitors, relationships |
| Sorted Set (ZSet) | ZADD, ZRANGE, ZRANK | O(log N) | Leaderboards, rate limiters, priority queues, time-series windows |
| Bitmap | SETBIT, BITCOUNT | O(1)/O(N) | Feature flags per user, attendance tracking |
| HyperLogLog | PFADD, PFCOUNT | O(1) | Approximate cardinality (unique counts) at ~12KB fixed memory, ~0.81% error |
| Stream | XADD, XREAD | O(1) append | Event sourcing / lightweight Kafka-like log with consumer groups |

### 2.3 Persistence
- **RDB (snapshotting)**: point-in-time binary dump at intervals (`save 900 1`, etc.) — compact, fast restarts, but can lose data since last snapshot.
- **AOF (Append-Only File)**: logs every write command; replay on restart. More durable (configurable fsync: `always`, `everysec`, `no`), larger file, slower restart unless compacted (AOF rewrite).
- **Hybrid**: RDB preamble + AOF tail (default in modern Redis) — fast load + minimal data loss.
- **No persistence**: Redis can run pure in-memory (cache-only use case) — fastest, but data lost on restart/crash.

### 2.4 Eviction Policies (when `maxmemory` is hit)
- `noeviction`: return errors on writes once full.
- `allkeys-lru` / `allkeys-lfu`: evict least-recently/frequently used key across the whole keyspace.
- `volatile-lru` / `volatile-lfu` / `volatile-ttl`: only evict keys that have a TTL set.
- `allkeys-random` / `volatile-random`.
- **Interview angle**: LRU in Redis is *approximated* (sampling-based, not exact) for performance — know this nuance.

### 2.5 Replication & High Availability
- **Master-replica replication**: asynchronous by default; replicas can serve reads (read scaling) but writes go to the master.
- **Redis Sentinel**: monitors master/replicas, performs automatic failover (promotes a replica) when the master is unreachable, and provides service discovery for clients. Needs quorum (odd number of Sentinels, typically 3+) to avoid split-brain on failure detection.
- **Redis Cluster**: native sharding across nodes using **16384 hash slots**; each key is mapped to a slot via `CRC16(key) % 16384`. Supports `{hashtag}` syntax to force related keys into the same slot (needed for multi-key operations/transactions). Handles node failure via per-shard replicas + gossip protocol, but cross-slot multi-key commands aren't supported without hash tags.

### 2.6 Caching Patterns (very commonly asked)
| Pattern | How it works | Tradeoff |
|---|---|---|
| Cache-aside (lazy loading) | App checks cache; on miss, reads DB, populates cache | Simple, but first request always misses; risk of stale data |
| Write-through | App writes to cache, cache synchronously writes to DB | Cache always consistent with DB, but write latency = cache+DB |
| Write-behind (write-back) | App writes to cache; cache asynchronously flushes to DB | Fast writes, but risk of data loss if cache crashes before flush |
| Read-through | Cache itself loads from DB on miss (cache library handles it) | Cleaner app code, needs cache layer support |

### 2.7 Cache Invalidation & Consistency
- **TTL-based expiry**: simplest, but allows brief staleness window.
- **Explicit invalidation on write**: delete/update cache key when DB record changes — better consistency, more code paths to get right.
- **Thundering herd / cache stampede**: many requests miss simultaneously (e.g., a hot key expires) and hammer the DB. Mitigate with: request coalescing (single-flight lock per key), staggered/jittered TTLs, or pre-emptive refresh before expiry.
- **Cache penetration**: queries for keys that don't exist in DB *or* cache, repeatedly hitting DB. Mitigate with caching the "not found" result (with short TTL) or a bloom filter.

### 2.8 Distributed Locking — Redlock
- Naive single-instance lock: `SET key value NX PX 30000` (atomic set-if-not-exists with expiry).
- **Redlock algorithm**: acquire the lock on a majority of N independent Redis instances (e.g., 3 of 5) within a time budget, accounting for clock drift — designed for higher-confidence locking across a cluster of independent masters. It's debated in the distributed-systems community (Martin Kleppmann's critique vs Redis's response) — a good interviewer-bait topic to be aware of: Redlock assumes bounded clock drift and doesn't protect against process pauses (GC, scheduling) causing a lock holder to act after its lock expired — for hard correctness, use fencing tokens at the resource, not just the lock.

### 2.9 Transactions & Scripting
- **MULTI/EXEC**: queues commands, executes atomically as a batch — no other client's commands interleave, but no rollback on a runtime error mid-transaction (it's not ACID rollback, just "no interleaving").
- **WATCH**: optimistic locking — abort the transaction if a watched key changed since WATCH (used for compare-and-swap style logic).
- **Lua scripting (`EVAL`)**: executes atomically server-side, good for multi-step logic (e.g., check-then-set, rate limiter logic) without round-trip races.

### 2.10 Pub/Sub vs Streams
- **Pub/Sub**: fire-and-forget, no persistence, no replay, no consumer groups, message dropped if no subscriber connected.
- **Streams (XADD/XREAD/XGROUP)**: persisted log with consumer groups, acknowledgments, and replay — closer to a lightweight Kafka. Use this instead of Pub/Sub when you need durability.

### 2.11 Common Interview Questions
1. **Why is Redis fast?** In-memory, single-threaded event loop avoids lock contention/context switching for command execution, efficient data structures (e.g., skip lists for ZSet), I/O multiplexing (epoll).
2. **How would you implement a rate limiter with Redis?** Fixed window (`INCR` + `EXPIRE`), sliding window log (ZSET with timestamps, trim old entries), or token bucket using Lua script for atomicity.
3. **Cache-aside vs write-through — which would you pick for a read-heavy product catalog?** Cache-aside, since most data is read far more than written and you don't want every read-miss path coupled to write latency.
4. **How do you handle a hot key in Redis Cluster (one key gets disproportionate traffic)?** Local in-process caching layer in front of Redis, key splitting/sharding (e.g., `key:0`, `key:1`... and read from a random shard), or replicate the hot key's read path.
5. **What's the difference between Redis and Memcached?** Redis: richer data structures, persistence, replication/cluster, pub/sub, Lua scripting. Memcached: pure cache, multi-threaded, simpler, slightly better for plain key-value at massive scale with no persistence need.
6. **How does Redis handle memory efficiently for small collections?** Uses compact encodings (`listpack`/`ziplist` for small hashes/lists/sets) that switch to full hash table/skiplist representations once size thresholds are crossed.

### 2.12 Cheat Sheet
| Concept | One-liner |
|---|---|
| RDB | Snapshot, fast restart, may lose recent writes |
| AOF | Command log, more durable, slower restart |
| Sentinel | Failover + discovery for master-replica setups |
| Cluster | Sharding via 16384 hash slots |
| Cache-aside | Lazy load on miss — most common pattern |
| Cache stampede fix | Locking/jitter/pre-refresh |
| Redlock | Multi-instance distributed lock; has known edge-case critiques |
| Streams > Pub/Sub | When you need persistence + consumer groups |

---

## 3. Docker & Docker Compose

### 3.1 Core Concepts
- **Image**: read-only template (layers) built from a `Dockerfile`. Layers are cached and content-addressable (each layer = a diff, hashed).
- **Container**: a running (or stopped) instance of an image — adds a thin writable layer on top.
- **Image vs Container**: image = class, container = object/instance.
- **Registry**: stores images (Docker Hub, ECR, GCR, private registries).

### 3.2 Dockerfile Best Practices (frequently asked to "write/critique a Dockerfile")
- Use small base images (`alpine`, `slim`, or distroless) to reduce attack surface and image size.
- Order instructions from least → most frequently changing, so Docker's layer cache is maximally reused (e.g., copy `package.json`/`requirements.txt` and install deps *before* copying the rest of the source).
- Use **multi-stage builds**: build/compile in one stage (with full toolchain), copy only the final artifact into a minimal runtime stage — keeps final image small and free of build tools.
- Avoid running as root in the final container (`USER appuser`) for security.
- Use `.dockerignore` to avoid bloating build context (e.g., exclude `node_modules`, `.git`).
- Prefer `COPY` over `ADD` unless you specifically need `ADD`'s remote-URL/auto-extract behavior.
- Combine related `RUN` commands with `&&` to reduce layer count where it doesn't hurt cache granularity.
- Set explicit `HEALTHCHECK` so orchestrators know real liveness, not just "process exists."

```dockerfile
# Example multi-stage build
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY package*.json ./
RUN npm ci --omit=dev
USER node
HEALTHCHECK CMD curl -f http://localhost:3000/health || exit 1
CMD ["node", "dist/index.js"]
```

### 3.3 Networking
- **bridge** (default): containers on the same custom bridge network can resolve each other by container/service name via Docker's embedded DNS.
- **host**: container shares the host's network namespace — no isolation, max performance.
- **none**: fully isolated, no networking.
- **overlay**: multi-host networking, used in Swarm (and conceptually similar to what CNI plugins do in Kubernetes).

### 3.4 Volumes & Persistence
- **Named volumes**: managed by Docker, live outside the container's writable layer, survive container removal — the right choice for databases/persistent state.
- **Bind mounts**: map a host path directly into the container — useful for local dev (live code reload), but couples to host filesystem layout.
- **tmpfs mounts**: in-memory, never persisted — useful for secrets/temp data.

### 3.5 Docker Compose
- Declarative multi-container orchestration for local dev / simple deployments via `docker-compose.yml`.
- Key fields: `services`, `image`/`build`, `ports`, `volumes`, `environment`/`env_file`, `depends_on`, `networks`, `healthcheck`.
- `depends_on` controls **start order**, not readiness — a DB container can be "up" before it's actually accepting connections. Use a `healthcheck` + `depends_on: condition: service_healthy`, or an app-level retry/backoff on connect, to handle this correctly.

```yaml
version: "3.9"
services:
  app:
    build: .
    ports: ["3000:3000"]
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/appdb
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16
    environment:
      - POSTGRES_PASSWORD=pass
    volumes:
      - dbdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      retries: 5
volumes:
  dbdata:
```

### 3.6 Common Interview Questions
1. **Why are Docker containers more lightweight than VMs?** Containers share the host kernel and use namespaces (process, network, mount, etc.) + cgroups (resource limits) for isolation, instead of virtualizing an entire OS/hardware stack like a VM/hypervisor does.
2. **How does Docker's layer caching work, and why does instruction order matter?** Each Dockerfile instruction creates a layer; Docker reuses cached layers if the instruction and its input (e.g., copied files) are unchanged. Put rarely-changing steps (dependency installs) before frequently-changing ones (source code copy) to maximize cache hits on rebuilds.
3. **What's the difference between `CMD` and `ENTRYPOINT`?** `ENTRYPOINT` defines the fixed executable; `CMD` provides default arguments (overridable at `docker run`). Often combined: `ENTRYPOINT ["python", "app.py"]`, `CMD ["--port", "8080"]`.
4. **How do you reduce image size?** Multi-stage builds, smaller base images (alpine/distroless), minimizing layers, removing build-time caches/package manager caches within the same `RUN` layer they were created.
5. **How would you debug a container that keeps restarting (CrashLoopBackOff-style behavior)?** `docker logs`, `docker inspect` for exit code/health status, run interactively overriding entrypoint (`docker run -it --entrypoint sh <image>`), check resource limits (OOMKilled).
6. **cgroups vs namespaces?** Namespaces provide isolation (what a process can *see*: PIDs, network, mounts, hostname). cgroups provide resource control (what a process can *use*: CPU, memory limits).

### 3.7 Cheat Sheet
| Concept | One-liner |
|---|---|
| Image | Immutable template, layered |
| Container | Running instance + writable layer |
| Multi-stage build | Slim runtime image, no build tools shipped |
| Named volume | Durable storage, Docker-managed |
| Bridge network | Default, name-based DNS between containers |
| depends_on | Start order only — pair with healthcheck for readiness |
| cgroups | Resource limits (CPU/mem) |
| Namespaces | Isolation (PID/net/mount/etc.) |

---

## 4. SQL Performance & Indexing

### 4.1 How Indexes Work
- Default index structure in most RDBMS (Postgres, MySQL/InnoDB) is a **B+ Tree**: balanced tree, sorted keys, O(log N) lookups, efficient range scans because leaves are linked in sorted order.
- **Clustered index** (e.g., InnoDB primary key): table rows are physically stored in index order — fast for PK lookups/range scans, but only one per table (it *is* the table).
- **Non-clustered (secondary) index**: separate structure pointing to the row (via PK or rowid) — every secondary index lookup that needs non-indexed columns requires an extra lookup to the base table, unless...
- **Covering index**: an index that includes all columns needed by a query, so the engine never touches the base table ("index-only scan") — a huge performance lever.
- **Composite (multi-column) index**: order of columns matters — an index on `(a, b, c)` can serve queries filtering on `a`, `a+b`, or `a+b+c`, but **not** efficiently serve a query filtering on `b` alone (leftmost-prefix rule).

### 4.2 When Indexes Help vs Hurt
- Help: `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY` columns with good selectivity (many distinct values).
- Hurt: every index adds write overhead (insert/update/delete must maintain it) and storage — over-indexing slows writes without proportional read benefit.
- Low-selectivity columns (e.g., a boolean `is_active` with 95% `true`) often don't benefit from a plain index — the optimizer may prefer a full scan anyway. A **partial index** (`WHERE is_active = false`) can still help if you mostly query the rare case.
- Functions on indexed columns (`WHERE LOWER(email) = ...`) typically prevent index usage unless you create a matching **functional/expression index**.

### 4.3 Query Plans
- `EXPLAIN` / `EXPLAIN ANALYZE` (Postgres) or `EXPLAIN` (MySQL) show the chosen execution plan: scan type (seq scan vs index scan vs index-only scan), join algorithm, estimated vs actual rows, cost.
- Common scan types: **Seq Scan** (full table scan), **Index Scan** (index lookup + row fetch), **Index Only Scan** (covering index, no table fetch), **Bitmap Heap Scan** (Postgres: combines multiple indexes via bitmap before fetching rows).
- Join algorithms: **Nested Loop** (good for small outer set + indexed inner lookup), **Hash Join** (good for large unsorted sets, no index needed, builds a hash table on the smaller side), **Merge Join** (good when both sides are already sorted on the join key).

### 4.4 The N+1 Query Problem
Classic interview/code-review topic: an ORM lazily fetches a parent row, then issues one additional query *per child row* in a loop (e.g., loading 100 orders, then 100 separate queries for each order's customer). Fixes: eager loading / `JOIN FETCH` / `select_related`/`prefetch_related` (Django), batching with `WHERE id IN (...)`, or a single JOIN query upfront.

### 4.5 Normalization vs Denormalization
- **Normalization** (3NF etc.): reduces redundancy, avoids update anomalies, but more joins for reads.
- **Denormalization**: duplicates data to avoid joins, speeds up reads at the cost of write complexity and potential inconsistency — common in read-heavy systems, materialized views, or CQRS read models.
- Be ready to discuss trade-offs for a specific schema, not just define the terms.

### 4.6 Transactions, ACID & Isolation Levels
- **ACID**: Atomicity, Consistency, Isolation, Durability.
- **Isolation levels** (weakest → strongest) and the anomalies they prevent:

| Level | Dirty Read | Non-repeatable Read | Phantom Read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed (Postgres/Oracle default) | Prevented | Possible | Possible |
| Repeatable Read (MySQL/InnoDB default) | Prevented | Prevented | Possible* |
| Serializable | Prevented | Prevented | Prevented |

\*InnoDB's Repeatable Read actually prevents most phantom reads too via MVCC + gap locking — a good nuance to mention if asked.

- **MVCC (Multi-Version Concurrency Control)**: how Postgres/InnoDB give readers a consistent snapshot without blocking writers — each row version is tagged with transaction visibility info instead of using read locks for plain reads.
- **Locking**: shared (read) vs exclusive (write) locks; row-level vs table-level; **deadlocks** occur when two transactions hold locks the other needs — DB detects and aborts one (deadlock victim) automatically in most engines.

### 4.7 Common Interview Questions
1. **A query got slow after the table grew — how do you debug it?** Run `EXPLAIN ANALYZE`, check if it's doing a seq scan where an index scan is expected, check index existence/staleness (stats out of date → `ANALYZE`), check for leading-wildcard `LIKE '%x'` (kills B-tree usage), check for implicit type casts preventing index use.
2. **Why might the query planner ignore an existing index?** Low selectivity (planner estimates a full scan is cheaper), stale statistics, function applied to the column, the index doesn't cover needed columns and the table is small enough that a scan is cheap anyway.
3. **Explain leftmost-prefix rule with an example.** Index on `(country, city)` serves `WHERE country = 'IN'` and `WHERE country='IN' AND city='Bengaluru'` efficiently, but not `WHERE city='Bengaluru'` alone.
4. **How would you paginate efficiently over millions of rows?** Avoid `OFFSET N` for large N (DB still scans/discards N rows); use **keyset/cursor pagination** — `WHERE id > last_seen_id ORDER BY id LIMIT k` — which stays O(log N) via the index regardless of page depth.
5. **Difference between `WHERE` and `HAVING`?** `WHERE` filters rows before grouping; `HAVING` filters groups after aggregation.
6. **What's a covering index and when would you add one?** An index containing every column the query needs (in `WHERE`, `SELECT`, `ORDER BY`) so the engine can answer entirely from the index — add it for hot, read-heavy queries where avoiding the table heap fetch matters.

### 4.8 Cheat Sheet
| Concept | One-liner |
|---|---|
| B+ Tree index | Sorted, O(log N), good for range + equality |
| Clustered index | Table data physically ordered by this index |
| Covering index | Query answered from index alone |
| Leftmost prefix | Composite index usable only from its left columns inward |
| N+1 problem | Loop-issued per-row queries; fix via eager load/batching |
| MVCC | Snapshot isolation without blocking readers |
| Keyset pagination | `WHERE id > last_id LIMIT k` beats large `OFFSET` |

---

## 5. System Design for SDE-1

> SDE-1 system design rounds rarely expect Netflix-scale answers. The bar is: structured approach, correct fundamentals (estimation, API design, data model, one bottleneck identified and resolved), and clear communication — not exhaustive scale handling.

### 5.1 A Repeatable Framework
1. **Clarify requirements** — functional (what features) and non-functional (scale, latency, consistency vs availability needs). Ask about read/write ratio, expected QPS, data size, growth.
2. **Back-of-envelope estimation** — rough numbers for QPS, storage, bandwidth. Interviewers care that you *can* estimate, not that you're exact.
3. **API design** — define key endpoints/contracts (e.g., `POST /urls`, `GET /{code}`).
4. **Data model** — entities, relationships, choice of SQL vs NoSQL and why.
5. **High-level design** — draw boxes: client → load balancer → app servers → cache → DB; identify where async/queue fits.
6. **Deep dive on 1–2 components** — whatever's most interesting/bottlenecked (e.g., ID generation, cache invalidation, hot partition).
7. **Address bottlenecks & trade-offs** — scaling reads vs writes, consistency vs availability, caching strategy.

### 5.2 Core Building Blocks to Know Cold
- **Load balancing**: L4 (transport-level, fast, less smart) vs L7 (application-level, can route on URL/headers, do SSL termination); algorithms — round robin, least connections, consistent hashing (for sticky routing / cache locality).
- **Caching**: where (client, CDN, app-level, DB-level) and which pattern (see Redis section 2.6). Know when caching helps (read-heavy, tolerant of slight staleness) vs when it doesn't (strict consistency needs).
- **Database scaling**:
  - **Vertical scaling**: bigger machine — simple, limited ceiling.
  - **Read replicas**: scale reads, async replication lag means replicas can be slightly stale.
  - **Sharding/partitioning**: split data across nodes by a shard key — scales writes too, but cross-shard queries/joins get hard, and a poorly chosen shard key creates hot shards.
  - **Vertical partitioning**: split by feature/table rather than by row.
- **Consistent hashing**: distributes keys/requests across nodes such that adding/removing a node only remaps a small fraction of keys (vs naive `hash % N` remapping almost everything) — used in caches, sharded databases, CDNs, load balancers.
- **CAP theorem**: under a network partition, you choose **Consistency** or **Availability** (you can't have both); in practice most real designs are about where on the **consistency ↔ latency** spectrum a *given operation* sits (PACELC extends this), not a binary global choice.
- **Rate limiting**: token bucket (smooths bursts, refill rate = limit), leaky bucket (steady output rate), fixed/sliding window counters — implement with Redis for distributed enforcement (see 2.11.2).
- **Async processing / queues**: decouple slow or bursty work (e.g., sending emails, resizing images) from the request path via a message queue (SQS/Kafka/RabbitMQ) + worker pool — improves perceived latency and resilience to load spikes.
- **Idempotency keys**: client-supplied unique ID per logical operation so retried requests (network blips) don't double-execute a non-idempotent action like "charge card" or "create order."
- **CDN**: cache static/semi-static content at edge locations close to users — cuts latency and origin load.

### 5.3 Classic SDE-1-Level Design Prompts (be ready to talk through these)
- **URL Shortener**: ID generation (auto-increment + base62 encoding, or a pre-generated key pool, vs hashing — discuss collision handling), read-heavy → cache layer, redirect via 301/302 (cache implications of each).
- **Rate Limiter**: algorithm choice, where it lives (API gateway vs app), distributed counting via Redis.
- **Pastebin / Notes app**: similar to URL shortener; add TTL/expiry handling.
- **Key-Value Store**: partitioning, replication, consistency model, simple LSM-tree vs B-tree storage engine awareness.
- **Notification/Chat-lite system**: fan-out on write vs fan-out on read trade-off (push vs pull model for delivering updates to many recipients).
- **Parking lot / library system (OOD-flavored)**: class design, SOLID principles, less about distributed scale, more about clean object modeling — sometimes asked instead of a "scale" system design at SDE-1.

### 5.4 Common Interview Questions
1. **How do you decide between SQL and NoSQL for a given system?** SQL when you need strong consistency, complex relational queries/joins, and well-defined schema; NoSQL (document/KV/wide-column) when you need horizontal write scalability, flexible schema, or a specific access pattern (e.g., DynamoDB for single-key lookups at huge scale).
2. **What is a single point of failure (SPOF) here, and how do you remove it?** Generic but important — show you can scan your own diagram for SPOFs (single LB, single DB instance, single cache node) and propose redundancy (multi-AZ, replicas, multiple LB instances behind DNS/anycast).
3. **How would you estimate storage for 10M daily active users posting 1 photo/day at 2MB each?** ~20TB/day raw — walk through the math out loud; this is what's actually being graded, not the final number.
4. **Strong vs eventual consistency — give a real example of each being the right choice.** Strong: bank balance after a transfer. Eventual: like counts/follower counts on a social post — slight staleness is fine for the UX.

### 5.5 Cheat Sheet
| Concept | One-liner |
|---|---|
| Read replica | Scales reads; replication lag = staleness risk |
| Sharding | Scales writes; shard key choice is the hard part |
| Consistent hashing | Minimal remap on node add/remove |
| CAP | Pick C or A under a partition; PACELC for the no-partition case |
| Token bucket | Allows bursts up to bucket size, refills steadily |
| Idempotency key | Makes retried non-idempotent requests safe |
| Fan-out on write | Precompute per-recipient feed — fast read, expensive write |
| Fan-out on read | Compute feed at read time — cheap write, expensive read |

---

## 6. Microservices

### 6.1 What & Why
Microservices decompose a system into independently deployable services, each owning its own data and business capability, communicating over the network. Trade-off vs a monolith: better team autonomy, independent scaling/deployment, fault isolation — at the cost of operational complexity, network latency, distributed-systems failure modes, and harder cross-service transactions/debugging.

### 6.2 Service Decomposition
- Decompose around **bounded contexts** / business capabilities (Domain-Driven Design), not technical layers (don't make "the database service" and "the validation service").
- Each service should own its own database/schema — no shared DB across services (avoids hidden coupling); cross-service data needs go through APIs or events.

### 6.3 Communication Patterns
| Style | Examples | Pros | Cons |
|---|---|---|---|
| Sync request/response | REST, gRPC | Simple mental model, immediate response | Caller blocked, cascading failures, tight runtime coupling |
| Async messaging | Kafka, RabbitMQ, SQS | Decoupled, resilient to downstream outages, natural backpressure handling | Eventual consistency, harder to reason about ordering/debugging |

- **gRPC vs REST**: gRPC uses HTTP/2 + Protobuf — strongly typed contracts, smaller/faster payloads, built-in streaming; good for internal service-to-service calls. REST/JSON: more universally compatible, human-readable, easier for public/browser-facing APIs.

### 6.4 Key Patterns
- **API Gateway**: single entry point for clients; handles routing, auth, rate limiting, request aggregation — avoids exposing every internal service directly.
- **Service Discovery**: services register themselves (or are registered) so callers can find current instances/IPs dynamically (e.g., Consul, Eureka, or Kubernetes' built-in DNS-based discovery) — needed because instances scale up/down and IPs change.
- **Circuit Breaker** (e.g., Hystrix/resilience4j pattern): stop calling a downstream service that's failing/slow, fail fast instead of piling up threads/connections waiting — has Closed → Open → Half-Open states, auto-probing recovery.
- **Retry with backoff + jitter**: handle transient failures, but must be paired with idempotency on the callee and capped attempts to avoid amplifying load during an outage ("retry storm").
- **Bulkhead**: isolate resource pools (e.g., separate thread/connection pools per downstream dependency) so one slow dependency can't exhaust resources needed by others.
- **Saga pattern**: manage a multi-service business transaction without a 2-phase-commit distributed transaction — either **orchestration** (a central coordinator tells each service what to do next) or **choreography** (each service reacts to events from others). Compensating transactions undo prior steps on failure (e.g., "refund payment" if "reserve inventory" later fails).
- **CQRS** (Command Query Responsibility Segregation): separate write model and read model, often with the read model as a denormalized projection kept in sync via events — common alongside event sourcing in microservice architectures.
- **Strangler fig pattern**: incrementally migrate a monolith to microservices by routing specific functionality to new services while the old monolith still handles the rest, until it's fully replaced.

### 6.5 Distributed Transactions & Data Consistency
- 2-phase commit (2PC) exists but is rarely used in practice across microservices — it's blocking, doesn't scale well, and a coordinator failure can leave participants hanging.
- Sagas + eventual consistency are the practical default; the trade-off to articulate clearly: you give up atomicity for availability/scalability, and need compensating actions for partial failure.
- **Outbox pattern**: write the DB change and the "event to publish" in the same local transaction (an outbox table), then a separate process reliably publishes from the outbox to the message broker — solves the "dual write" problem (DB write succeeds but event publish fails, or vice versa).

### 6.6 Observability (a frequent SDE-1/2 follow-up area)
- **Distributed tracing** (OpenTelemetry, Jaeger, Zipkin): a trace ID propagated across service calls so you can see the full request path and where latency/errors occurred.
- **Centralized logging**: structured logs shipped to a central store (ELK/Datadog) with correlation IDs, since `ssh`-ing into individual instances doesn't scale.
- **Metrics**: RED method (Rate, Errors, Duration) per service, or the four golden signals (latency, traffic, errors, saturation).

### 6.7 Common Interview Questions
1. **Monolith vs microservices — when would you NOT recommend microservices?** Small team, early-stage product with unclear domain boundaries, low traffic — microservices add operational overhead (deployment, monitoring, network reliability) that isn't justified yet; "premature microservices" is a real anti-pattern interviewers like to hear you flag.
2. **How do you handle a transaction spanning two services (e.g., place order + reserve inventory)?** Saga with compensating actions, or redesign so the boundary doesn't require cross-service atomicity in the first place.
3. **How do you prevent cascading failures when a downstream service is slow?** Circuit breaker + timeouts + bulkheads + load shedding, rather than letting requests queue up indefinitely.
4. **How does service discovery work in Kubernetes specifically?** Each Service gets a stable DNS name and ClusterIP; kube-proxy/iptables (or IPVS) load-balances to healthy pod endpoints — no external registry needed for the common case.
5. **What's the "dual write" problem and how do you solve it?** Writing to your DB and publishing an event are two separate operations that can fail independently, causing inconsistency; solve with the outbox pattern (or CDC reading the DB's write-ahead log, e.g., Debezium).

### 6.8 Cheat Sheet
| Concept | One-liner |
|---|---|
| API Gateway | Single client-facing entry point |
| Circuit breaker | Fail fast on a sick dependency instead of piling up |
| Saga | Multi-service transaction via local steps + compensation |
| Outbox pattern | Atomic "DB write + event" via local transaction + relay |
| CQRS | Separate write/read models |
| Strangler fig | Incremental monolith → microservices migration |
| Bulkhead | Isolated resource pools per dependency |

---

## 7. AWS Basics (EC2, S3, RDS, IAM)

### 7.1 EC2 (Elastic Compute Cloud)
- Virtual machines in the cloud; choose **instance type** based on workload (e.g., `t3` burstable general purpose, `m-series` general purpose, `c-series` compute-optimized, `r-series` memory-optimized).
- **Security Groups**: stateful, instance-level virtual firewalls — allow rules only (no explicit deny needed; return traffic is automatically allowed).
- **NACLs (Network ACLs)**: stateless, subnet-level, support explicit allow *and* deny rules, evaluated in rule-number order. (SG vs NACL is a classic comparison question.)
- **Auto Scaling Groups (ASG)**: scale instance count based on metrics (CPU, custom CloudWatch metrics) or schedules; works with a Launch Template defining instance config.
- **Elastic Load Balancer (ELB)**: ALB (L7, HTTP/HTTPS, path/host-based routing) vs NLB (L4, TCP/UDP, ultra-low latency, static IP) vs the older CLB.
- **EBS vs Instance Store**: EBS = persistent network-attached block storage (survives stop/terminate if configured); instance store = ephemeral, physically attached, lost on stop/terminate.
- **Spot vs On-Demand vs Reserved**: Spot = cheapest, can be reclaimed by AWS with short notice (good for fault-tolerant/batch workloads); On-Demand = pay-as-you-go, no commitment; Reserved/Savings Plans = discount for committing to usage over 1-3 years.

### 7.2 S3 (Simple Storage Service)
- Object storage (not a filesystem/block device) — key-value, virtually unlimited scale, 11 9's durability (data replicated across multiple AZs within a region).
- **Storage classes**: Standard (frequent access) → Standard-IA / One Zone-IA (infrequent access, cheaper, retrieval cost) → Glacier / Glacier Deep Archive (archival, cheapest storage, slow/costly retrieval) → Intelligent-Tiering (auto-moves objects between tiers based on access patterns).
- **Versioning**: keeps every version of an object on overwrite/delete (delete becomes a "delete marker," not permanent removal) — protects against accidental deletes/overwrites, but increases storage cost.
- **Lifecycle policies**: automatically transition objects between storage classes or expire/delete them after N days — used to control cost over time.
- **Consistency model**: S3 now provides strong read-after-write consistency for all operations (this changed from the older "eventual consistency" model — worth knowing it's no longer the right answer to "S3 is eventually consistent").
- **Access control**: bucket policies (resource-based, JSON), IAM policies (identity-based), and ACLs (legacy, mostly discouraged now) — and **Block Public Access** settings as a safety net.

### 7.3 RDS (Relational Database Service)
- Managed relational DB (Postgres, MySQL, MariaDB, SQL Server, Oracle, or Aurora — AWS's own MySQL/Postgres-compatible engine with better performance/availability) — AWS handles patching, backups, failover.
- **Multi-AZ deployment**: synchronous standby replica in a different AZ for **high availability/failover** (not for read scaling) — automatic failover on primary failure.
- **Read Replicas**: asynchronous, for **read scaling**, can be cross-region; promotable to standalone if needed, but not used for automatic failover by default.
- **Aurora** specifically: storage layer is decoupled and replicated across AZs automatically (6 copies across 3 AZs), supports up to 15 low-latency read replicas, generally higher throughput than standard RDS engines.
- **Backups**: automated daily snapshots + transaction log backups enabling point-in-time recovery within the retention window, plus manual snapshots you control independently.

### 7.4 IAM (Identity and Access Management)
- **Users**: long-term credentials for a person/service (avoid for workloads — prefer roles).
- **Groups**: collections of users sharing permissions.
- **Roles**: temporary credentials assumed by a user, service, or resource (e.g., an EC2 instance assumes a role to call S3 without storing static keys) — the recommended way to grant permissions to compute resources.
- **Policies**: JSON documents defining allowed/denied actions on resources; can be attached to users, groups, or roles. **Principle of least privilege**: grant only the permissions actually needed.
- **Policy evaluation logic**: an explicit `Deny` always wins; otherwise at least one applicable `Allow` is required, and with no matching statement the default is implicit deny.

### 7.5 Common Interview Questions
1. **Security Group vs NACL?** SG = stateful, instance-level, allow-only. NACL = stateless, subnet-level, allow + deny, ordered rules.
2. **When would you use an IAM Role instead of an IAM User with access keys for an application running on EC2?** Always prefer Roles for workloads — no long-lived credentials to leak/rotate, automatically refreshed temporary credentials via the instance metadata service.
3. **Read replica vs Multi-AZ — what's each one actually for?** Multi-AZ = HA/failover (sync standby, not for scaling reads by default in most engines). Read replica = read scaling (async, can serve reads, can lag).
4. **How would you serve a static website cheaply and reliably at global scale on AWS?** S3 (static hosting) + CloudFront (CDN) in front of it, optionally + Route 53 for DNS — no servers to manage.
5. **What does "eventual vs strong consistency" mean for S3 today?** As of the Dec 2020 update, S3 provides strong read-after-write consistency for all requests, including overwrite PUTs and DELETEs — there's no longer a stale-read window to design around.

### 7.6 Cheat Sheet
| Concept | One-liner |
|---|---|
| Security Group | Stateful, instance-level, allow-only |
| NACL | Stateless, subnet-level, allow+deny |
| S3 storage classes | Standard → IA → Glacier, by access frequency |
| Multi-AZ RDS | HA/failover, sync standby |
| Read replica | Read scaling, async, can lag |
| IAM Role | Temporary creds — preferred over static IAM user keys for workloads |
| Least privilege | Grant only what's needed, nothing more |

---

## 8. Kubernetes (Basics)

### 8.1 Why Kubernetes
Orchestrates containers across a cluster of machines: scheduling, self-healing (restart/reschedule failed containers), scaling, service discovery, and rolling updates — solves the "now I have 200 containers across 30 machines" problem that plain Docker doesn't address.

### 8.2 Core Objects
- **Pod**: smallest deployable unit — one or more tightly-coupled containers sharing network namespace (same IP/localhost) and optionally storage. Pods are ephemeral and disposable; you rarely create them directly.
- **Deployment**: manages a ReplicaSet of identical Pods, handles rolling updates and rollbacks, declarative desired-state ("I want 5 replicas of this image").
- **ReplicaSet**: ensures N pod replicas are running at all times (usually managed indirectly via Deployment).
- **Service**: stable virtual IP + DNS name that load-balances to a dynamic set of Pods (selected via labels) — solves the "pod IPs change constantly" problem.
  - `ClusterIP` (default, internal-only), `NodePort` (exposes a port on every node), `LoadBalancer` (provisions a cloud LB), `ExternalName` (DNS alias to an external service).
- **Ingress**: L7 HTTP(S) routing into the cluster (host/path-based routing, TLS termination) — needs an Ingress Controller (nginx, ALB controller, etc.) to actually do the work; the Ingress resource is just the config.
- **ConfigMap / Secret**: externalize configuration / sensitive values from the image; injected as env vars or mounted files. Secrets are base64-encoded (not encrypted by default at rest unless you enable encryption-at-rest/KMS integration — a common misconception to clarify in interviews).
- **Namespace**: logical partitioning of cluster resources (multi-tenancy, environment separation) — most objects are namespace-scoped.
- **StatefulSet**: like a Deployment but for stateful workloads needing stable network identity and stable storage per pod (e.g., databases) — pods get predictable names (`pod-0`, `pod-1`...) and ordered, stable rollout.
- **DaemonSet**: runs exactly one pod per node (or matching subset) — for node-level agents (log collectors, monitoring agents).
- **Job / CronJob**: run-to-completion tasks, one-off or scheduled.

### 8.3 Architecture
- **Control Plane**: API server (front door for all cluster operations), etcd (distributed key-value store holding all cluster state), scheduler (assigns pods to nodes based on resource requests/constraints), controller manager (reconciliation loops that drive actual state → desired state).
- **Node components**: kubelet (agent that ensures containers described in PodSpecs are running on its node), kube-proxy (implements Service networking rules), container runtime (containerd/CRI-O).
- **Reconciliation loop** is the core mental model for almost everything in K8s: controllers continuously compare desired state (from etcd, via the API server) to observed state and act to converge them — not a one-shot imperative action.

### 8.4 Scaling & Scheduling
- **Horizontal Pod Autoscaler (HPA)**: scales replica count based on CPU/memory or custom metrics.
- **Resource requests vs limits**: `requests` = guaranteed minimum (used by scheduler for placement); `limits` = hard ceiling (pod gets throttled on CPU, or OOM-killed on memory, if it exceeds limit). Setting these correctly is a real-world interview favorite ("what happens if you don't set limits?" → noisy-neighbor risk, one pod can starve others on the node).
- **Liveness vs Readiness probes**: liveness failing → kubelet restarts the container; readiness failing → pod is removed from Service endpoints (traffic stops routing to it) but it's *not* restarted — a subtle but commonly tested distinction.
- **Rolling updates**: Deployment gradually replaces old-version pods with new-version pods (`maxSurge`/`maxUnavailable` control the pace), enabling zero-downtime deploys with easy rollback (`kubectl rollout undo`).

### 8.5 Common Interview Questions
1. **Liveness vs readiness probe — what's the practical difference?** Liveness = "is this container alive, restart it if not." Readiness = "is this container ready for traffic right now," temporarily pulling it from load balancing without killing it (useful during slow startup/warm-up or temporary downstream unavailability).
2. **What happens when a node dies?** The control plane (via node controller) detects the missed heartbeats, marks the node NotReady, and after a grace period reschedules the node's pods onto healthy nodes (assuming they're managed by a Deployment/ReplicaSet/etc., not bare Pods).
3. **How does a Service find the right Pods?** Label selectors — the Service watches for Pods matching its selector and updates its Endpoints/EndpointSlice accordingly; kube-proxy programs the actual packet-forwarding rules.
4. **Why use a Deployment instead of creating Pods directly?** Self-healing (recreates failed pods), declarative scaling, rolling updates with history/rollback — a bare Pod that dies just stays dead.
5. **What's the difference between a ConfigMap and a Secret if Secrets aren't even encrypted by default?** Mostly intent/handling conventions (K8s treats Secrets specially — e.g., not printed in some `describe` outputs, can integrate with external secret managers/KMS encryption-at-rest) — for real security you should layer on encryption at rest and/or an external secret store (Vault, AWS Secrets Manager) rather than relying on base64 alone.
6. **How would you debug a Pod stuck in `Pending`?** `kubectl describe pod` → check Events for scheduling failures: insufficient resources on any node, unsatisfied node affinity/taints, no PV available for a PVC, image pull errors.

### 8.6 Cheat Sheet
| Concept | One-liner |
|---|---|
| Pod | Smallest unit; ephemeral |
| Deployment | Declarative desired state + rolling updates for stateless pods |
| Service | Stable VIP/DNS load-balancing to pods via label selector |
| Ingress | L7 HTTP routing in, needs a controller |
| StatefulSet | Stable identity/storage per pod, for stateful workloads |
| etcd | Source of truth for all cluster state |
| Liveness probe | Restart if failing |
| Readiness probe | Pull from traffic if failing (no restart) |
| requests/limits | Scheduling guarantee vs hard ceiling |

---

## 9. CI/CD (GitHub Actions / Jenkins)

### 9.1 Core Concepts
- **CI (Continuous Integration)**: every code change is automatically built and tested, catching integration issues early — the discipline of merging small, frequent changes against an automated safety net.
- **CD**: **Continuous Delivery** (every change is automatically prepared for release, but a human triggers the final deploy) vs **Continuous Deployment** (every passing change is automatically deployed to production with no manual gate) — a frequently tested distinction.
- **Pipeline stages** (typical): checkout → install deps → lint/static analysis → unit tests → build artifact/image → integration tests → security/dependency scan → push artifact to registry → deploy → smoke test/verify.

### 9.2 GitHub Actions
- **Workflow**: a YAML file in `.github/workflows/`, triggered by **events** (`push`, `pull_request`, `schedule` (cron), `workflow_dispatch` (manual), `release`, etc.).
- **Job**: a set of steps that run on a runner (GitHub-hosted or self-hosted); jobs run in parallel by default unless you set `needs:` to create dependencies.
- **Step**: an individual command or a reusable **Action** (`uses: actions/checkout@v4`).
- **Runner**: the VM/container executing the job — GitHub-hosted (ephemeral, various OS images) or self-hosted (your own infra, needed for special hardware/network access).
- **Secrets**: stored encrypted at the repo/org/environment level, injected as env vars — never hard-code credentials in the workflow.
- **Matrix builds**: run the same job across a matrix of variables (e.g., multiple Node/OS versions) in parallel.
- **Caching** (`actions/cache`): persist dependency caches (e.g., `node_modules`, pip cache) between runs to speed up builds.
- **Environments + required reviewers**: gate a deploy job behind manual approval for sensitive environments (e.g., production).

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - run: npm ci
      - run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: ./deploy.sh
        env:
          API_KEY: ${{ secrets.API_KEY }}
```

### 9.3 Jenkins
- **Jenkinsfile**: pipeline-as-code, either **Declarative** (structured `pipeline { stages { stage { steps {...} } } } }`, easier to read/lint) or **Scripted** (Groovy DSL, more flexible/imperative).
- **Agents/Nodes**: machines (or containers/pods if using the Kubernetes plugin) that actually execute pipeline steps; Jenkins controller schedules work to them.
- **Plugins ecosystem**: Jenkins's biggest strength and weakness — huge flexibility/integrations, but plugin maintenance/version drift is a real operational cost (a fair point to raise if asked "Jenkins vs GitHub Actions").
- **Blue-Green / parameterized / multibranch pipelines**: Jenkins supports multibranch pipeline jobs that auto-discover branches/PRs and run a Jenkinsfile per branch.

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps { sh 'npm ci && npm run build' }
    }
    stage('Test') {
      steps { sh 'npm test' }
    }
    stage('Deploy') {
      when { branch 'main' }
      steps { sh './deploy.sh' }
    }
  }
  post {
    failure {
      mail to: 'team@example.com', subject: 'Build failed', body: "${env.BUILD_URL}"
    }
  }
}
```

### 9.4 Deployment Strategies (commonly asked regardless of tool)
| Strategy | How | Pros | Cons |
|---|---|---|---|
| Rolling update | Gradually replace old instances with new | No extra infra, default in K8s | Brief mixed-version window |
| Blue-Green | Full second environment, switch traffic at once | Instant rollback (switch back), no mixed versions | Double infra cost during cutover |
| Canary | Route a small % of traffic to new version, ramp up if healthy | Limits blast radius, real-traffic validation | Needs good metrics/automation to decide ramp/rollback |
| Feature flags | Ship code dark, toggle behavior at runtime independent of deploy | Decouples deploy from release, instant kill switch | Flag debt if not cleaned up |

### 9.5 Branching & Trunk-Based Development
- **GitFlow**: long-lived `develop`/`release`/`feature` branches — more structure, more merge overhead, slower integration.
- **Trunk-based development**: short-lived feature branches merged frequently into `main`, gated by CI + feature flags for anything not ready for users — generally preferred at high-velocity orgs because it keeps integration pain low.
- **Required checks / branch protection**: block merging to `main` unless CI passes and (often) a review is approved — the actual mechanism that makes CI meaningful as a gate, not just a status badge.

### 9.6 Common Interview Questions
1. **Continuous Delivery vs Continuous Deployment?** Delivery = always release-ready, human approves the final push. Deployment = no human gate, every green build ships automatically.
2. **How do you keep secrets out of your CI pipeline logs/config?** Use the CI platform's encrypted secret store (GitHub Secrets, Jenkins Credentials), never echo secret values in scripts, mask/redact in logs, scope secrets to the minimum environment/job that needs them.
3. **How would you speed up a slow CI pipeline?** Parallelize independent jobs/tests, cache dependencies, only run affected test suites (if monorepo tooling supports it), use faster/larger runners, fail fast (lint before slow integration tests).
4. **What's a canary deployment and how do you decide when to roll it forward or back?** Route a small traffic percentage to the new version, monitor error rate/latency/business metrics against a baseline, auto- or manually promote if healthy, automatically roll back on regression past a threshold.
5. **How do you make a deployment safely rollback-able?** Keep the previous artifact/image versioned and deployable, use a strategy with fast traffic-switch (blue-green) or simple revert (`kubectl rollout undo`, redeploy previous tag), and decouple risky logic behind a feature flag so you can disable behavior without a full redeploy.

### 9.7 Cheat Sheet
| Concept | One-liner |
|---|---|
| CI | Auto build+test on every change |
| Continuous Delivery | Always deployable; human triggers release |
| Continuous Deployment | Fully automatic release on green build |
| GitHub Actions job | Parallel by default; `needs:` for ordering |
| Jenkinsfile (declarative) | Structured pipeline-as-code |
| Canary | Small % traffic first, ramp on healthy signal |
| Blue-Green | Full second env, instant switch + rollback |
| Trunk-based dev | Short-lived branches, frequent merges to main |

---

## Final Pre-Interview Recap (1-Page Skim)

| Topic | If you remember ONE thing |
|---|---|
| Kafka | Ordering is per-partition only; `acks=all` + idempotent producer is your durability story |
| Redis | Know cache-aside vs write-through, and how you'd fix a cache stampede |
| Docker | Multi-stage builds for small images; `depends_on` ≠ "is ready" |
| SQL Indexing | Composite index leftmost-prefix rule; covering index = no table lookup |
| System Design | Estimate → API → data model → diagram → bottleneck deep-dive, in that order |
| Microservices | Saga + outbox pattern is how you avoid 2PC and the dual-write problem |
| AWS | SG = stateful/allow-only, NACL = stateless/allow+deny; Role > static keys |
| Kubernetes | Liveness restarts, readiness reroutes traffic — don't mix these up |
| CI/CD | Delivery = human gate, Deployment = no gate; canary limits blast radius |

### How to Use This Doc
- Do one topic a day, working through the Q&A out loud (not just reading) — interviewers are evaluating how you *explain* things, not just whether you know them.
- For every topic, be ready for the natural follow-up: **"how would you actually implement/debug this?"** — most of the Q&A sections above are written with that follow-up already baked in.
- Pair this with one mock system-design session and one Kafka/Redis-focused "deep dive" round, since those are the two most heavily weighted topics here (⭐⭐⭐⭐⭐ in your list) and the ones most likely to get a dedicated round rather than a 10-minute slice of a broader interview.

