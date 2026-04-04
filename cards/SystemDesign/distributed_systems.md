Q: What is the Raft consensus algorithm?
A: A leader-based consensus algorithm for distributed logs. One leader accepts writes, replicates to a quorum of followers, commits when a majority acknowledges. Leaders are elected via randomized timeouts. Used in etcd, CockroachDB, TiKV.

---

Q: What is a quorum and why does it matter?
A: A majority of nodes that must agree for an operation to succeed. For N nodes, quorum = floor(N/2) + 1. Ensures that any two quorums overlap by at least one node, guaranteeing consistent reads after quorum writes.

---

Q: What is the difference between a leader-follower and a leaderless replication model?
A: Leader-follower: one node coordinates all writes, simpler consistency. Leaderless (Dynamo-style): any node accepts writes, uses quorum reads/writes (W+R > N), conflict resolution via last-write-wins or vector clocks. Leaderless is more available under partitions.

---

Q: What are vector clocks used for?
A: Tracking causality in distributed systems — each node maintains a counter per process. Enables detecting concurrent writes and determining "happened-before" relationships. Used for conflict detection in Dynamo-style systems (DynamoDB, Riak).

---

Q: What is a distributed transaction and why is it hard?
A: A transaction spanning multiple nodes/services. Hard because any node can fail independently — you need atomicity across unreliable components. 2PC (Two-Phase Commit) is the classic protocol but blocks if the coordinator fails. Saga and outbox patterns are modern alternatives.

---

Q: What is Two-Phase Commit (2PC) and what is its failure mode?
A: Phase 1: coordinator asks all participants to "prepare" (vote yes/no). Phase 2: if all vote yes, coordinator sends "commit". Failure mode: if the coordinator crashes after prepare but before commit, participants are left in a blocking uncertain state.

---

Q: What is the difference between a saga and 2PC for distributed transactions?
A: 2PC: strong atomicity, synchronous, coordinator is a bottleneck. Saga: sequence of local transactions with compensating rollback transactions — eventually consistent, no global lock, more complex business logic. Use saga for long-lived transactions across microservices.

---

Q: What is a distributed lock and when should you avoid it?
A: A lock held across multiple service instances (Redis SETNX, ZooKeeper). Avoid when: high throughput (contention kills performance), lock holder can die mid-operation (use expiry + fencing tokens), or strong consistency isn't needed (optimistic concurrency or idempotency suffice).

---

Q: What is a fencing token?
A: A monotonically increasing number issued with each lock grant. Storage systems check that write requests carry the latest token — preventing a lock holder that resumed after expiry from overwriting a newer holder's writes.

---

Q: What is a circuit breaker pattern?
A: Tracks failure rate of calls to a downstream service. When failures exceed a threshold, the circuit "opens" — subsequent calls fail fast without hitting the downstream, giving it time to recover. After a timeout, the circuit goes "half-open" to test recovery.

---

Q: What is the difference between a circuit breaker and a retry with backoff?
A: Retry: immediately reattempts a failed call — good for transient errors, harmful during outages (amplifies load). Circuit breaker: stops sending requests entirely when a service is down. Use both: retry for transient errors, circuit breaker to detect and handle prolonged failures.

---

Q: What is service mesh and what does it provide?
A: Infrastructure layer managing service-to-service communication (Istio, Linkerd). Provides: mutual TLS, load balancing, circuit breaking, retries, distributed tracing, and traffic management — without changing application code. Adds operational complexity.

---

C: [Eventual consistency] is acceptable when users tolerate brief staleness (social feeds, analytics). [Strong consistency] is required when correctness is critical (bank balance, inventory count, distributed locks).
