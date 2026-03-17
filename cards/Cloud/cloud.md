Q: What are the three components of an object in object storage?
A: Data (bytes), Metadata (descriptive info), and Key (unique identifier to retrieve it).

---

Q: How does object storage differ from file or block storage?
A: It is flat, massively scalable, and accessed exclusively via HTTP REST APIs.

---

Q: What are the five NIST essential cloud characteristics?
A: On-demand self-service, broad network access, resource pooling, rapid elasticity, measured service.

---

C: [On-demand self-service] means provisioning resources with no human interaction from the provider.

---

C: [Measured service] means every byte stored and every API call is tracked, enabling usage-based billing.

---

Q: What are the three cloud service models?
A: IaaS (infrastructure), PaaS (platform), SaaS (software).

---

Q: What are the three cloud deployment models?
A: Public cloud, private cloud, hybrid cloud.

---

Q: What is the difference between a cloud provider and a cloud consumer?
A: Provider manages the infrastructure; consumer provisions and uses the resources.

---

Q: What is IAM in cloud computing?
A: Identity and Access Management — controls who can authenticate and what resources they can access.

---

Q: What is the shared responsibility model?
A: Provider secures the infrastructure; consumer secures their data and access configuration.

---

Q: What is the leading cause of cloud data breaches?
A: Misconfiguration — e.g., a storage bucket that should be private left publicly accessible.

---

Q: Why is REST the dominant API style for cloud storage?
A: It is stateless, uses standard HTTP, and works across languages, CLIs, and browsers.

---

Q: What is a cloud SLA?
A: A contract specifying availability, response time, reliability, and penalties between provider and consumer.

---

Q: What is the difference between single-tenant and multi-tenant cloud?
A: Single-tenant is dedicated to one organisation; multi-tenant shares infrastructure among many consumers.

---

C: [IaaS] — provider manages hardware; consumer manages OS, runtime, and applications upward.

---

Q: What is a bucket in cloud object storage?
A: A provisioned IT resource — a logical container for objects, accessible via REST API.

---

Q: What is broad network access?
A: Cloud resources are accessible over the network via standard mechanisms (browsers, CLIs, APIs).

---

Q: What is resource pooling in cloud?
A: Provider serves multiple consumers from shared physical resources using multi-tenancy.

---

Q: What is rapid elasticity?
A: Resources can be scaled up or down quickly, appearing unlimited to the consumer.

---

Q: What HTTP methods map to object storage operations?
A: GET (download), PUT (upload), DELETE (remove), HEAD (metadata only).

---

Q: What is S3 API compatibility and why does it matter?
A: Any tool built for AWS S3 works with any S3-compatible service — vendor-agnostic portability.

---

Q: What is a cloud management console?
A: A web UI to visually provision and manage cloud resources — e.g., IBM Cloud dashboard or MinIO Console.

---

Q: What is a cloud CLI and what does authentication look like?
A: A command-line tool (e.g., ibmcloud, aws, mc) that requires login before issuing resource commands.

---

Q: What four threats apply to cloud security?
A: Eavesdropping, DoS (denial of service), privilege escalation, misconfiguration.

---

Q: In the shared responsibility model, who is responsible for bucket access policy?
A: The cloud consumer — the provider enforces the policy but the consumer must set it correctly.

---

Q: What is IBM Cloud Object Storage pricing model an example of?
A: Measured service — charged per GB stored, per 1,000 API calls, and per GB of bandwidth transferred.

---

Q: What does 99.9% availability SLA mean in minutes of downtime per year?
A: Approximately 525 minutes (~8.7 hours) of permitted downtime per year.

---

Q: What is the difference between PaaS and IaaS responsibility?
A: PaaS — provider also manages OS and runtime; consumer only manages the application and data.

---

Q: What is a hybrid cloud?
A: A combination of private and public cloud environments, typically connected and sharing workloads.

---

Q: What enabling technologies underpin cloud computing?
A: Virtualisation, containerisation, REST APIs, and broadband networking.
