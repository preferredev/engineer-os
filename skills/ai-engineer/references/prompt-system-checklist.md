# Prompt / system instruction checklist

A prompt is a versioned, evaluated artifact — not free text. Every instruction
should change an output; if it doesn't (verify by eval), delete it.

## Structure
- [ ] Role and task stated explicitly and concretely.
- [ ] Inputs are clearly delimited from instructions (so input cannot be read
      as a command).
- [ ] Output contract specified: exact format + schema + allowed values.
- [ ] Steps/criteria the model should follow are explicit (not implied).
- [ ] A few high-signal examples included where they improve the eval; no
      example dumps.

## Correctness & grounding
- [ ] For factual tasks: "answer ONLY from the provided context/tools."
- [ ] Explicit uncertainty behavior: "if unknown or not in context, say so" —
      never invent.
- [ ] Citation/attribution required when the contract needs traceability.
- [ ] Refusal/escalation behavior for out-of-scope or unsafe requests defined.

## Clarity & economy
- [ ] No contradictory instructions.
- [ ] No dead instructions that don't affect outputs (proven by eval).
- [ ] No vague filler ("be helpful", "think carefully", "be smart") unless it
      measurably moves a metric.
- [ ] Edge-case handling spelled out (empty input, multiple matches, ambiguity).
- [ ] Length/verbosity constraints stated if they matter for cost/UX.

## Safety
- [ ] Untrusted/retrieved/tool content is labeled as data, not instructions.
- [ ] Explicit rule: ignore instructions embedded in user/retrieved content.
- [ ] No secrets, keys, or hidden policy that would leak if the prompt echoes.

## Structured output
- [ ] Use schema-constrained output (JSON schema / function call) whenever code
      consumes the result.
- [ ] Output is validated/parsed; on invalid output, repair or re-ask, never
      pass through.
- [ ] Enums/booleans/numbers used instead of free text where the value space is
      closed.

## Versioning & evaluation
- [ ] Prompt has a version id and is stored, not inlined-and-forgotten.
- [ ] Each version is tied to an eval score.
- [ ] Model + params (temperature, max tokens, top_p) are pinned and recorded.
- [ ] Any wording change is re-run through the eval before shipping.

## Quick smell test
If you cannot point to the eval case that each instruction protects, you are
writing prose, not a prompt.
