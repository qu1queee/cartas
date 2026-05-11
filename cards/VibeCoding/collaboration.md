Q: How do you establish code ownership for AI-generated code in a team?
A: The person who reviews and merges it owns it — not the AI, not the person who prompted it. Teams should make this explicit in their contributing guide. Ownership means accountability for bugs, on-call response, and future maintenance.

---

Q: What should a PR description include when the code is primarily AI-generated?
A: The same things as any PR: what changed, why, and how to test it. Additionally: any non-obvious choices the AI made that you reviewed and accepted. Do not note "AI wrote this" as a substitute for understanding — the reviewer needs to know you understood what you're merging.

---

Q: How does a code reviewer approach AI-generated code differently?
A: Assume no defensive bias — the author may not have written or fully understood every line. Review with more skepticism than usual for: logic correctness, missing edge cases, security boundaries, and out-of-scope changes. Treat the review as the primary quality gate, not a secondary check.

---

Q: What is the "AI attribution" question and how do most teams handle it?
A: Whether to note in code or PRs that AI generated something. Most teams that have addressed this conclude: attribution is not useful in code comments (it rots), but may appear in commit messages or PR descriptions for transparency. The code must stand on its own regardless of origin.

---

Q: How do you prevent AI assistants from diverging on team conventions?
A: Put team conventions in CLAUDE.md (or equivalent) at the repo root, committed and reviewed. This gives every developer's AI session the same constraints. Without it, each developer's AI will infer conventions differently from the surrounding code.

---

Q: What is the risk of siloed vibe coding in a team setting?
A: Each developer's AI session lacks awareness of what others are building simultaneously. This produces duplicate implementations, inconsistent patterns, and merge conflicts at the logic level (not just the line level). Counter with: short-lived branches, frequent integration, and shared ADRs.

---

Q: How do you onboard a new teammate to a vibe-coded codebase?
A: The same as any codebase — readable code, clear module boundaries, and good commit history — but more attention to the "why." AI-generated code often lacks the author's intent in comments or naming. Invest in CLAUDE.md and ADRs to capture decisions that aren't visible in the code.

---

Q: What is a vibe coding style guide and what should it cover?
A: A team document defining: which AI tools are approved, what context must be in CLAUDE.md, the review bar for AI-generated PRs, how to handle hallucinated dependencies, what's off-limits for AI generation (e.g., migrations, auth), and commit message conventions for AI-assisted work.

---

