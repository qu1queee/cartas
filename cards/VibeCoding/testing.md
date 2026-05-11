Q: Should you ask the AI to write tests before or after code?
A: Before, when possible. Prompt the AI to write tests that match your spec first, review them, then prompt it to implement the code that passes them. This prevents the common failure mode where AI writes tests that merely describe its own implementation rather than the intended behavior.

---

Q: What is AI-assisted TDD?
A: Write the test cases yourself (or review AI-generated ones carefully), run them against nothing to confirm they fail, then prompt the AI to write code that makes them pass. The red-green cycle keeps the AI's output anchored to specified behavior rather than plausible behavior.

---

Q: What is the "fox guarding the henhouse" risk in AI testing?
A: When the same AI session writes both the code and the tests, tests often verify what the code does rather than what it should do. The AI has no independent ground truth. Mitigate by writing tests first, or using a separate session with only the spec for test generation.

---

Q: What test types are most valuable for verifying AI-generated code?
A: Integration tests over unit tests, because unit tests can pass while the integration is wrong. Property-based tests are especially powerful — they test invariants rather than specific examples, catching edge cases the AI didn't anticipate.

---

Q: What is the golden path test and why is it essential for AI output?
A: A test that exercises the primary intended use case end-to-end. It's the minimum bar: if the golden path fails, nothing else matters. AI-generated code sometimes handles edge cases correctly while getting the main case subtly wrong.

---

Q: How do you prompt AI to generate meaningful edge case tests?
A: Ask explicitly: "What are the three most likely edge cases this function could fail on? Write a test for each." Then review whether those edge cases are the ones that matter for your domain — the AI's "likely" may not match production reality.

---

Q: What is snapshot testing and when is it useful for AI-generated UI code?
A: Snapshot tests capture rendered output and fail if it changes unexpectedly. Useful for AI-generated UI components as a regression guard — any unintended structural change is caught. But snapshots don't verify correctness, only stability; they must be reviewed when updated.

---

Q: How do you use the AI to find bugs in its own code?
A: After generation, prompt: "Review this code for bugs, edge cases, and incorrect assumptions" or "What inputs would cause this to produce wrong output?" This exploits the AI's ability to reason about code independently of having generated it.

---

Q: What is the risk of 100% test coverage on AI-generated code?
A: Coverage measures which lines execute, not whether behavior is correct. AI can generate tests that hit every line while testing trivial assertions. Coverage is necessary but not sufficient — focus on whether tests would catch a wrong implementation, not just whether they run.

---

Q: How do you validate that an AI-generated fix actually fixes the bug?
A: First, write a failing test that reproduces the bug before applying the fix. Then apply the fix and confirm the test passes. This proves the fix addresses the specific issue rather than just making the symptom go away.

---

