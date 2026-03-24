Q: What is Infrastructure as Code (IaC)?
A: The practice of defining and provisioning infrastructure (servers, networks, databases) through machine-readable configuration files rather than manual processes or interactive UIs.

---

Q: What is Terraform?
A: An open-source IaC tool by HashiCorp that lets you define cloud and on-premise infrastructure in declarative HCL configuration files and manages the full lifecycle of those resources.

---

Q: What is HCL?
A: HashiCorp Configuration Language — the declarative, human-readable language used to write Terraform configurations. It describes the desired end state, not the steps to reach it.

---

Q: What are the four main Terraform workflow commands?
A: `terraform init` (initialise providers/modules), `terraform plan` (preview changes), `terraform apply` (apply changes), `terraform destroy` (tear down resources).

---

Q: What is a Terraform provider?
A: A plugin that translates Terraform resource definitions into API calls for a specific platform (e.g., AWS, Azure, GCP, Kubernetes). Each provider is downloaded during `terraform init`.

---

Q: What is Terraform state?
A: A JSON file (`terraform.tfstate`) that records the current known state of managed resources. Terraform uses it to compute diffs between desired configuration and real infrastructure.

---

Q: Why should Terraform state be stored remotely?
A: Remote backends (e.g., S3, Terraform Cloud) enable team collaboration, state locking to prevent concurrent modifications, and keep sensitive data off local developer machines.

---

Q: What is a Terraform module?
A: A reusable, self-contained package of Terraform configuration that groups related resources — called with input variables and returning output values, similar to a function.

---

Q: What is the difference between `terraform plan` and `terraform apply`?
A: `plan` generates an execution plan showing what would change without making any changes. `apply` executes that plan and makes the actual infrastructure changes.

---

Q: What is a data source in Terraform?
A: A read-only configuration block that fetches information from an existing resource or external system (e.g., the latest AMI ID) without creating or managing it.

---

Q: What is `terraform import` used for?
A: Bringing an existing, manually created resource under Terraform management by mapping it to a resource block in configuration and writing it into state.

---

Q: What is the purpose of `terraform.tfvars`?
A: A file that supplies values for declared input variables, keeping variable values separate from the configuration logic and allowing environment-specific overrides.

---

Q: What is state locking in Terraform?
A: A mechanism where the remote backend acquires an exclusive lock on the state file during operations, preventing two `apply` runs from corrupting state simultaneously.

---

Q: What is the `depends_on` meta-argument?
A: An explicit dependency directive that forces Terraform to create or destroy one resource before another when the dependency cannot be inferred automatically from resource references.

---

Q: What is a Terraform workspace?
A: A named, isolated instance of state within the same configuration, allowing the same code to manage multiple environments (e.g., dev, staging, prod) with separate state files.
