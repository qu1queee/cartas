Q: What is the plan-then-implement workflow in vibe coding?
A: Before touching code, produce a written spec or outline — via prompting or manually — that defines what will be built, what will not, and how success is measured. Only then prompt for implementation. This separates the thinking phase (cheap to revise) from the building phase (expensive to revise).

---

Q: What is spec-driven vibe coding?
A: Writing a formal specification — function signatures, data types, invariants, expected inputs/outputs — before prompting for implementation. The spec acts as both a prompt and a contract. The AI fills in the body; you verify it matches the spec.

---

Q: How often should you commit when vibe coding?
A: After each discrete, verifiable change — not at the end of a long session. "Works so far" is enough to commit. Small commits let you bisect failures, roll back a bad AI suggestion, and maintain a clear history of what each AI step produced.

---

Q: What is the "checkpoint commit" pattern?
A: Committing the current working state before handing a large task to the AI. If the AI's output is wrong or breaks things, you have a clean rollback point without losing prior work. Never let the AI make multiple large changes without a checkpoint between them.

---

Q: What is the ideal scope of a task given to the AI in one session?
A: One feature, one bug fix, or one refactor with a clear before/after. If the task requires changing more than 3–5 files in ways that are hard to verify independently, break it down further. The larger the AI's diff, the harder it is to catch mistakes.

---

Q: What is the "undo safety net" principle?
A: Always be one git reset or revert away from a known-good state. This means: commit before starting, commit after each validated step, and never accept AI output without running at least a compile/lint check. The safety net makes experimentation cheap.

---

Q: How do you maintain a human mental model of AI-generated code?
A: Read the diffs, not just the final file. For every non-trivial change, ask the AI to explain its reasoning. Write a brief commit message that captures the intent, not just what changed. This forces comprehension and creates a record you can return to.

---

Q: How do you handle task decomposition with an AI assistant?
A: Break the task into independently testable subtasks. Give the AI one subtask at a time, validate its output, then proceed. Avoid giving the AI a chain of dependent subtasks in one prompt — errors in step 1 propagate silently through steps 2 and 3.

---

Q: What is the difference between vibe coding greenfield vs. brownfield?
A: Greenfield (new project): AI has maximum freedom, fewer constraints to violate, mistakes are cheap to fix early. Brownfield (existing codebase): the AI must respect established patterns; wrong assumptions compound existing debt. Always give the AI more context in brownfield work.

---

Q: What is the role of code review when all code is AI-generated?
A: More important, not less. AI-generated code can be syntactically correct and functionally plausible while violating architectural constraints, security requirements, or performance budgets. Review should focus on intent, correctness of logic, and fit within the system — not style.

---

Q: How do you track what the AI changed across a session?
A: Use git diff liberally during the session, not just at PR time. Run `git diff HEAD` before accepting each new AI output. This keeps the reviewer (you) aware of the cumulative scope of changes and prevents "how did this get in here" surprises.

---

Q: What should a commit message contain for AI-generated code?
A: The intent (why this change), not just the what. "Add rate limiting middleware" is better than "Claude added rate limiting." The AI's role is implementation detail; the decision and its rationale belong to you and should be in the message.

---

