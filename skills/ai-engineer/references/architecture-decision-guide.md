# Architecture decision guide

Goal: pick the **simplest architecture that passes the eval**. Complexity is a
cost (latency, money, bugs, surface area), not a sign of sophistication. Always
start at tier 1 and only climb when the tier below provably fails.

## The ladder (climb only on evidence)

### Tier 1 — Single prompt (+ structured output)
Use when the task fits in context and needs no live data or actions.
- Covers: classify, extract, summarize, rewrite, generate, route.
- Add structured output (JSON schema / function call) whenever the result is
  consumed by code.
- Climb only if: the task needs current/external data, multiple dependent
  steps, or a corpus that doesn't fit context.

### Tier 2 — Deterministic workflow
A fixed, code-orchestrated sequence of prompts/tools/functions.
- Use when the steps are **known in advance** (e.g. extract -> validate ->
  summarize).
- This is the correct answer for most "agent" requests. Prefer it over an agent
  whenever you can draw the flowchart up front.
- Climb only if: the next step genuinely depends on model decisions you cannot
  enumerate.

### Tier 3 — Tool / function calling
Give the model typed tools to fetch data or take actions.
- Use when the model needs live data, computation, or side effects.
- Keep the tool set small and typed; validate arguments; gate mutations.
- Can be embedded inside a tier-2 workflow without becoming an agent.

### Tier 4 — RAG (retrieval-augmented)
Retrieve relevant chunks and put them in context.
- Use only when the knowledge is too large or too fresh for the prompt.
- Gate with `rag-decision-checklist.md`. Most "we need RAG" cases are solved by
  pasting the doc or a tier-3 lookup.

### Tier 5 — Agent (dynamic loop)
Model chooses steps/tools iteratively until done.
- Use only when steps **cannot** be predetermined and the task needs
  open-ended planning.
- Highest cost, latency, and failure surface. Gate with
  `agent-design-checklist.md`. Bound iterations and tools hard.

### Tier 6 — Fine-tuning
Change the model weights.
- Use only when ALL hold: you have a labeled dataset, an eval, and prompting +
  retrieval have hit a measured ceiling; and the task is narrow/stable.
- Good for: format/style adherence, latency/cost reduction on a fixed task,
  classification at scale. Bad for: adding fresh knowledge (use retrieval).
- Never a first move and never without an eval to prove it helped.

## Decision questions (answer in order)
1. Does it fit in context with no live data? -> Tier 1.
2. Are the steps known ahead of time? -> Tier 2 (+ tier 3 tools as needed).
3. Does it need live data/actions? -> add Tier 3.
4. Is the knowledge corpus too big/fresh for context? -> consider Tier 4.
5. Are the steps truly unknowable up front? -> consider Tier 5.
6. Hit a measured prompting/retrieval ceiling with data in hand? -> Tier 6.

## Anti-patterns
- Building an agent because it's impressive, when a workflow is deterministic.
- Adding RAG by reflex when the source doc fits in the prompt.
- Fine-tuning to "teach facts" (facts belong in retrieval/context).
- Multi-agent systems before a single prompt has been evaluated.
- Choosing the architecture before defining the task and the eval.

## Output of this step
Write: the tier chosen, the tiers rejected and why, and the one piece of
evidence (eval gap, context size, latency need) that justified climbing.
