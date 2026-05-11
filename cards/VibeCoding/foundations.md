Q: What is vibe coding?
A: A development style where the programmer drives primarily through natural language prompts to an AI, accepting or lightly editing generated code rather than writing every line manually. The developer's role shifts from typing code to directing, reviewing, and integrating AI output.

---

Q: Who coined the term "vibe coding" and when?
A: Andrej Karpathy in February 2025, in a post describing the experience of "fully giving in to the vibes" — prompting an AI, accepting its output, and iterating until the program works, without reading every line.

---

Q: What is the core philosophical shift vibe coding introduces?
A: From authorship to curation. The developer is no longer the primary author of code; they are the architect of intent, the judge of output, and the owner of consequences. Writing skill matters less; specification and verification skill matters more.

---

Q: When is vibe coding appropriate?
A: Prototypes, internal tools, greenfield projects with low security surface, personal scripts, and exploratory spikes. It shines when speed-to-working-prototype matters more than long-term maintainability or when the domain is well-understood by the AI.

---

Q: When is vibe coding a poor fit?
A: Security-critical code, financial logic, healthcare systems, and code operating at regulatory boundaries. Also poor for deep performance optimization, systems programming, or domain-specific logic the AI cannot reason about reliably (e.g., proprietary business rules).

---

Q: What does "you ship it, you own it" mean in vibe coding?
A: AI-generated code carries no transfer of responsibility. If you merge it, you are accountable for its behavior, security, and correctness — exactly as if you typed it yourself. Vibe coding doesn't reduce liability, it redirects authorship.

---

Q: How does vibe coding differ from traditional pair programming with an AI?
A: In pair programming you read and understand everything being written in real time. In vibe coding you tolerate more opacity — you often accept diffs without line-by-line comprehension, relying on tests, type checking, and runtime behavior to validate correctness.

---

Q: What skill level is required to vibe code effectively?
A: Higher than often assumed. You need enough domain knowledge to (a) detect wrong output, (b) write precise specifications, and (c) recognize security or correctness issues in generated code. Novices who can't detect hallucinations are most at risk.

---

Q: What is the "10x productivity" claim about vibe coding and what's the nuance?
A: The claim is that vibe coding can produce working software 5–10x faster for certain tasks. The nuance: speed gains are real for greenfield and well-scoped work but can reverse on maintenance — AI-generated code without clear intent accumulates technical debt faster than hand-written code.

---

Q: How does vibe coding change the developer's primary leverage point?
A: From implementation to specification. The highest-leverage skill becomes writing clear, constraint-rich prompts and knowing how to decompose problems so the AI produces correct, composable pieces rather than one large, opaque blob.

---

