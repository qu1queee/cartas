Q: What are the main frontier AI model providers as of 2025–2026?
A: Anthropic (Claude), Google DeepMind (Gemini), OpenAI (GPT/o-series), Meta (Llama), Mistral, and xAI (Grok). Each targets different tradeoffs between capability, speed, cost, and openness.

---

Q: What does "frontier model" mean?
A: A model at or near the current state-of-the-art on benchmarks like MMLU, HumanEval, or GPQA — typically large, closed, and expensive to train. Frontier models set the capability ceiling that others are measured against.

---

Q: What is a model family and why do providers offer tiers?
A: A family is a set of models sharing architecture/training lineage but scaled differently. Tiers (e.g. Opus/Sonnet/Haiku, GPT-4o/mini) let users trade off intelligence vs. latency vs. cost per token.

---

Q: What are the three capability tiers in the Claude 4 family and their positioning?
A: Opus (most capable, complex reasoning), Sonnet (balanced performance and speed), Haiku (fastest, cheapest, lightweight tasks). Each maps to a different use-case budget.

---

Q: What are the Gemini model tiers and their analogues?
A: Ultra (flagship, hardest tasks), Pro (general purpose, API default), Flash (speed-optimized, low latency), Nano (on-device, smallest footprint). Flash ≈ Sonnet; Nano ≈ Haiku in positioning.

---

Q: What is the OpenAI o-series (o1, o3) and how does it differ from GPT-4o?
A: The o-series uses chain-of-thought "thinking" tokens at inference time to solve hard reasoning problems. GPT-4o is a fast general model; o1/o3 sacrifice speed for accuracy on math, coding, and science tasks.

---

Q: What makes Meta's Llama models strategically different from Claude, GPT, and Gemini?
A: Llama models are open-weights — weights are publicly released, allowing anyone to download, fine-tune, and self-host. This enables community fine-tunes, avoids API costs, and keeps data on-premise.

---

Q: What is Mistral's key architectural contribution to the open-source ecosystem?
A: Mixtral — a Mixture-of-Experts (MoE) model that activates only a subset of parameters per token, giving near-large-model quality at much lower inference cost. Released under open weights.

---

Q: What is Mixture of Experts (MoE)?
A: An architecture where the model contains multiple "expert" sub-networks. A routing layer selects a small number of experts per token. Total parameters are large, but compute per forward pass is small — better efficiency at scale.

---

Q: What is xAI's Grok and its differentiating feature?
A: Grok is xAI's LLM integrated with real-time X (Twitter) data, giving it live web context that most models lack without retrieval augmentation. Positioned as less filtered and more opinionated than competitors.

---

Q: What is Constitutional AI (CAI)?
A: Anthropic's training method where a model critiques and revises its own outputs against a written "constitution" of principles — reducing reliance on human labelers for harmlessness feedback. Used in Claude's RLAIF pipeline.

---

Q: What is the difference between RLHF and RLAIF?
A: RLHF (Reinforcement Learning from Human Feedback) uses humans to rank outputs. RLAIF (from AI Feedback) uses a separate model to generate preference labels, scaling feedback without proportional human cost. Anthropic uses RLAIF via CAI.

---

Q: What is Anthropic's stated core safety philosophy?
A: "Responsible development and maintenance of advanced AI for the long-term benefit of humanity." Anthropic frames itself as a safety company that builds frontier models to stay at the frontier and influence norms — not to cede it to less safety-focused labs.

---

Q: What is Google DeepMind's approach to multimodality in Gemini?
A: Gemini was designed natively multimodal from pretraining — trained on interleaved text, image, audio, and video rather than bolted-on vision adapters. This differs from GPT-4V which added vision post-hoc to a text model.

---

Q: What does "context window" mean and why does size matter?
A: The maximum number of tokens (words/subwords) a model can process in one call — both prompt and response combined. Larger windows let you pass entire codebases, long documents, or multi-turn history without chunking.

---

Q: What are the approximate context windows of major models (2025)?
A: Claude 3.5/4: 200K tokens. Gemini 1.5 Pro/Flash: 1M tokens (up to 2M in some configs). GPT-4o: 128K tokens. Llama 3: 128K tokens. Gemini holds the largest publicly available window.

---

