Q: What is the difference between RTO and RPO?
A: RTO (Recovery Time Objective): maximum acceptable downtime after a failure. RPO (Recovery Point Objective): maximum acceptable data loss measured in time. Lower RTO/RPO = higher cost. Design backup, replication, and failover strategies to meet these targets.

---

Q: What is chaos engineering?
A: Deliberately injecting failures (kill nodes, inject latency, drop network packets) in production or staging to discover weaknesses before they cause real incidents. Netflix's Chaos Monkey is the canonical example. Validate your system handles failures as designed.

---

Q: What is a post-mortem and what makes it blameless?
A: A structured review of an incident: timeline, root cause, contributing factors, impact, and action items. Blameless means focusing on system/process failures rather than individual mistakes — creates safety to report issues honestly and improves culture.

---

Q: What is the difference between MTBF and MTTR?
A: MTBF (Mean Time Between Failures): average uptime between outages — measure of reliability. MTTR (Mean Time To Recovery): average time to restore service — measure of resilience. High availability requires both high MTBF and low MTTR.

---

Q: What is a runbook?
A: Step-by-step documentation for responding to a specific operational scenario (e.g., database failover, disk full, traffic spike). Reduces cognitive load during incidents. Good runbooks include: symptoms, diagnosis steps, mitigation actions, escalation path.

---

Q: What is canary deployment and how does it reduce risk?
A: Routing a small percentage of traffic (e.g., 1-5%) to a new version before full rollout. Monitor error rates, latency, and business metrics. If metrics degrade, roll back; if stable, gradually increase traffic. Limits blast radius to a small fraction of users.

---

Q: What is blue-green deployment?
A: Running two identical production environments (blue = current, green = new). Route all traffic to green after validation; blue becomes the rollback target. Instant cutover and instant rollback, but doubles infrastructure cost. Use for high-stakes releases.

---

Q: What is a feature flag and what role does it play in deployments?
A: A configuration switch that enables/disables code paths at runtime without a deploy. Enables: dark launches (deploy code off, enable per user/segment), gradual rollouts, A/B testing, and instant kill switches. Store flag state in a fast config service (LaunchDarkly, Unleash).

---

Q: What is graceful degradation?
A: Designing the system so it continues providing reduced functionality when a component fails, rather than failing completely. Examples: serve cached content when the DB is down, show trending content instead of personalized feed if recommendation service is unavailable.

---

Q: What is a cascading failure and how do you prevent it?
A: One failing service overwhelms others through retries, connection queues, or resource contention — causing system-wide outage. Prevention: circuit breakers (stop sending to failing services), bulkheads (isolate resource pools), timeout budgets, load shedding, and backpressure.

---

Q: What is load shedding?
A: Deliberately dropping or rejecting requests when the system is overloaded, to protect its ability to serve high-priority traffic. Implemented via queue depth limits, request prioritization, or explicit HTTP 503 responses. Preferred over accepting all requests and serving all of them slowly.

---

C: [Observability] = [metrics] (what is happening) + [logs] (why it happened) + [traces] (where it happened across services). You cannot reliably operate a distributed system without all three.
