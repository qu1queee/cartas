Q: What is CLAUDE.md and what should it contain?
A: A project-level instruction file read by Claude Code at session start. It should contain: tech stack and versions, project conventions (naming, error handling style), commands to run tests and lint, known gotchas, and what's off-limits. It is the AI's onboarding document.

---

Q: What should NOT go in CLAUDE.md?
A: Architecture diagrams or high-level overviews (too stale), exhaustive file listings (derivable from code), task-specific state or in-progress work (belongs in the conversation), and secrets or credentials (never in a committed file).

---

Q: What is context drift and how does it cause bugs?
A: Over a long session, the AI loses track of earlier constraints as the conversation fills the context window. It may contradict earlier decisions or forget established conventions. Result: subtly inconsistent code that passes local review but diverges from project norms.

---

Q: How do you prevent context drift in a long vibe coding session?
A: Use short, focused sessions scoped to one task. At key decision points, write the decision into CLAUDE.md or a session note so it persists across sessions. When a session gets long, start fresh and re-inject the relevant context explicitly.

---

Q: What is the "fresh session" pattern?
A: Starting a new AI conversation for each discrete task rather than continuing one long thread. Benefits: no context drift, clean audit trail per task, and AI starts without stale assumptions from earlier in the session.

---

Q: What is a project brief and how does it help AI sessions?
A: A short (1–2 page) document describing: what the project does, the core data model, the key invariants, and what "correct" looks like. Pasting or referencing it at session start gives the AI a shared mental model, reducing specification gaps and hallucinated architecture.

---

Q: How do you give an AI useful context about an existing codebase?
A: Show the entry points and the key abstractions, not the full code. Provide the schema or type definitions, the main interfaces, and a working example of the pattern in use. Let the AI ask for more rather than dumping everything upfront.

---

Q: How do you maintain consistency across multiple AI sessions?
A: Keep a lightweight decision log (ADR-style) of non-obvious choices: why a library was picked, what was ruled out, what conventions were adopted. Inject relevant entries at session start. Without this, each session reinvents decisions already made.

---

Q: What is the risk of ignoring context window pressure mid-task?
A: As the window fills, the AI's attention on early instructions degrades. It may forget a constraint you stated at the start ("don't use global state") and violate it late in the conversation. Watch for this in long implementation tasks.

---

Q: How should you structure a repo to be maximally AI-friendly?
A: Clear module boundaries, consistent naming, types co-located with logic, and short files. AI tools perform better when each file has one clear concern and the codebase avoids implicit conventions that require reading many files to understand.

---