Q: What is "extended thinking" in Claude?
A: A mode (available on Opus and Sonnet) where the model reasons through a problem in a scratchpad before producing the final answer — similar to OpenAI's o1. Thinking tokens are billed but improve accuracy on hard tasks.

---

Q: What is prompt caching and which providers offer it?
A: Prompt caching stores the KV-cache of a repeated prefix so subsequent requests reuse it, reducing latency and cost. Anthropic offers it on Claude (cache writes cost more; cache reads are cheaper). OpenAI has a similar feature.

---

Q: What is the Anthropic Batch API?
A: An API mode that processes requests asynchronously in bulk at ~50% lower cost than real-time. Useful for large-scale evaluations, dataset generation, or any workload that tolerates hours of latency.

---

Q: What is "tool use" (function calling) in LLMs?
A: The ability for a model to emit a structured call to an external function/API mid-generation, receive the result, and incorporate it into the response. All major providers support this (Anthropic: tool use; OpenAI: function calling; Gemini: function declarations).

---

Q: What is "computer use" in Claude?
A: An Anthropic capability (beta, Claude 3.5+) where the model can control a computer — taking screenshots, moving the mouse, clicking, and typing — to autonomously complete GUI-based tasks.

---

Q: What are Artifacts in Claude?
A: A UI feature in Claude.ai where the model generates self-contained content (code, HTML, SVGs, documents) in a side panel that can be previewed, edited, and exported — separate from the conversation text.

---

Q: What is RAG (Retrieval-Augmented Generation)?
A: A pattern where relevant documents are retrieved from a vector database at query time and injected into the model's context, grounding answers in up-to-date or proprietary information without fine-tuning.

---

Q: What is the difference between RAG and fine-tuning for adding knowledge to a model?
A: RAG is dynamic — retrieves facts at inference time, easy to update, keeps source attribution. Fine-tuning bakes knowledge into weights — faster at inference, harder to update, no automatic citation, can hallucinate stale facts.

---

Q: What is "grounding" in the context of Gemini?
A: Google's term for connecting Gemini to real-time Google Search or Vertex AI data stores, so responses cite live web sources. Analogous to RAG but built into the Google ecosystem.

---

Q: What is speculative decoding?
A: An inference optimization where a small "draft" model generates candidate tokens quickly, and the large model verifies them in parallel. Accepted tokens are kept; rejected tokens trigger a fresh generation — net result is faster output with identical quality.

---

Q: What is quantization in model deployment?
A: Reducing the numerical precision of model weights (e.g. FP32 → INT8 or INT4) to shrink memory footprint and speed up inference, at a small quality cost. Critical for running large models on consumer hardware.

---

Q: What are embeddings and how do they relate to LLMs?
A: Embeddings are dense vector representations of text (or images) that capture semantic meaning. LLMs produce them as intermediate representations. Embedding-only models (e.g. text-embedding-3, Gemini embedding) are used for similarity search in RAG pipelines.

---

Q: What is OpenAI's GPT-4o and what does the "o" stand for?
A: GPT-4o is OpenAI's "omni" model — a single model handling text, image, and audio natively end-to-end, replacing the patchwork of separate models. It's faster and cheaper than GPT-4 Turbo.

---

Q: What is the difference between a closed model and an open-weights model?
A: Closed models (Claude, GPT-4o, Gemini) expose only an API — weights are proprietary. Open-weights models (Llama, Mixtral) release the weights publicly, enabling local deployment and arbitrary fine-tuning.

---

Q: What is instruction tuning?
A: Post-pretraining fine-tuning on (instruction, response) pairs to teach a model to follow directions. All chat models go through this — it's what converts a raw next-token predictor into an assistant.

---

Q: What is RLHF in plain terms?
A: Humans rank pairs of model outputs; those rankings train a "reward model"; the LLM is then fine-tuned with RL to maximize the reward model's score. Aligns the model toward human-preferred responses.

---

Q: What benchmark is used to measure general knowledge and reasoning across subjects?
A: MMLU (Massive Multitask Language Understanding) — 57 subjects from elementary math to law. Common baseline for comparing frontier models, though labs are increasingly training to it, reducing its signal.

---

Q: What is HumanEval and what does it test?
A: An OpenAI benchmark of 164 Python programming problems. A model generates a function body; unit tests verify correctness. Widely used to compare coding ability across models.

