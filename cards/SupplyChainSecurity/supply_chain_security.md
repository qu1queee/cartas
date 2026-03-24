Q: What is software supply chain security?
A: The practice of securing every component, tool, and process that contributes to building and delivering software — from source code and dependencies to CI/CD pipelines and package registries.

---

Q: What is a Software Bill of Materials (SBOM)?
A: A machine-readable inventory of all components, libraries, and dependencies in a piece of software, enabling visibility into what is actually shipped.

---

Q: What is a dependency confusion attack?
A: An attack where a malicious package with the same name as a private internal package is published to a public registry (e.g., npm, PyPI), causing build tools to fetch the attacker's version instead.

---

Q: What does SLSA stand for and what is its purpose?
A: Supply-chain Levels for Software Artifacts — a security framework that defines levels of build integrity, from basic source versioning (L1) to fully hermetic, reproducible builds (L4).

---

Q: What is a reproducible build?
A: A build process that always produces byte-for-byte identical output given the same source inputs, allowing independent parties to verify that the artifact was not tampered with.

---

Q: What is the purpose of signing software artifacts?
A: To cryptographically bind an artifact to its producer so consumers can verify authenticity and integrity — ensuring the package was not altered after it was built.

---

Q: What is Sigstore and what problem does it solve?
A: An open-source project providing keyless signing of software artifacts using short-lived certificates tied to identity (e.g., OIDC), removing the need to manage long-lived private signing keys.

---

Q: What is a typosquatting attack in the context of package managers?
A: Publishing a malicious package with a name very similar to a popular legitimate package (e.g., `lodash` vs `1odash`), tricking developers into installing it by accident.

---

Q: What is the principle of least privilege as applied to CI/CD pipelines?
A: Each pipeline job should have only the permissions it strictly needs — e.g., a build job should not have write access to production registries or secrets it does not use.

---

Q: What is a build provenance attestation?
A: A signed, verifiable statement describing how, when, and where a software artifact was built — including the source commit, build system, and environment — used to establish trust in the artifact's origin.
