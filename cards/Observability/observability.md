Q: What are the three pillars of observability?
A: Metrics (aggregated numerical measurements over time), Logs (discrete timestamped event records), and Traces (end-to-end records of a request across services).

---

Q: What is the difference between monitoring and observability?
A: Monitoring tells you when something is wrong (known unknowns). Observability lets you understand why — you can ask arbitrary questions about system state from external outputs alone.

---

Q: What is a metric in the context of observability?
A: A numeric value measured at a point in time, associated with a name and labels (dimensions). Metrics are cheap to store and query but lose the detail of individual events.

---

Q: What are the four Prometheus metric types?
A: Counter (monotonically increasing), Gauge (can go up or down), Histogram (samples in configurable buckets), Summary (pre-calculated quantiles on the client).

---

Q: What is the difference between a Prometheus Counter and a Gauge?
A: A Counter only ever increases (or resets to zero on restart) — used for totals like requests served. A Gauge represents a current value that can rise or fall — used for things like memory usage.

---

Q: What is a Prometheus Histogram used for?
A: Measuring the distribution of values (e.g., request latency) by counting observations in configurable buckets — enabling server-side calculation of quantiles via `histogram_quantile`.

---

Q: What is scraping in Prometheus?
A: Prometheus pulls (scrapes) metrics from instrumented targets at a configured interval by making HTTP GET requests to the `/metrics` endpoint of each target.

---

Q: What is OpenTelemetry?
A: A vendor-neutral CNCF project providing a unified set of APIs, SDKs, and a collector for generating, collecting, and exporting metrics, logs, and traces to any backend.

---

Q: What is a trace in distributed tracing?
A: A record of a single request's journey through a distributed system, composed of spans. It shows the full call path, timing, and relationships between services.

---

Q: What is a span in distributed tracing?
A: The unit of work within a trace — represents a single operation (e.g., an HTTP call, a DB query) with a start time, duration, name, and attributes. Spans are nested to form a tree.

---

Q: What is context propagation in tracing?
A: Passing trace identifiers (trace ID, span ID) across process boundaries (e.g., via HTTP headers like `traceparent`) so all spans from a single request are linked into one trace.

---

Q: What are the four Golden Signals?
A: Latency (how long requests take), Traffic (how much demand), Errors (rate of failed requests), Saturation (how full the system is — the most constrained resource).

---

Q: What is a Service Level Indicator (SLI)?
A: A quantitative measure of a service's behaviour — e.g., the proportion of requests served in under 200 ms. It is the metric you choose to represent user experience.

---

Q: What is the difference between an SLI, SLO, and SLA?
A: SLI is the measured metric. SLO (Service Level Objective) is the target value for that metric (e.g., 99.9% of requests < 200 ms). SLA is the contractual agreement with consequences for missing the SLO.

---

Q: What is an error budget?
A: The amount of unreliability permitted within an SLO window (e.g., if SLO is 99.9%, the error budget is 0.1% of requests or ~43 min/month). It balances reliability work against feature velocity.
