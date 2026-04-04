Q: What is the difference between L4 and L7 load balancing?
A: L4 (transport layer): routes based on IP/TCP — fast, low overhead, can't inspect content. L7 (application layer): routes based on HTTP headers, URL, cookies — enables content-based routing, A/B testing, SSL termination, but adds latency.

---

Q: What are common load balancing algorithms?
A: Round robin (simple rotation), Weighted round robin (proportional to capacity), Least connections (send to least loaded), IP hash (sticky routing by client IP), Random with two choices (pick best of two random — low overhead, near-optimal).

---

Q: What is a health check and why is it critical for load balancers?
A: Periodic probes (HTTP/TCP) to verify a backend is alive. Load balancer removes unhealthy nodes from the pool. Without health checks, traffic is sent to dead instances causing cascading errors. Use both liveness (alive?) and readiness (ready for traffic?) probes.

---

Q: What is the difference between active-active and active-passive high availability?
A: Active-active: all nodes serve traffic simultaneously — better throughput and utilization. Active-passive: standby takes over only on primary failure — simpler consistency, wasted capacity. Active-active requires conflict resolution or stateless services.

---

Q: What is DNS-based load balancing and what are its limitations?
A: Returning multiple IPs for a hostname. Simple and global, but: clients cache DNS (ignoring TTL), no health awareness, uneven distribution. Suitable for geographic routing; not for fine-grained load control.

---

Q: What is Anycast routing?
A: Multiple servers share the same IP address; BGP routing sends each client to the topologically nearest one. Used by CDNs and DNS providers (Cloudflare, Google 8.8.8.8) to serve from the closest data center with no client configuration.

---

Q: What is the difference between a forward proxy and a reverse proxy?
A: Forward proxy: sits in front of clients, hides client identity from servers (e.g., corporate egress, VPN). Reverse proxy: sits in front of servers, hides server topology from clients (e.g., Nginx, load balancer, API gateway).

---

Q: What is connection draining / graceful shutdown?
A: Before removing a backend from the pool (deploy/scale-down), the load balancer stops sending new requests but lets in-flight requests complete. Prevents dropped requests during rolling deploys.

---

Q: What is a weighted traffic split used for?
A: Gradually shifting traffic between versions (canary deployments, blue-green). E.g., send 5% of traffic to v2 while monitoring error rates, then increase to 100% or rollback. Reduces blast radius of bad deployments.

---

C: [Sticky sessions] route a user to the same backend instance — needed for stateful servers but prevents true horizontal scaling. Prefer [externalizing state] to Redis so any instance can serve any user.
