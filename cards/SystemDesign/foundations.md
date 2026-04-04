Q: What are the four axes of scalability?
A: Vertical scaling (bigger machine), horizontal scaling (more machines), functional decomposition (split by service), and data partitioning (sharding).

---

Q: What is the difference between latency and throughput?
A: Latency is the time to complete a single operation (ms). Throughput is the number of operations completed per unit time (req/s). Optimizing one can hurt the other.

---

Q: What does "p99 latency" mean and why does it matter more than average?
A: The 99th percentile — 99% of requests complete faster than this value. It captures the worst-case experience for real users; averages hide outliers.

---

Q: What is the CAP theorem?
A: A distributed system can guarantee at most two of: Consistency (every read returns the latest write), Availability (every request gets a response), Partition Tolerance (system continues under network splits). Since partitions are unavoidable, the real choice is CP vs AP.

---

Q: What does PACELC extend CAP with?
A: Even without partitions (P), there is a tradeoff: choose lower latency (L) or stronger consistency (C). PACELC = Partition → {Availability or Consistency}; Else → {Latency or Consistency}.

---

Q: What is the difference between strong, eventual, and causal consistency?
A: Strong: all nodes see the same data immediately. Eventual: replicas converge given no new writes. Causal: operations that have a causal relationship are seen in order everywhere.

---

Q: What is the difference between availability and reliability?
A: Availability is the fraction of time a system is operational (uptime). Reliability is whether it produces correct results consistently. A system can be up but return wrong data.

---

Q: What does "five nines" availability mean in concrete terms?
A: 99.999% uptime → ~5.26 minutes of downtime per year.

---

Q: What is a Service Level Objective (SLO) vs a Service Level Agreement (SLA)?
A: SLO is an internal target (e.g., p99 < 200ms). SLA is a contractual commitment with customers; breach triggers penalties. SLOs should be tighter than SLAs to leave a buffer.

---

Q: What is an error budget?
A: The allowed downtime/error rate within an SLO window. E.g., 99.9% SLO = 43.8 min/month budget. Teams spend the budget on risky deploys and freeze changes when it runs out.

---

Q: What is back-pressure in a distributed system?
A: A mechanism where downstream services signal overload upstream so producers slow down rather than overwhelming the system. Essential for preventing cascade failures.

---

Q: What is a thundering herd problem?
A: When many clients simultaneously request the same resource (e.g., cache expiry or server restart), causing a burst of load on the backend. Mitigations: jitter, cache warming, request coalescing.

---

Q: What is the difference between stateless and stateful services?
A: Stateless services hold no session data — any instance can handle any request, enabling easy horizontal scaling. Stateful services store session/context and require sticky routing or external state stores.

---

Q: Why is idempotency critical in distributed systems?
A: Networks can duplicate or retry requests. An idempotent operation produces the same result regardless of how many times it is applied, making retries safe. Use idempotency keys for payment/mutation APIs.

---

Q: What is the fallacies of distributed computing?
A: Eight false assumptions: network is reliable, latency is zero, bandwidth is infinite, network is secure, topology doesn't change, one administrator, transport cost is zero, network is homogeneous.

---

C: [Horizontal scaling] adds more machines; [vertical scaling] upgrades a single machine. Horizontal is preferred for web tiers; vertical hits hardware limits.

---

C: The [two generals problem] proves that perfect consensus over an unreliable channel is impossible — foundational reason why distributed systems must tolerate uncertainty.
