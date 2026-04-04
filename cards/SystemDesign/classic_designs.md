Q: How would you design a URL shortener (e.g., bit.ly)?
A: Core: generate a short key (base62 encode a counter or use MD5 hash prefix), store key→long URL in a KV store (Redis + persistent DB). Reads: cache hot URLs in Redis, serve 301/302 redirects. Scale: shard by key hash, CDN for edge redirects. Key questions: custom aliases, analytics (click tracking), expiry.

---

Q: How would you design a rate limiter?
A: Store counters in Redis (fast atomic ops). Algorithm: sliding window counter (Redis sorted set by timestamp, count entries in last N seconds) or token bucket (store tokens + last refill time, refill on each request). Distribute via API gateway. Handle Redis failure: fail open (allow) or fail closed (deny) — choose based on risk profile.

---

Q: How would you design a distributed unique ID generator (like Twitter Snowflake)?
A: Snowflake format: 41-bit timestamp (ms) + 10-bit machine ID + 12-bit sequence. Fits in a 64-bit int, time-ordered, no coordination needed, 4096 IDs/ms per machine. Alternatives: UUID v4 (not sortable), database sequences (bottleneck), ULIDs (sortable UUID).

---

Q: How would you design a news feed (e.g., Twitter/Instagram timeline)?
A: Two models: Fan-out on write (push new posts to all followers' feed caches on publish — fast reads, expensive for celebrities), Fan-out on read (pull and merge followed users' posts on request — fresh but slow). Hybrid: push to regular users, pull-merge for high-follower accounts. Store feeds in Redis sorted sets by timestamp.

---

Q: How would you design a notification system?
A: Write path: events → Kafka → notification service (groups, deduplicates) → workers per channel (push, email, SMS). Delivery: use APNs/FCM for mobile push, SendGrid for email, Twilio for SMS. Reliability: at-least-once via queues + idempotency keys to dedup at delivery. Store notification history in a NoSQL store for inbox view.

---

Q: How would you design a distributed key-value store?
A: Use consistent hashing to partition keys across nodes. Replicate each key to N nodes (e.g., 3). Reads/writes use quorum (W + R > N). Conflict resolution: last-write-wins (timestamp) or vector clocks. Gossip protocol for membership. Compaction/LSM for storage. Modeled on Amazon Dynamo.

---

Q: How would you design a search autocomplete system?
A: Store all prefixes in a trie (in-memory) or use Elasticsearch prefix queries. For top-N suggestions: store frequency counts, use a min-heap per trie node. Scale: partition trie by first character, cache top-N per prefix in Redis with TTL. Freshness: async update from query logs via batch or stream.

---

Q: How would you design a web crawler?
A: Components: URL frontier (queue of URLs to visit), fetcher (HTTP workers), parser (extract links + content), dedup store (Bloom filter or URL hash set), storage (raw HTML + extracted data). Scale: distributed queue (Kafka), politeness (respect robots.txt, rate limit per domain), priority queue (BFS for breadth, priority for important pages).

---

Q: How would you design a chat system (e.g., WhatsApp)?
A: Real-time: WebSocket connections between client and chat server. Message flow: sender → chat server → Kafka → receiver's chat server → WebSocket push. Storage: messages in Cassandra (time-series, append-only, partition by conversation). Offline delivery: store undelivered messages, push on reconnect. Group chat: fan-out to all member servers.

---

Q: How would you design a ride-sharing system (e.g., Uber)?
A: Core: location service (drivers push GPS every 4s → Redis geo index), matching service (find nearest available drivers within radius), trip service (FSM: requested → accepted → in-progress → completed). Dispatch: geohash regions, consistent hashing. Supply/demand: surge pricing algorithm. Scale: separate write path (location updates) from read path (rider queries nearby drivers).

---

Q: How would you design a video streaming platform (e.g., YouTube)?
A: Upload: chunked upload to object storage, async transcoding pipeline (multiple resolutions via FFmpeg workers). CDN: distribute video chunks globally, use adaptive bitrate streaming (HLS/DASH). Metadata: video info in SQL, view counts in Redis (periodic flush to DB). Recommendations: offline ML job, store results in a fast KV store.

---

Q: How would you design a distributed cache (e.g., Redis Cluster)?
A: Partition keyspace across nodes using hash slots (Redis uses 16384 slots). Each node owns a range of slots. Replication: each primary has N replicas. Failover: replicas elect a new primary via Raft-like voting when primary is unreachable. Client routes to the correct node using cluster map (MOVED redirect on wrong node).

---

C: In any system design interview, structure your answer: [requirements] → [capacity estimation] → [high-level design] → [deep dive on bottlenecks] → [trade-offs]. Explicitly state assumptions and ask clarifying questions before drawing the architecture.
