Q: What are the key constraints of REST?
A: Stateless (server holds no client context), Client-server separation, Cacheable responses, Uniform interface (resource-based URLs, standard HTTP verbs), Layered system, Code on demand (optional).

---

Q: When would you choose gRPC over REST?
A: gRPC is preferred for: internal service-to-service communication, bidirectional streaming, polyglot environments (protobuf codegen), when strict contracts and performance matter. REST is better for public APIs, browser clients, and simpler ecosystems.

---

Q: What is GraphQL and what problem does it solve?
A: A query language where clients specify exactly what data they need. Solves over-fetching (REST returns too much) and under-fetching (multiple round trips to assemble data). Good for complex frontends with varying data needs; adds complexity on the backend (N+1 queries, authorization).

---

Q: What is the N+1 query problem in GraphQL and how do you solve it?
A: For each parent object, a resolver fires a separate DB query for each child — N parents = N+1 queries total. Solution: DataLoader pattern — batches and deduplicates child queries within a single request tick.

---

Q: What is API versioning and what strategies exist?
A: Managing breaking changes in an API. Strategies: URL path versioning (/v1/users), query parameter (?version=1), Accept header versioning (preferred for REST purists), consumer-driven contracts (Pact). URL versioning is most common and operationally simple.

---

Q: What is rate limiting and what algorithms implement it?
A: Restricting request frequency per client to prevent abuse and protect capacity. Algorithms: Token bucket (smooth bursts), Leaky bucket (enforces constant rate), Fixed window counter (simple, burst at window boundary), Sliding window log (precise, high memory), Sliding window counter (balance of both).

---

Q: What is an API gateway and what does it handle?
A: A single entry point for all client requests. Handles: authentication/authorization, rate limiting, SSL termination, request routing, load balancing, request/response transformation, logging, and circuit breaking — offloading cross-cutting concerns from services.

---

Q: What is the difference between authentication and authorization?
A: Authentication: verifying identity (who are you? — JWT, OAuth). Authorization: verifying permissions (what can you do? — RBAC, ABAC, scopes). Authentication must happen before authorization.

---

Q: What is JWT and what are its security considerations?
A: JSON Web Token — a signed (or encrypted) token containing claims. Considerations: tokens are stateless and can't be revoked before expiry (use short TTLs + refresh tokens or a blocklist), use HS256/RS256 correctly, validate all claims including expiry and audience.

---

Q: What is the difference between OAuth 2.0 and OpenID Connect?
A: OAuth 2.0 is an authorization framework (grants access to resources via tokens). OpenID Connect (OIDC) is an identity layer on top of OAuth 2.0 that adds authentication and provides an ID token with user info.

---

Q: What is an idempotency key in API design?
A: A client-provided unique key per request. Server stores the key and result — if the same key arrives again (retry), returns the stored result instead of re-executing. Critical for payment, order, and mutation APIs to make retries safe.

---

C: [Pagination] strategies: offset/limit (simple, inconsistent under writes), cursor-based (stable, performant for deep pages), keyset (efficient DB index scan). Prefer [cursor-based] for feeds and real-time data.
