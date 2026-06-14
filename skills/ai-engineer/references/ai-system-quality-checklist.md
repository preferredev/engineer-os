# AI system quality checklist

The production bar for an AI feature. An item that doesn't apply should be
marked N/A with a reason, not silently skipped.

## 1. Task & contract
- [ ] Task is stated in one sentence with explicit inputs and outputs.
- [ ] Output contract is defined (format + schema + allowed values).
- [ ] Good-output and bad-output examples are written down.
- [ ] Non-goals / out-of-scope inputs are listed.

## 2. Architecture
- [ ] Chosen tier is the simplest that passes the eval.
- [ ] Cheaper tiers were considered and rejected with reasons.
- [ ] No agent where a fixed workflow suffices.
- [ ] No RAG where context fits or retrieval isn't needed.
- [ ] No fine-tuning without data + eval + a proven prompting ceiling.

## 3. Correctness & grounding
- [ ] Factual outputs are grounded in provided context or tool results.
- [ ] Model is instructed to abstain ("I don't know") instead of guessing.
- [ ] Unsupported claims are treated as defects, not style.
- [ ] Outputs are validated against the schema before use.

## 4. Reliability
- [ ] Every model/tool call has a timeout.
- [ ] Malformed output is detected and handled (re-ask, repair, or fail).
- [ ] Retries are bounded and only on retryable errors (not on 4xx/validation).
- [ ] There is a defined fallback when the model fails or refuses.
- [ ] Non-determinism is controlled where needed (low temp / fixed seed / cache).

## 5. Safety & failure modes
- [ ] Prompt-injection and jailbreak inputs are considered and tested.
- [ ] Untrusted retrieved/tool content is not treated as instructions.
- [ ] Tool calls that mutate state are gated (confirmation/allow-list/dry-run).
- [ ] PII / secret handling is intentional, not accidental.

## 6. Evals
- [ ] An eval dataset exists with checkable expectations.
- [ ] Adversarial and edge cases are included.
- [ ] Current score vs target and vs baseline is recorded.
- [ ] Eval is re-run on every prompt/model/retrieval change.

## 7. Observability
- [ ] Inputs, raw + parsed outputs, tool calls, tokens, cost, latency, errors
      are logged per request.
- [ ] Multi-step flows are traceable via a correlation id.
- [ ] Failures and refusals are queryable, not buried.

## 8. Cost & latency
- [ ] p50/p95 latency measured against the budget.
- [ ] Cost per call (and per step) measured against the ceiling.
- [ ] Context size is controlled (no dumping everything into the prompt).
- [ ] Cheaper model / caching / truncation evaluated where quality allows.

## 9. Maintainability
- [ ] Prompts/configs are versioned and tied to eval scores.
- [ ] Model + params are explicit and pinned, not implicit defaults.
- [ ] Changing the model/prompt is a reviewable, re-evaluable change.

## Release gate
Ship only when: contract defined, architecture justified, eval meets target,
observability live, latency/cost within budget, and the top failure modes have
a defined handling path. Otherwise, ship behind a flag and say what's missing.
