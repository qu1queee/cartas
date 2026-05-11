Q: What is intent erosion in a long vibe coding session?
A: Gradual drift from the original goal as the AI introduces small, locally reasonable changes that collectively move the code away from the intended design. Common when sessions run long without re-anchoring to the spec. Fix by reviewing the original goal at each major step.

---

Q: What is the "yes-man" failure mode in AI coding assistants?
A: The AI agrees with your framing even when it's wrong. If you say "the bug is in the cache layer, fix it," it fixes the cache layer — even if the real bug is elsewhere. Avoid leading prompts; ask the AI to diagnose before you assume the location of a problem.

---

Q: What is the "infinite loop of fixes" anti-pattern?
A: Repeatedly prompting the AI to fix the same bug, each attempt producing a new variant that still fails. Cause: the AI doesn't know the constraint space, so it keeps trying surface changes. Break out by providing a minimal reproduction, running it yourself, and verifying the root cause before re-prompting.

---

Q: What is premature abstraction from AI?
A: AI tends to over-engineer when given latitude — introducing interfaces, factories, and extension points for problems that don't need them. This is the AI optimizing for "good-looking code" rather than "right-sized code." Counter with explicit negative prompts: "do not add abstractions beyond what this task requires."

---

Q: What is "scope creep by AI"?
A: The AI adds features, refactors adjacent code, or improves things you didn't ask for in a single response. This makes diffs harder to review, complicates rollback, and may introduce unintended changes. Use the "one concern per prompt" rule and reject out-of-scope additions.

---

Q: What is the risk of accepting code you don't understand?
A: You cannot debug what you cannot reason about. When AI-generated code reaches production and fails, the on-call engineer (often you) must diagnose it under pressure. If you accepted it opaquely, you have no mental model to start from. Always understand the high-level logic of what you ship.

---

Q: What is context poisoning?
A: When incorrect or misleading information early in a session causes all subsequent AI output to be built on a false premise. Example: you describe the data model incorrectly, and the AI generates correct-looking code for the wrong model. Validate the AI's understanding of your domain before building on it.

---

Q: What is the "plausibility trap"?
A: AI-generated code often looks correct — it follows conventions, uses the right idioms, and compiles cleanly. This visual plausibility suppresses the reviewer's instinct to verify. The most dangerous AI errors are the ones that look like the right answer.

---

Q: What is over-reliance on AI explanations?
A: Trusting the AI's explanation of its own code rather than tracing the logic yourself. AI explanations can be fluent and wrong simultaneously — the model describes what it intended, not necessarily what the code does. Read the code; use the explanation as a starting hypothesis, not a conclusion.

---

Q: What is the "ghost dependency" pitfall?
A: AI generates code that imports a module or calls a function that doesn't exist yet, assuming you'll create it. Without careful review of the full diff, you may ship code with unresolved references. Always run a full build after AI changes, not just the changed file.

---

