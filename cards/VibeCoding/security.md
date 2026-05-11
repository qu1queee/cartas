Q: What is prompt injection and why does it matter in vibe coding?
A: An attack where malicious content in data the AI reads (files, web pages, database rows) causes it to execute unintended instructions. In agentic vibe coding where the AI has tool access, a crafted input could trick it into deleting files or exfiltrating data. Never give an AI agent access to external data and sensitive tool access simultaneously without sandboxing.

---

Q: What are hallucinated packages and why are they a security risk?
A: AI may reference package names that don't exist (e.g., `npm install some-plausible-util`). Attackers pre-register these names with malicious code, knowing AI will recommend them. Always verify every dependency against the official registry before installing.

---

Q: What OWASP vulnerabilities are most commonly introduced by AI-generated code?
A: SQL injection (string-concatenated queries), XSS (unescaped output), insecure deserialization, hardcoded secrets, and missing input validation. AI models learned from code that includes vulnerable patterns — it reproduces them confidently.

---

Q: How do you prevent SQL injection in AI-generated database code?
A: Require parameterized queries or prepared statements as an explicit constraint in the prompt: "use parameterized queries, never string concatenation for SQL." Review every query in the diff. AI frequently defaults to string interpolation in examples.

---

Q: What is the "over-permissioned agent" risk?
A: Giving an AI agent broad tool access (file system, network, shell) for a task that only needs narrow access. If the AI is compromised via prompt injection or makes an error, broad permissions amplify the blast radius. Grant only the tools needed for the specific task.

---

Q: How should you handle secrets and credentials in AI-assisted development?
A: Never paste secrets into prompts. Never let the AI write code that hardcodes credentials. Instruct it to use environment variables or a secrets manager, and review generated code for any literal strings that look like tokens, keys, or passwords.

---

Q: What is the risk of AI using deprecated or insecure APIs?
A: Training data includes old code. AI may recommend APIs that were deprecated or patched for security vulnerabilities years ago — and do so confidently. Always check the library version and changelog when the AI references a specific API.

---

Q: What is the "blast radius" principle when granting AI tool access?
A: Scope tool access to the minimum surface needed. Read-only file access before write. Write access scoped to the project directory, not the home directory. No network access unless the task requires it. Narrower access means a mistake or attack affects less.

---

Q: How do you audit AI-generated code for security vulnerabilities?
A: Run a static analysis tool (e.g., Semgrep, Bandit, gosec) on the diff before merging. Focus manual review on: all input handling, all queries, all auth checks, and any use of eval/exec/subprocess. Automated scanning catches the common patterns AI reproduces.

---

Q: What is the "trust boundary" principle for AI agents?
A: Define where trusted input ends and untrusted input begins. Instructions from the developer are trusted; content from the internet, users, or external files is untrusted. The AI should never treat untrusted content as instructions — structure your agent to enforce this separation architecturally.

---

