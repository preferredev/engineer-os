# Core anti-slop rules

"Slop" is output that looks like engineering but isn't: plausible, confident,
and wrong or hollow. These are hard constraints for every EngineerOS skill. Each
has a *why* and a *do-instead* so the agent can self-correct.

## Definition: what "clean code" means here

Do not use the phrase "clean code" as a justification. It means, concretely:

1. **Reads in one pass.** A competent reader understands a function without
   jumping to three other files. Max ~1 level of non-obvious indirection.
2. **Names describe role, not type.** `usersById`, not `data`, `obj`, `temp`,
   `helper2`.
3. **Single responsibility per unit.** A function does one thing; if you need
   "and" to describe it, split it.
4. **No unreachable or unused code** shipped: no dead branches, unused vars,
   commented-out blocks, or unused imports.
5. **Errors are handled at the right layer** and never silently swallowed.
6. **Consistent with the file it lives in** — matches existing style, not your
   preferred style.

If a change doesn't measurably improve one of these, don't make it.

## Hard bans

### 1. No placeholders in a "complete" deliverable
- **Banned:** `...`, `pass  # TODO`, `throw new Error("not implemented")`,
  fabricated return values, `// rest of logic here`.
- **Why:** the user cannot run it; "looks done" hides that it isn't.
- **Do instead:** implement it, or explicitly mark the response as a partial
  draft and list exactly what remains.

### 2. No invented interfaces
- **Banned:** calling functions, methods, params, env vars, config keys, CLI
  flags, file paths, or package names you have not verified.
- **Why:** hallucinated APIs are the #1 cause of broken first attempts.
- **Do instead:** read the source/type/docs and confirm the real signature. If
  you cannot, say "unverified" and ask or flag it.

### 3. No editing an unread file
- **Banned:** changing a file based on its name or your assumptions about it.
- **Why:** you will clash with existing structure and break callers.
- **Do instead:** read the file (and its collaborators) before editing.

### 4. No unrequested changes
- **Banned:** reformatting, renaming, re-ordering imports, "modernizing", or
  refactoring code the task didn't ask you to touch.
- **Why:** it bloats the diff, hides the real change, and risks regressions.
- **Do instead:** minimal diff. Note suggested cleanups separately as
  follow-ups.

### 5. No speculative abstraction
- **Banned:** interfaces/base classes/factories/config layers/generic wrappers
  with one caller and no stated future requirement.
- **Why:** premature abstraction is harder to delete than to add later.
- **Do instead:** write the concrete code. Abstract on the second real use.

### 6. No silent error handling
- **Banned:** empty `catch {}`, `except: pass`, catching broad `Exception`/
  `Error` just to continue, swallowing then returning a default that masks
  failure.
- **Why:** turns a loud bug into a silent corruption.
- **Do instead:** handle the specific error, or let it propagate. Log/wrap with
  context. Only suppress when you can justify it in a comment.

### 7. No noise comments
- **Banned:** `# increment i` over `i += 1`; docstrings that restate the name;
  banner comments; AI-tell phrases like "Here's the updated code".
- **Why:** noise reduces signal and ages badly.
- **Do instead:** comment *why* (intent, trade-off, gotcha), not *what*.

### 8. No unverified success claims
- **Banned:** "this works", "this should fix it", "now it's production-ready"
  with nothing run.
- **Why:** false confidence costs the user a debugging cycle.
- **Do instead:** run it and show output, or say precisely what is unverified.

### 9. No gaming the tests
- **Banned:** deleting/skipping/weakening tests, or asserting trivially, to make
  a change "pass".
- **Why:** hides the defect and erodes the safety net.
- **Do instead:** fix the code so real tests pass; only change a test when the
  spec genuinely changed, and say so.

### 10. No magic values
- **Banned:** unexplained literals (`* 86400`, `retries = 7`, `0.85`) buried in
  logic.
- **Do instead:** named constant with a one-line reason, or an inline `why`.

### 11. No scope-mismatched solutions
- **Banned:** rewriting a subsystem to fix a typo; adding a dependency to avoid
  a 5-line function.
- **Do instead:** size the solution to the problem. Justify any new dependency.

## Self-check before sending
Ask: *Did I read the files? Did I run it? Did I invent anything? Is this the
smallest correct change that fits the codebase?* If any answer is uncertain, fix
it or disclose it.
