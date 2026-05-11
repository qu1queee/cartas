Q: What three elements make a coding prompt effective?
A: Context (what exists, what language/framework, relevant constraints), goal (what the output should do or be), and acceptance criteria (how you'll know it's correct). Missing any one of these causes the AI to fill in the gap with assumptions.

---

Q: What is the "plan-first" prompting pattern?
A: Ask the AI to produce a written plan or outline before writing any code. Review the plan, correct it if needed, then prompt it to implement. This catches misunderstandings at the cheap stage (text) rather than the expensive stage (code you now have to debug or discard).

---

Q: What is iterative refinement in prompting and why is it more effective than one big prompt?
A: Breaking a task into sequential prompts, each building on validated prior output. Each step stays within scope the AI can reason about accurately, and errors are caught before they propagate. One large prompt risks coherent-looking but subtly wrong output across many components.

---

Q: What is negative prompting in a coding context?
A: Explicitly stating what you do NOT want: "do not add error handling for X", "do not introduce new dependencies", "do not refactor anything outside this function." Negative constraints prevent AI from adding unrequested features or abstractions.

---

Q: How should you provide existing code as context in a prompt?
A: Paste the minimal relevant slice — the function signature, the type definitions, and the immediate callers — not the entire file. Excess context dilutes focus; the AI may over-fit to irrelevant code or hit context limits. Annotate what role each piece plays.

---

Q: What is the "minimal reproducible example" prompt pattern?
A: When debugging, strip the problem down to the smallest code that still exhibits the issue, then prompt with that. Avoids the AI diagnosing irrelevant surrounding code and produces a targeted, verifiable fix.

---

Q: How do you prompt for a refactor vs. a new feature?
A: For refactors: specify what behavior must be preserved and what structural property you want after (e.g., "extract this logic into a pure function, no behavior change"). For features: specify inputs, outputs, and side effects. Conflating the two often produces a partial rewrite that changes both.

---

Q: What is chain-of-thought prompting and when does it help for code?
A: Asking the model to reason step-by-step before producing output ("think through the edge cases before writing the function"). Helps for algorithmic problems, complex logic, and tricky state machines. Less useful for boilerplate or simple CRUD.

---

Q: How do you handle a prompt where the AI produces something plausible-looking but wrong?
A: Don't re-prompt with "that's wrong, fix it" — be specific. Identify exactly what's wrong, explain why, and re-state the constraint the AI violated. Vague correction prompts often produce a different wrong answer.

---

Q: What is the "rubber duck" prompting pattern?
A: Writing out the problem in full detail as a prompt — even before asking the AI — often surfaces the solution yourself. The act of specification forces precision. The AI's value is then confirming, filling gaps, or catching what you missed.

---

Q: How do you prompt AI to explain its own output?
A: After receiving code, ask: "What are the edge cases this doesn't handle?" or "What assumptions does this code make?" This surfaces blind spots the AI knows about but didn't volunteer, and reveals whether the logic is actually understood.

---

Q: How do you prompt for debugging an error you can't understand?
A: Provide: (1) the full error message and stack trace, (2) the code that triggered it, (3) what you expected to happen, and (4) what you already tried. Skipping (3) or (4) causes the AI to suggest things you've already ruled out.

---

Q: What is the risk of prompting with "make it better"?
A: Undefined success criteria cause the AI to optimize for whatever proxy it prefers — often adding abstraction, comments, or features you didn't want. Always specify the dimension to improve: performance, readability, test coverage, or security.

---

Q: How do you decompose a large task into AI-friendly subtasks?
A: Identify the natural seams: data model first, then business logic, then API layer, then UI. Each subtask should have clearly defined inputs and outputs that the next subtask can consume. Avoid subtasks whose correctness depends on subtasks not yet built.

---

Q: What is the "one concern per prompt" principle?
A: Each prompt should address exactly one functional concern. Mixing "add auth" and "refactor the data layer" in one prompt risks the AI making coupled changes that are hard to separate, review, or roll back independently.

---

