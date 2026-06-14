# Agent design checklist

An "agent" = a model that dynamically chooses steps/tools in a loop until done.
It is the highest-cost, highest-failure architecture. Use it only when steps
cannot be predetermined. If you can draw the flowchart up front, build a
deterministic workflow instead.

## Gate: should this be an agent at all?
NO (use a workflow) if:
- [ ] The steps are known in advance.
- [ ] The branching is small and enumerable.
- [ ] A fixed sequence of prompts/tools produces the result.

YES (agent justified) only if:
- [ ] The required steps depend on intermediate results you cannot enumerate.
- [ ] The task needs open-ended planning/exploration.
- [ ] You have evals + observability to operate a non-deterministic loop.

## If you build an agent, design these
### Scope & control
- [ ] Hard cap on iterations / depth; defined behavior when the cap is hit.
- [ ] Small, typed tool set; each tool has a clear contract and validation.
- [ ] Mutating/irreversible tools are gated (confirmation, allow-list, dry-run).
- [ ] A clear, checkable "done" condition (not "until the model feels finished").
- [ ] Cost/latency budget per run, enforced.

### Reliability
- [ ] Tool errors are fed back so the agent can recover, with bounded retries.
- [ ] Loop/oscillation detection (repeating the same call) -> stop + fallback.
- [ ] State/memory is explicit and bounded; context growth is managed.
- [ ] Deterministic fallback path when the agent fails or stalls.

### Safety
- [ ] Tool outputs and retrieved content treated as untrusted (injection-safe).
- [ ] Least-privilege tool access; no broad write/exec without gating.
- [ ] Sensitive actions logged and, where needed, human-in-the-loop.

### Observability
- [ ] Full trace per run: every thought/decision, tool call + args + result,
      tokens, cost, and latency per step, tied to a run id.
- [ ] Failed/aborted runs are queryable with the reason.

## Evaluate the agent
- [ ] Task-success rate on a real task set (end-to-end), not single steps.
- [ ] Steps-to-success / tool-call efficiency (catch wandering).
- [ ] Cost + latency distribution per run vs budget.
- [ ] Behavior on adversarial and impossible tasks (does it stop safely?).

## Agent failure modes to test
- Infinite/again-and-again loops; never reaching "done".
- Calling the wrong tool or with malformed args.
- Ignoring a tool error and hallucinating success.
- Runaway cost from too many steps.
- Prompt injection via tool/retrieved output redirecting the agent.

## Decision output
State: why a workflow was insufficient, the tool set, the iteration/cost caps,
the done-condition, and the end-to-end success rate.
