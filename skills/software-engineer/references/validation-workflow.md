# Validation workflow

The loop is non-negotiable for in-scope tasks. Each phase has an exit gate; do
not advance until the gate passes. Verification is part of the task, not an
optional extra.

## Phase 1 — Understand
**Goal:** know exactly what success looks like.
- Restate the task in one sentence.
- Enumerate acceptance criteria. If none given, propose them and proceed.
- Classify the task: feature / modification / bugfix / refactor / test / review.
**Gate:** you can state "done means ___" without hedging.

## Phase 2 — Explore
**Goal:** replace assumptions with facts about THIS codebase.
- Read every file you'll touch, in full, plus its callers/callees and tests.
- Verify the real signature of each external API/type/import.
- Identify the existing convention for this kind of change and how to run the
  project's build, lint, and tests.
- Confirm any dependency you plan to use is already present.
**Gate:** zero unverified APIs/files/deps remain in your plan.

## Phase 3 — Reproduce (bug fixes)
**Goal:** see the failure before you fix it.
- Write or run a minimal repro / failing test.
- Identify the root cause, not just the symptom.
**Gate:** you can trigger the bug on demand.

## Phase 4 — Plan
**Goal:** a reviewable plan before code exists.
- List files to change and the specific change in each.
- State explicit assumptions for ambiguous, load-bearing decisions.
- Decide the validation commands now.
- Share the plan first if the change is large or risky.
**Gate:** plan is concrete enough that someone else could execute it.

## Phase 5 — Implement
**Goal:** the smallest correct change that fits the codebase.
- Follow the plan and the house style. If reality contradicts the plan, return
  to Explore — don't improvise silently.
- Minimal diff. No placeholders. No drive-by edits.
**Gate:** code is complete, not stubbed.

## Phase 6 — Test
**Goal:** lock in the new behavior.
- Add/update tests: happy path + ≥1 edge/failure case; regression test for bugs.
- Match the project's test framework and layout.
**Gate:** the new/updated tests express the intended behavior.

## Phase 7 — Validate
**Goal:** proof, not optimism.
- Run build / type-check, linter/formatter, and the relevant tests.
- Capture actual output (counts, errors, before/after for bugs).
- On failure, fix and re-run — never report a failing change as done.
- If you have no environment to run in, say so explicitly and provide the exact
  commands plus expected results.
**Gate:** you have evidence, or an explicit statement of what's unverified.

## Phase 8 — Explain
**Goal:** an honest, actionable summary.
- What changed (per file) and why.
- Evidence it works (the validation output).
- Residual risks, untested paths, and decisions the user should confirm.

## Decision rules
- **Ambiguity:** if the wrong guess is cheap to reverse, pick the most
  conventional option and note it. If expensive, ask.
- **Plan vs reality conflict:** trust the codebase; surface the conflict.
- **Scope creep temptation:** capture as a follow-up, don't act on it.
- **Uncertainty:** prefer a smaller change you can fully verify over a larger
  one you can only partly verify.
