# Eval-first workflow

The core discipline of this skill: **you build the eval before you claim the
feature works, and you never report an improvement without a number.**

## Why eval-first
- Demos lie. A feature that handles 5 hand-picked inputs routinely fails on the
  6th real one.
- Without a baseline you cannot know if a change helped, hurt, or did nothing.
- Prompt/model changes silently regress other cases; only an eval catches it.

## Step 0 — Define the task contract
- One-sentence task. Inputs. Output schema. Allowed values.
- Write 2–3 concrete **good outputs** and 2–3 **bad outputs** with the reason
  each is bad. This is the seed of your eval.

## Step 1 — Pick metrics
Choose the cheapest metric that captures correctness:
- **Exact / normalized match** — classification, extraction, routing.
- **Schema validity** — structured outputs (must parse + validate 100%).
- **Programmatic checks** — contains/!contains, regex, numeric range, set
  overlap (F1) for extraction.
- **Grounding check** — every claim supported by provided context.
- **LLM-as-judge with a rubric** — only for genuinely subjective quality, with
  a written rubric and spot-checked against humans. Never the default.
- Always pair quality with **latency (p50/p95)** and **cost per call**.

## Step 2 — Build the starter dataset (before building the solution)
- 5–20 cases to start; grow it from real/production failures over time.
- Mix: typical cases, boundary cases, and adversarial cases (empty input,
  wrong language, prompt injection, missing context, out-of-scope, very long
  input).
- Each case = { input, expected/expectation, notes }.

## Step 3 — Establish a baseline
- Run the simplest possible approach (tier 1) through the eval first.
- Record the score. This is the number every later change must beat.

## Step 4 — Iterate against numbers
- Change one thing at a time (prompt, model, retrieval, params).
- Re-run the full eval. Keep a changelog: change -> score delta.
- Inspect failures, not just the aggregate; add new failures to the dataset.
- Stop when you meet the target or hit diminishing returns (then decide whether
  to climb the architecture ladder).

## Step 5 — Guard against regression
- Re-run the eval on every prompt/model/retrieval change, including "tiny" ones.
- Treat a score drop as a failing test: do not ship it.
- Pin model + params; an unpinned model is an unannounced change.

## Step 6 — Report honestly
- Score vs target and vs baseline, with N (dataset size).
- Latency p50/p95 and cost/call.
- The failure cases you could not yet fix.
- If you could not run the eval (no keys/env): say so and hand over the dataset
  + exact commands + expected thresholds.

## Eval smells (fix these)
- A dataset of only happy-path cases.
- LLM-as-judge with no rubric and no human spot-check.
- Reporting averages while ignoring catastrophic individual failures.
- "It feels better" with no re-run.
- Tuning on the eval set until it memorizes (keep a held-out set as you grow).
