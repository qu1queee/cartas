Q: What is the strangler fig pattern?
A: Incrementally migrating a monolith by routing specific paths to new microservices while the monolith still handles the rest. New functionality is added to services, not the monolith. Over time the monolith "withers" as services absorb all its responsibilities.

---

Q: What is the CQRS pattern?
A: Command Query Responsibility Segregation — separates write operations (commands) from read operations (queries) into different models/services. Enables independent scaling, optimized read models (materialized views), and event sourcing. Adds synchronization complexity.

---

Q: What is event sourcing?
A: Storing the full history of state-changing events rather than current state. Current state is derived by replaying events. Provides a complete audit log, enables time-travel, and decouples services via events. Trade-off: query complexity, eventual consistency.

---

Q: What is the sidecar pattern?
A: Deploying a helper container alongside the main application container in the same pod/host. Sidecars handle cross-cutting concerns: logging, metrics, proxying (service mesh), configuration — without coupling them into the application code.

---

Q: What is the bulkhead pattern?
A: Isolating components into separate resource pools (thread pools, connection pools, processes) so a failure in one doesn't exhaust resources for others. Like bulkheads in a ship — a compartment flooding doesn't sink the whole vessel.

---

Q: What is service discovery and what are the two models?
A: The mechanism by which services find each other's network addresses dynamically. Client-side discovery: client queries a registry (Consul, Eureka) and picks an instance itself. Server-side discovery: client calls a load balancer/router which handles lookup internally (Kubernetes Service).

---

Q: What is the difference between orchestration and choreography in microservices?
A: Orchestration: a central coordinator (workflow engine) tells services what to do — easy to reason about, single point of failure. Choreography: services react to events independently — more resilient, harder to trace end-to-end flow.

---

Q: What is a bounded context in Domain-Driven Design?
A: A logical boundary around a domain model within which terms and concepts have a consistent meaning. Each microservice should ideally align with one bounded context — prevents models leaking across services and causing coupling.

---

Q: What is the anti-corruption layer (ACL) pattern?
A: A translation layer between two systems with different domain models. Prevents the concepts and data structures of a legacy system from "corrupting" the clean model of a new service. Common in migration and integration scenarios.

---

Q: What is an API composition pattern vs a materialized view in microservices queries?
A: API composition: API gateway queries multiple services and joins data in memory — simple, but slow for complex queries. Materialized view: a dedicated read service subscribes to events and pre-joins data into a queryable store — fast reads, eventual consistency, more infrastructure.

---

Q: What is a saga's compensating transaction?
A: A business operation that undoes the effect of a previous step when a later step fails. E.g., if payment succeeds but inventory reservation fails, a compensating transaction refunds the payment. Compensating transactions must themselves be idempotent.

---

C: [Conway's Law]: organizations design systems that mirror their communication structure. Microservice boundaries that align with [team boundaries] reduce coordination overhead and are more likely to succeed.
