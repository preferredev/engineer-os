# Eval tasks (for this skill)

Two suites: **trigger tests** (does the skill activate correctly?) and
**functional tests** (does the activated behavior meet the AI-engineering bar?).

## A. Trigger tests

| # | User prompt | Expected | Pass criteria |
|---|-------------|----------|---------------|
| T1 | "Add an AI feature to summarize tickets." | Activate | Defines contract + eval before prompting. |
| T2 | "Should we use RAG for our help docs?" | Activate | Applies rag-decision-checklist; may reject RAG. |
| T3 | "Design an agent to triage emails." | Activate | Checks workflow-vs-agent; gates the agent. |
| T4 | "Write a prompt to extract invoice fields." | Activate | Uses structured output + grounding + eval. |
| T5 | "Reduce our LLM latency and cost." | Activate | Measures p50/p95 + cost; structured tradeoffs. |
| T6 | "Set up evals for our chatbot." | Activate | Builds dataset + metrics + baseline. |
| T7 | "Should we fine-tune a model for this?" | Activate | Requires data + eval + proven ceiling first. |
| T8 | "Rename this variable across the repo." | Stay off | Non-AI task -> general engineering skill. |
| T9 | "Write a poem about the ocean." | Stay off | Pure prose, no AI system being built. |

**Suite passes** when >= 8/9 match and no false-activation on T8/T9.

## B. Functional tests

### F1 — Eval-before-claim
- **Setup:** "Improve our classifier prompt."
- **Pass:** establishes/uses a dataset + baseline, reports before/after numbers.
- **Fail:** changes the prompt and says "better" with no metric.

### F2 — Simplest architecture
- **Setup:** task whose source fits in context.
- **Pass:** chooses tier-1 prompt; explicitly rejects RAG/agent with reasons.
- **Fail:** reaches for RAG/agent/fine-tuning by default.

### F3 — Workflow over agent
- **Setup:** task with knowable, fixed steps.
- **Pass:** builds a deterministic workflow; uses agent only for open-ended part
  (or not at all).
- **Fail:** builds an unbounded agent loop for enumerable steps.

### F4 — Structured output
- **Setup:** "return name and age for downstream code."
- **Pass:** schema-constrained output, validated, null on unknown.
- **Fail:** free-form text parsed by regex.

### F5 — Hallucination / grounding
- **Setup:** Q&A where some questions aren't covered by the source.
- **Pass:** grounded prompt + abstention; eval includes not-in-corpus cases.
- **Fail:** answers confidently outside the provided context.

### F6 — Guarded calls
- **Setup:** "call the model to enrich each row."
- **Pass:** timeout + bounded retry + output validation + fallback.
- **Fail:** raw call, no timeout/validation, output passed downstream.

### F7 — Observability
- **Setup:** "ship this AI endpoint."
- **Pass:** logs input/output/tool calls/tokens/cost/latency/errors with a
  request id.
- **Fail:** no logging plan.

### F8 — Fine-tuning discipline
- **Setup:** "fine-tune a model so it's more accurate."
- **Pass:** requires labeled data + eval + a measured prompting/retrieval
  ceiling first; offers cheaper options.
- **Fail:** proposes fine-tuning as the first step.

### F9 — Honest non-execution
- **Setup:** no API key/env available.
- **Pass:** states it can't run, hands over dataset + commands + expected
  thresholds.
- **Fail:** claims eval scores it never produced.

## Scoring
- **Release-ready** when: trigger suite passes AND >= 8/9 functional tests pass
  with ZERO Blocker failures (quality claim with no eval, unguarded call,
  ungrounded facts as truth, invented specifics, fine-tune-first, unlogged prod
  call).
- Tag each failure with the rule number from `anti-ai-slop-rules.md` so
  regressions are traceable across versions.
