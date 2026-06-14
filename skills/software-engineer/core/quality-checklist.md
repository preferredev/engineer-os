# Core quality checklist

Run the relevant checklist at each phase. Treat unchecked load-bearing items as
blockers, not warnings. Skills may add domain-specific checks on top of these.

## Pre-flight (before writing any code)

- [ ] I can restate the task and its acceptance criteria in one sentence.
- [ ] I have read every file I intend to modify, in full.
- [ ] I read the immediate collaborators (callers, callees, types, tests).
- [ ] I confirmed the real signatures of all APIs/types/imports I will call.
- [ ] I identified the existing pattern/convention to follow in this codebase.
- [ ] I checked that any dependency I plan to use is already available.
- [ ] For a bug: I reproduced the failure first.
- [ ] I listed explicit assumptions for any ambiguous, load-bearing choice.
- [ ] I know how I will verify the result before I start.

## In-flight (while implementing)

- [ ] The diff touches only files the task requires.
- [ ] No placeholders, TODOs, or stubbed returns remain.
- [ ] I matched existing naming, formatting, and error-handling style.
- [ ] No reformatting/renaming/refactoring of unrelated code.
- [ ] Inputs/preconditions validated at the boundary; errors not swallowed.
- [ ] No new abstraction with a single caller and no stated need.
- [ ] Magic values are named or justified inline.
- [ ] No unused imports, vars, dead branches, or commented-out code introduced.
- [ ] No new dependency added without justification.

## Pre-submit (before claiming done)

- [ ] I ran the build / type-check. Result captured.
- [ ] I ran the linter/formatter if the project uses one. Result captured.
- [ ] I ran the relevant tests. Pass/fail counts captured.
- [ ] For a bug fix: repro failed before, passes after (shown).
- [ ] For new behavior: ≥1 happy-path test and ≥1 edge/failure test added.
- [ ] For a refactor: behavior parity shown; no unintended contract change.
- [ ] I did not delete or weaken tests to make the change pass.
- [ ] If I could not run anything: I said so and gave exact verify commands.
- [ ] My report lists residual risks and anything unverified.
- [ ] Final scan for AI-tells: filler intros, restated-code comments, hedging.

## Severity guide

- **Blocker:** invented API/file/dependency, unverified success claim,
  placeholder in a "complete" deliverable, edited an unread file, swallowed
  error on a critical path, deleted a test to pass.
- **Must-fix:** unrequested rewrite/refactor, missing edge-case test, magic
  value on a load-bearing path, style mismatch with the file.
- **Nit (note as follow-up):** style preferences, optional refactors.
