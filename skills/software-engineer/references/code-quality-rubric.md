# Code quality rubric

Do not justify a change with the word "clean" or "maintainable" as a vibe. Use
this operational, checkable definition. A change should measurably improve or
preserve these — never degrade them.

## 1. Readability
- **Reads in one pass.** A competent reader understands a function without
  jumping to three other files. Max ~1 level of non-obvious indirection.
- **Names describe role, not type or vagueness.** `usersById`, `retryCount` —
  not `data`, `obj`, `tmp`, `helper2`, `doStuff`.
- **Functions are small and single-purpose.** If describing it needs "and,"
  split it.
- **Consistent with the file it lives in.** Matches existing style, not yours.

## 2. Correctness & robustness
- **Inputs validated at boundaries**; invalid states are rejected early.
- **Errors handled at the right layer** and never silently swallowed.
- **Edge cases handled**: empty/null, zero, negative, very large, concurrent,
  and failure paths — not just the happy path.
- **No off-by-one / resource leaks** (unclosed files, handles, connections).

## 3. Minimal footprint
- **Smallest diff that solves the task.** No unrelated edits.
- **No dead code shipped**: unused vars/imports, unreachable branches,
  commented-out blocks.
- **No speculative generality**: abstraction only on real, present need.

## 4. Consistency with the codebase
- **Same architecture and layering** as surrounding code.
- **Same error-handling, logging, and config patterns.**
- **Same formatting/lint rules** (run the project's formatter, don't hand-roll).

## 5. Maintainability
- **Comments explain why**, not what; complex intent is documented.
- **Magic values named** with rationale.
- **Public behavior/contracts are stable** unless the task is to change them.
- **Tests exist** for new behavior and for the bug being fixed.

## 6. Security & safety basics
- **No secrets hardcoded**; use existing config/secret patterns.
- **Untrusted input is sanitized/validated** before use (injection, path,
  deserialization).
- **Least privilege** for any new permission/scope/dependency.

## Scoring a change (quick pass/fail)
For each dimension, mark Pass / Degraded / N/A. Any **Degraded** on dimensions
2, 3, or 4 is a must-fix before reporting done. If a change does not improve any
dimension and the task didn't ask for it, do not make the change.
