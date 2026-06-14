# AI observability checklist

If you cannot see what the model received, produced, and cost, you cannot
operate, debug, or improve the system. Instrument from the first call — not
after the incident.

## Log per request (minimum)
- [ ] Correlation/request id (and run id for multi-step flows).
- [ ] Timestamp + environment (dev/staging/prod).
- [ ] Prompt template id + version.
- [ ] Model name + all params (temperature, max tokens, top_p, seed).
- [ ] Full input (resolved prompt + variables), redacted for secrets/PII as
      policy requires.
- [ ] Raw model output (verbatim).
- [ ] Parsed/validated output and whether validation passed.
- [ ] Token counts (input/output) and computed cost.
- [ ] Latency: total and per step / per model call.
- [ ] Errors, timeouts, refusals, retries (with reason + attempt count).

## Tool / retrieval calls
- [ ] Each tool call: name, arguments, result/error, latency.
- [ ] Retrieval: query, retrieved chunk ids + scores, top_k, filters.
- [ ] Which retrieved chunks were actually used in the answer (for grounding).

## Tracing
- [ ] Multi-step flows are a single trace (request -> retrieval -> model ->
      tools -> output) via one id.
- [ ] Step boundaries and durations are visible to find the slow/failing step.

## Aggregate metrics & alerting
- [ ] Quality metric tracked over time (from eval runs and/or sampled prod).
- [ ] Latency p50/p95/p99 and cost/day dashboards.
- [ ] Error/refusal/validation-failure rates with alert thresholds.
- [ ] Cost spike + latency regression alerts.
- [ ] Schema-validation failure rate (a leading indicator of breakage).

## Feedback & data loop
- [ ] User feedback / thumbs / corrections captured and linked to the request.
- [ ] Production failures are easy to pull into the eval dataset.
- [ ] Sampled prod traffic is periodically scored to catch silent regressions.

## Privacy & retention
- [ ] PII/secret redaction policy applied to logs.
- [ ] Retention window defined; sensitive payloads access-controlled.

## Smell test
If a user reports "the AI gave a wrong answer," you should be able to pull the
exact request, see the prompt version, model, retrieved context, tool calls,
output, and cost in under a minute. If you can't, observability is incomplete.