---

Q: What is GPQA and why is it used for frontier evals?
A: Graduate-Level Google-Proof Q&A — hard science questions that are difficult to answer by searching. Used to test true reasoning vs. retrieval, because the questions require multi-step expert knowledge.

---

Q: What is "hallucination" in LLMs?
A: When a model generates confident-sounding but factually incorrect or fabricated information. A fundamental failure mode driven by the model optimizing for plausible text, not verifiable truth.

---

Q: How do providers approach reducing hallucination?
A: Common strategies: RLHF to reward honesty, retrieval augmentation (RAG/grounding), citation generation, refusal training ("I don't know"), and extended thinking for verification before output.

---

Q: What is multimodal capability and which models support text + image + audio + video?
A: Multimodal means processing multiple input types. Gemini 1.5+ supports text, image, audio, and video natively. GPT-4o supports text, image, and audio. Claude 3+ supports text and image. Video is still mostly Gemini's differentiator.

---

Q: What is Anthropic's Model Spec?
A: A published document defining Claude's values, how it should prioritize between being helpful, harmless, and honest, and how it should reason about edge cases — a more granular elaboration of Constitutional AI principles.

---

Q: What is the "alignment" problem in AI?
A: The challenge of ensuring a powerful AI system pursues goals that are actually beneficial to humans, even as capability scales. Misalignment risks range from unhelpful outputs to, in extreme scenarios, goals actively contrary to human welfare.

---

Q: What is Anthropic's "responsible scaling policy" (RSP)?
A: A commitment to evaluate models at each capability level before deploying them further — pausing deployment if safety evaluations indicate dangerous capabilities (e.g. CBRN uplift, cyberoffense). Ties capability advancement to demonstrated safety.

---

Q: What is the MCP (Model Context Protocol)?
A: An open standard from Anthropic for connecting LLMs to external tools and data sources in a composable way — like a USB-C standard for AI integrations. Allows models to call databases, APIs, and local services through a unified interface.

---

Q: What is an AI agent and how does it differ from a single LLM call?
A: An agent is a system where an LLM iteratively takes actions (tool calls, memory reads, sub-task delegation) and observes results in a loop until a goal is reached — not just one prompt-in / response-out.

---

Q: What is "agentic" behavior in the context of Claude or GPT-4o?
A: The model autonomously plans, uses tools, executes multi-step workflows, and makes decisions without human input at each step. Requires careful sandboxing and permission design to avoid unintended side-effects.

---

Q: What is Vertex AI in relation to Gemini?
A: Google's enterprise ML platform, which hosts Gemini models with additional features: fine-tuning, grounding with Google Search, RAG engine, model evaluation, and data governance. The enterprise deployment path for Gemini.

---

Q: What is Amazon Bedrock and how does it relate to model providers?
A: AWS's managed service for accessing foundation models from multiple providers (Anthropic Claude, Meta Llama, Mistral, Stability AI, etc.) via a unified API — without managing inference infrastructure.

---

Q: What is the difference between a system prompt and a user prompt?
A: The system prompt sets persistent instructions (persona, rules, context) that frame the whole conversation. The user prompt is the per-turn input. Models treat system prompts as higher-authority instructions than user turns.

---

Q: What is "temperature" in model inference?
A: A parameter (0–1+) controlling output randomness. Temperature 0 = near-deterministic, picks the highest-probability token each step. Higher values increase diversity and creativity but also increase errors and hallucinations.

---

Q: What is "top-p" (nucleus sampling)?
A: An alternative to pure temperature sampling — at each step, only the smallest set of tokens whose cumulative probability exceeds p are considered. Prevents the model from sampling very low-probability (weird) tokens while preserving diversity.

---

Q: What does "tokens per second" (TPS) measure and why does it matter?
A: The speed at which a model generates output tokens. Higher TPS = faster response for the user. Haiku/Flash/Gemini Flash are optimized for high TPS; Opus/Ultra sacrifice TPS for quality.

---

Q: What is time-to-first-token (TTFT)?
A: The latency from sending a request to receiving the first output token — perceived "responsiveness." Distinct from throughput (TPS). Critical for interactive applications where users see streaming output.

---
