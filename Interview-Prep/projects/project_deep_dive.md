# 🛡️ Project Deep-Dive — Understand Your Own Code

> The goal is NOT to memorize scripts. It's to understand each project well enough that you can explain it naturally, like you're teaching a friend.

---

## The Common Pattern Across All 4 Projects

All your projects follow the same **Generate → Verify → Fix** loop:

```
Something generates output → Something checks it → If bad, fix and retry
```

| Project | Generator | Verifier | Fix Loop |
|:--------|:----------|:---------|:---------|
| RAG System | LLM generates answer | Critic checks for hallucinations | Refiner rewrites answer |
| Eval Dashboard | Prompts generate responses | 5-layer evaluation scores them | Statistical comparison picks winner |
| VS Code Extension | Coder writes code | Critic reviews code | Coder rewrites (max 3x) |
| Dev Automation | LLM generates Docker config | Critic boots containers | Generator fixes from error logs |

**Say this in any interview:** *"I'm drawn to the Generate-Verify-Fix pattern because autonomous AI systems need self-correction to be reliable."*

---

## Project 1: Agentic RAG with Self-Correction ⭐

### What it does (explain like teaching a friend):
"You upload PDF research papers. You ask a question. Instead of just throwing the question at an LLM and hoping for the best, the system:
1. **Searches** through the PDFs using 3 different search methods combined
2. **Generates** an answer with citations [1], [2] pointing to exact source passages
3. **Checks** every sentence — did the LLM make anything up?
4. **Fixes** any hallucinations and returns a clean answer"

### The 3 search methods (why not just one?):
- **Vector search (ChromaDB):** Finds passages that MEAN similar things. "Machine learning" matches "predictive modeling"
- **Keyword search (BM25/FTS5):** Finds exact words. "Adam optimizer" matches "Adam optimizer" — vector search might miss exact terms
- **Knowledge graph (Neo4j):** Connects entities across the document. "Adam optimizer" in section 3 and "learning rate" in section 5 are related — graph links them

These three are combined using **RRF (Reciprocal Rank Fusion)** — a simple formula that merges rankings without needing to normalize scores: `score = 1/(k+rank_from_search_1) + 1/(k+rank_from_search_2)`

### The hallucination check (why 3 layers?):
1. **Embedding similarity** — Is the claim semantically close to any source passage? Fast but has a blind spot
2. **NLI entailment** — Does the source SUPPORT, CONTRADICT, or is NEUTRAL to the claim? Catches "used Adam" vs "used SGD" (embeddings say these are similar but they're factually different!)
3. **Keyword overlap** — Do specific names/numbers in the claim actually exist in the source? Catches completely made-up stats

### Tech stack: Python, FastAPI, ChromaDB, SQLite + FTS5, Neo4j, Ollama (Llama 3.2), Docker

### Key files:
- `src/pipeline/orchestrator.py` — The brain. Calls Retriever → Generator → Critic → Refiner in a loop
- `src/retrieval/hybrid_search.py` — The 3-way search + RRF fusion
- `src/agents/critic.py` — Extracts claims, verifies each one concurrently with asyncio
- `src/agents/refiner.py` — Takes hallucinated claims, rewrites the answer

### Metrics defense:
- **97% hallucination reduction** — "I injected synthetic hallucinations into test responses and measured the Critic's detection rate across 25 queries"
- **25% retrieval improvement** — "Compared MRR@5 scores of vector-only vs full hybrid pipeline"

---

## Project 2: LLM Evaluation Dashboard

### What it does (explain like teaching a friend):
"Imagine you're writing prompts for ChatGPT and you have 3 different ways to ask the same question. Which one gives the best answer? This tool runs ALL of them automatically, scores every answer on 5 criteria, and tells you which prompt wins — with statistical proof, not just gut feeling."

### The 5 scoring layers:
1. **Latency** — How fast? (milliseconds, tokens per second)
2. **Token efficiency** — Did it ramble or was it concise? (output length vs reference length)
3. **Semantic quality** — Is the answer actually relevant? (embedding similarity to reference + LLM-as-judge)
4. **Hallucination rate** — Did it make stuff up? (same 3-layer check as the RAG project)
5. **Consistency** — Run the same prompt 3 times — do you get similar answers? (LLMs are non-deterministic)

### Why multiple repetitions?
"LLMs give different answers each time. Running each variant 3+ times lets me calculate statistical significance with Welch's t-test and effect size with Cohen's d. This tells me if Prompt A is ACTUALLY better than Prompt B, or if it was just luck."

### Tech: Python, FastAPI, SQLite, asyncio Semaphore (limits concurrent LLM calls), Ollama

---

## Project 3: AI Pair Programming Assistant (VS Code Extension)

### What it does (explain like teaching a friend):
"You type 'add authentication to my app' in VS Code. The extension reads your project, makes a plan, writes tests first, writes code, reviews its own code, and if something breaks, it captures the error and tries to fix itself. You approve everything before it touches your files."

### The 6 agents (think of them as 6 team members):
1. **Analyzer** — Scans your project, picks relevant files to read
2. **Planner** — Breaks the task into steps (JSON)
3. **Tester** — Writes unit tests FIRST (TDD — Test-Driven Development)
4. **Coder** — Writes the actual code to pass those tests
5. **Critic** — Reviews the code. If bad → sends feedback → Coder rewrites (max 3 attempts)
6. **Git** — Auto-commits with a meaningful message

### Self-healing:
"If a terminal command fails (like `npm install` errors), the orchestrator captures stderr, creates a NEW task: 'Fix this error: [error message]', and recursively triggers the entire agent loop. This is the self-healing pattern."

### Human-in-the-loop:
"The extension shows you a preview of all proposed file operations (create/edit/delete) in a JSON document. You click 'Approve & Apply' or 'Reject'. Nothing happens without your explicit approval."

### Tech: TypeScript (required for VS Code extensions), VS Code Extension API, Ollama, SQLite (telemetry)

---

## Project 4: Dev Environment Automation (CLI Tool)

### What it does (explain like teaching a friend):
"You type: `devenv generate 'Python Flask with Postgres and Redis'`

It generates a complete Docker development environment: Dockerfile, docker-compose.yml, and VS Code devcontainer config. But it doesn't just generate and hope — it actually BOOTS the containers, watches for 5 seconds, and if anything crashes, reads the Docker error logs and fixes the config automatically."

### The flow:
```
Your prompt → Generator Agent creates structured config (JSON)
           → Critic Agent runs docker-compose up in sandbox
           → If crash → reads docker logs → feeds back to Generator → retry (max 3x)
           → If success → writes files to disk
```

### Why Pydantic + Instructor?
"LLMs output unstructured text. You can't reliably parse 'Here's your Dockerfile: ...' from raw text. Instructor forces the LLM to return valid JSON that matches a Pydantic schema — so I get a validated `EnvironmentConfig` object with guaranteed fields like `dockerfile_content`, `compose_content`, etc."

### Tech: Python, Click (CLI framework), Rich (pretty terminal output), Docker, Pydantic + Instructor, Ollama

---

## Quick Tips for Explaining Projects

1. **Start with the problem, not the tech.** "The problem is that LLMs hallucinate" → then "here's how I solved it"
2. **Use analogies.** "The Critic is like a fact-checker at a newspaper"
3. **Draw the flow.** "Data flows: PDF → chunks → embeddings → search → generate → verify → respond"
4. **Admit limitations.** "If I rebuilt this, I'd swap SQLite for PostgreSQL for production scaling"
5. **Connect to the role.** "This project gave me experience with APIs, databases, and debugging pipelines — exactly what [ROLE] needs"
