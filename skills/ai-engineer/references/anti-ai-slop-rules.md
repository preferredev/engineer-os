# Anti-AI-slop rules

AI slop is AI engineering that looks sophisticated and measures nothing. These
are hard constraints. Each has a *why* and a *do-instead*.

## 1. No quality claim without an eval number
- **Banned:** "this improved it", "this is more accurate", "works well now" with
  no dataset or metric.
- **Why:** vibes don't survive contact with real inputs; you can't see
  regressions.
- **Do instead:** report score vs target and vs baseline, with N.

## 2. No agent when a workflow works
- **Banned:** building a dynamic agent loop for steps you can enumerate.
- **Why:** agents add cost, latency, and failure surface for no gain.
- **Do instead:** deterministic workflow; climb only on eval evidence.

## 3. No RAG by reflex
- **Banned:** vector DB + embeddings when the source fits in context or a single
  fetch answers it.
- **Why:** unnecessary infra, new failure modes, worse latency.
- **Do instead:** paste the doc or use a tool call; gate RAG with its checklist.

## 4. No fine-tuning without data + eval + ceiling
- **Banned:** fine-tuning as a first move, to "teach facts", or without an eval
  proving it beat prompting/retrieval.
- **Why:** expensive, slow to iterate, and usually solves the wrong problem.
- **Do instead:** exhaust prompting + retrieval, build the dataset and eval,
  then fine-tune only if it measurably wins.

## 5. No free-form text where structure is correct
- **Banned:** parsing the model's prose with regex when a schema/function call
  would do.
- **Why:** free text breaks silently and is unvalidatable.
- **Do instead:** schema-constrained output + validation.

## 6. No unguarded model calls
- **Banned:** a call with no timeout, no error handling, no output validation,
  passing raw output straight downstream.
- **Why:** the API will fail, stall, or return garbage — guaranteed at scale.
- **Do instead:** timeout + bounded retry + validate + fallback.

## 7. No ungrounded facts passed as truth
- **Banned:** surfacing model claims for factual tasks without grounding in
  context/tools.
- **Why:** hallucinations shipped as answers.
- **Do instead:** require grounding + citation, or abstention.

## 8. No invented specifics
- **Banned:** made-up model names, params, API shapes, pricing, context limits,
  or benchmark numbers.
- **Why:** confident fiction breaks builds and misleads decisions.
- **Do instead:** verify against docs/SDK, or label as unverified and check.

## 9. No unlogged production call
- **Banned:** shipping a model call you cannot later inspect.
- **Why:** you can't debug or improve what you can't see.
- **Do instead:** log inputs, outputs, tool calls, tokens, cost, latency,
  errors.

## 10. No untested failure path
- **Banned:** only testing the happy path; ignoring empty input, injection,
  refusals, timeouts, malformed output.
- **Why:** that's where AI features actually break in production.
- **Do instead:** put failure + adversarial cases in the eval.

## 11. No prompt prose theater
- **Banned:** long prompts full of "be smart/helpful/careful" filler that
  doesn't move a metric.
- **Why:** noise, cost, and false confidence.
- **Do instead:** keep instructions that change outputs; delete the rest (prove
  it by eval).

## 12. No silent model/prompt swap
- **Banned:** changing model or prompt without re-running the eval.
- **Why:** invisible regressions.
- **Do instead:** pin versions; re-evaluate every change.

## Self-check before sending
*Did I measure it? Is this the simplest architecture? Is every call guarded,
grounded, and logged? Did I invent any specifics?* Fix or disclose.
