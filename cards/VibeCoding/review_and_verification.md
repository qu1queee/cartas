Q: What is the minimum you must always review in AI-generated code?
A: (1) Every file path modified — check no unintended files were changed. (2) All external calls — network, DB, file system. (3) Any security boundary: auth, input validation, secret handling. (4) All new dependencies introduced. Even a "small" AI change can touch these.

---

Q: When is it relatively safe to accept AI output without line-by-line review?
A: When the change is: small (< 50 lines), purely mechanical (rename, format, boilerplate), in a well-tested area where tests will catch regressions, and contains no IO, auth, or security logic. Never skip review when any of those conditions fail.

---

Q: What is the "diff-first" review approach?
A: Start your review from `git diff`, not from the files themselves. Diffs surface what changed; reading files surfaces what exists. Reviewing the delta is more efficient and focuses attention on what the AI actually did rather than what was already there.

---

Q: How do you spot a hallucinated API in generated code?
A: Check imports and function calls against the actual library documentation or source. Warning signs: a method that sounds plausible but doesn't autocomplete, an argument order that feels wrong, or a pattern inconsistent with the library's other APIs. Never assume a plausible-sounding method exists.

---

Q: What signals suggest an AI is guessing rather than reasoning confidently?
A: Hedging language in explanations ("this should work", "you may need to adjust"), multiple alternatives offered without a clear recommendation, or code that works around a problem in a roundabout way. These are signs to verify before shipping.

---

Q: What is the "compile and run" verification minimum?
A: For any AI change: compile (or pass type checking), run the relevant tests, and manually exercise the changed behavior at least once. These three steps catch the majority of AI errors. Skipping any one significantly raises the risk of shipping broken code.

---

Q: How do you verify correctness of AI-generated business logic?
A: State the expected behavior explicitly, then check whether the code expresses it. Trace through the logic manually for at least one happy-path case and one edge case. If you can't trace it, you can't verify it — ask the AI to simplify or explain.

---

Q: What is the danger of accepting a large AI diff all at once?
A: Cognitive overload makes it easy to miss subtle errors. Individual changes may each look correct while their interaction introduces a bug. Large diffs also make git history less useful for bisecting failures. Prefer multiple small, reviewed steps over one large acceptance.

---

Q: How do you review AI-generated SQL or database migrations?
A: Verify: (1) the migration is reversible (has a rollback), (2) no unintended table or column drops, (3) index additions won't lock a production table, (4) data type changes won't silently truncate data, (5) the query plan is acceptable for production data volumes.

---

Q: What is the role of static type checking in verifying AI output?
A: Types are the fastest, cheapest verification layer. A type-correct program has a much smaller space of possible behaviors. Always run the type checker before accepting AI output, and prefer languages/configs with strict type checking when vibe coding.

---

