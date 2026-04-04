Q: When would you choose SQL over NoSQL for a new system?
A: Choose SQL when you need ACID transactions, complex joins/queries, or a well-defined relational schema. NoSQL wins for massive scale writes, flexible schemas, low-latency key-value access, or document/graph data models.

---

Q: What are the four main NoSQL data models and a use case for each?
A: Key-value (session store, cache), Document (user profiles, catalogs), Column-family (time-series, IoT), Graph (social networks, fraud detection).

---

Q: What is database sharding and what are its trade-offs?
A: Splitting a dataset across multiple nodes by a shard key. Enables horizontal write scale but adds complexity: cross-shard queries are expensive, resharding is painful, joins require scatter-gather.

---

Q: What are the three common sharding strategies?
A: Range-based (easy range queries, hot spot risk), Hash-based (even distribution, no range queries), Directory-based (flexible, single point of failure in the lookup service).

---

Q: What is a hotspot in sharding and how do you mitigate it?
A: A shard receiving disproportionately more traffic (e.g., all celebrity writes go to the same user shard). Mitigations: consistent hashing with virtual nodes, adding a random suffix to hot keys, or routing hot keys to dedicated shards.

---

Q: What is consistent hashing and why is it used?
A: A technique mapping keys and nodes onto a ring so adding/removing a node only remaps ~k/n keys (k=total keys, n=nodes). Minimizes cache invalidation and data movement during scaling events.

---

Q: What is the difference between read replicas and multi-master replication?
A: Read replicas: one primary accepts writes, replicas serve reads — simple but reads may lag. Multi-master: multiple nodes accept writes — higher availability but conflict resolution is complex.

---

Q: What is replication lag and when does it cause problems?
A: The delay between a write on the primary and it appearing on replicas. Causes stale reads ("read your own writes" violation), and can lead to data loss if primary fails before replication completes.

---

Q: What is the difference between synchronous and asynchronous replication?
A: Synchronous: primary waits for replica acknowledgment before committing — strong durability, higher latency. Asynchronous: primary commits immediately — lower latency, risk of data loss on primary failure.

---

Q: What is a write-ahead log (WAL)?
A: A log that records every change before it is applied to the main data store. Enables crash recovery, replication, and point-in-time recovery. Used in PostgreSQL, Kafka, and most ACID databases.

---

Q: What is a column-family store (e.g., Cassandra, HBase) optimized for?
A: High write throughput and time-series/append workloads. Uses LSM trees: writes go to an in-memory buffer (memtable), then flush to SSTables on disk. Reads may need to check multiple SSTables.

---

Q: What is an LSM tree vs a B-tree index?
A: LSM (Log-Structured Merge): writes go to memory + sequential log, merged periodically — fast writes, slower reads. B-tree: in-place updates on a balanced tree — fast reads, more write amplification. Cassandra uses LSM; PostgreSQL uses B-tree.

---

Q: What is a time-series database and when should you use one?
A: A database optimized for append-only sequential writes keyed by timestamp (e.g., InfluxDB, TimescaleDB, Prometheus). Use for metrics, monitoring, IoT sensor data — offers efficient downsampling and retention policies.

---

Q: What is data denormalization and when is it the right choice?
A: Storing redundant data to avoid expensive joins. Right choice when read performance is critical and write volume is manageable, or in NoSQL stores that don't support joins. Trade-off: consistency overhead on writes.

---

Q: What is the difference between OLTP and OLAP workloads?
A: OLTP (transactional): many short read/write operations on individual rows — optimized for throughput and latency. OLAP (analytical): few complex queries over large datasets — optimized for aggregation, uses columnar storage.

---

C: [Read-heavy] workloads benefit from [caching] and [read replicas]. [Write-heavy] workloads benefit from [sharding], [LSM-tree stores], and [async replication].
