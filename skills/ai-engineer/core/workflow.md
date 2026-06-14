# Core workflow

The loop is non-negotiable for in-scope coding tasks. Each phase has an exit
gate; do not advance until the gate passes. Skills may add phases, but may not
remove these.

## Phase 1 — Understand
**Goal:** know exactly what success looks like.
- Restate the task in one sentence.
- Enumerate acceptance criteria. If none given, propose them and proceed.
- Classify the task: feature / bugfix / refactor / test / review / design.
**Gate:** you can state "done means ___" without hedging.

## Phase 2 — Investigate
**Goal:** replace assumptions with facts about THIS codebase.
- Read every file you'll touch and its immediate collaborators.
- Verify the real signature of each external API/type/import (source or docs).
- Find the existing convention for this kind of change and plan to match it.
- Confirm any dependency you plan to use is already available.
- For bugs: reproduce first. A bug you can't reproduce, you can't confirm fixed.
**Gate:** zero unverified APIs/files/deps remain in your plan.

## Phase 3 — Plan
**Goal:** a reviewable plan before code exists.
- List files to change and the specific change in each.
- State explicit assumptions for ambiguous, load-bearing decisions.
- Decide the verification method now (which tests/commands).
- If the plan is large or risky, share it before implementing.
**Gate:** plan is concrete enough that someone else could execute it.

## Phase 4 — Implement
**Goal:** the smallest correct change that fits the codebase.
- Follow the plan; if reality contradicts it, return to Investigate, don't
  improvise silently.
- Minimal diff. No placeholders. No drive-by edits.
- Handle errors at the right layer; validate at boundaries.
**Gate:** code is complete, not stubbed.

## Phase 5 — Verify
**Goal:** proof, not optimism.
- Build / type-check. Run the targeted tests. Run the repro for bug fixes.
- Capture actual output (counts, errors, before/after).
- If verification fails, fix and re-verify — never report a failing change as
  done.
- If you have no environment to run in, say so explicitly and provide the exact
  commands plus expected results.
**Gate:** you have evidence, or an explicit statement of what's unverified.

## Phase 6 — Report
**Goal:** an honest, actionable summary.
- What changed (per file) and why.
- Evidence it works (the verification output).
- Residual risks, untested paths, and decisions the user should confirm.

## Decision rules
- **Ambiguity:** if the wrong guess is cheap to reverse, pick the most
  conventional option and note it. If expensive, ask.
- **Conflict between task and codebase reality:** trust the codebase; surface
  the conflict.
- **Scope creep temptation:** capture as a follow-up, don't act on it.
- **Uncertainty:** prefer a smaller change you can fully verify over a larger
  one you can only partly verify.
