Q: What are the key numbers every system designer should know?
A: L1 cache: ~1ns, L2 cache: ~4ns, RAM read: ~100ns, SSD random read: ~100μs, HDD seek: ~10ms, network round trip (same DC): ~0.5ms, cross-continental round trip: ~150ms.

---

Q: How do you estimate QPS (queries per second) from DAU?
A: Assume ~10% of DAU are active in peak hour, and each active user makes ~N requests. QPS = DAU × 0.1 × N / 3600. Example: 10M DAU, 5 requests each → peak QPS ≈ 1,400. Add 2-3× headroom for spikes.

---

Q: How do you size storage for a write-heavy system?
A: Estimate: rows/day × row size (bytes) × 365 × replication factor × retention years. Example: 1M writes/day × 1KB × 365 × 3 replicas × 5 years = ~5.5 TB.

---

Q: What is a reasonable read:write ratio for common systems?
A: Social feed: ~100:1 read-heavy. Twitter-like: ~1000:1. E-commerce product page: ~500:1. Logging/metrics ingestion: write-heavy, ~1:10. Use the ratio to decide where to invest in optimization.

---

Q: How do you estimate bandwidth requirements?
A: Peak QPS × average response size. Example: 10,000 QPS × 10KB = 100 MB/s = 800 Mbps. Check against typical 10 Gbps NIC — if close, consider compression, CDN offload, or response pagination.

---

Q: How do you estimate memory needed for a cache?
A: Identify the hot dataset (typically 20% of data handles 80% of traffic per Pareto). Cache size = hot dataset size. Example: 10TB total, 20% hot = 2TB. Distribute across Redis nodes (e.g., 10 × 256GB nodes).

---

Q: What is a useful rule of thumb for database connections per service?
A: Each service instance typically uses 10-100 DB connections. At 100 instances × 50 connections = 5,000 connections — near the limit of PostgreSQL defaults (~100-500). Use a connection pooler (PgBouncer) to multiplex.

---

Q: How do you estimate partition count in Kafka?
A: Target throughput / per-partition throughput. A single Kafka partition handles ~10-100 MB/s. E.g., 1 GB/s target → 10-100 partitions. Align partition count with desired consumer parallelism. More partitions = more overhead.

---

Q: What is the 80/20 rule applied to system design?
A: 80% of traffic comes from 20% of content/users. Design the hot path for the 20% (cache aggressively, optimize reads). The cold path (80% of content, 20% of traffic) can afford slower, cheaper storage.

---

C: Quick size references: 1 char = 1 byte, UUID = 36 bytes, Unix timestamp = 4 bytes, average URL = ~100 bytes, thumbnail = ~100KB, photo = ~1MB, video minute = ~50MB. Use these to estimate row and object sizes quickly.
