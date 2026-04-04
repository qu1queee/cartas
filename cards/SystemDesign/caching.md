Q: What are the four main cache invalidation strategies?
A: TTL (time-based expiry), Event-driven invalidation (write triggers cache bust), Cache-aside (application checks cache, falls back to DB on miss), Write-through (write to cache and DB simultaneously).

---

Q: What is the difference between cache-aside and read-through caching?
A: Cache-aside: application code manages cache — checks cache, reads DB on miss, populates cache. Read-through: cache sits in front of DB and handles misses automatically. Read-through simplifies code but requires a capable cache layer.

---

Q: What is write-through vs write-back (write-behind) caching?
A: Write-through: write to cache and DB synchronously — strong consistency, higher write latency. Write-back: write to cache only, flush to DB asynchronously — lower write latency, risk of data loss on cache failure.

---

Q: What is a cache stampede (dog-pile) and how do you prevent it?
A: When a cached item expires and many requests simultaneously hit the backend. Prevention: use a mutex/lock on cache miss (request coalescing), probabilistic early expiration, or background refresh before TTL expires.

---

Q: What is cache eviction and what are the common policies?
A: Removing items when the cache is full. Policies: LRU (least recently used — most common), LFU (least frequently used), FIFO (first in first out), TTL-based, Random. LRU is the default in Redis.

---

Q: When would you use a local in-process cache vs a distributed cache (Redis)?
A: Local cache: ultra-low latency, no network hop, good for immutable/rarely-changing data. Distributed cache: consistent data across multiple service instances, supports larger datasets, required for stateless services.

---

Q: What is cache warm-up and why does it matter?
A: Pre-populating a cache before traffic hits it. Matters after deploys or failover — a cold cache causes a storm of backend requests. Strategies: lazy warming (first requests fill it), proactive warming (scheduled job pre-loads hot data).

---

Q: What is the difference between a CDN and an application cache?
A: CDN caches static/semi-static content at edge nodes geographically close to users — reduces latency for assets, HTML, APIs. Application cache (Redis/Memcached) sits inside your infrastructure to reduce DB load for dynamic data.

---

Q: How do you handle cache consistency in a write-heavy multi-region system?
A: Accept eventual consistency and use short TTLs, or use write-through + publish invalidation events via pub/sub so all regions bust stale cache entries on writes. Strong consistency across regions requires synchronous replication which is expensive.

---

Q: What data is NOT a good candidate for caching?
A: Frequently mutated data (high invalidation cost), data requiring strong consistency (financial balances), unique per-user data at high cardinality (no reuse benefit), data that is faster to compute than retrieve from cache.

---

C: [Cache hit ratio] = hits / (hits + misses). A ratio below [0.9] in a high-traffic system often indicates a sizing, TTL, or key-design problem worth investigating.
