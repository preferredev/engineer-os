# Anti-slop rules

"Slop" is code that looks like engineering but isn't: plausible, confident, and
wrong, hollow, or disruptive. These are hard constraints. Each has a *why* and a
*do-instead* so the agent can self-correct.

## 1. No placeholders in a "complete" deliverable
- **Banned:** `...`, `pass  # TODO`, `throw new Error("not implemented")`,
  fabricated return values, `// rest of logic here`.
- **Why:** the user cannot run it; "looks done" hides that it isn't.
- **Do instead:** implement it, or explicitly mark the response as a partial
  draft and list exactly what remains.

## 2. No invented interfaces, files, or dependencies
- **Banned:** calling functions, methods, params, env vars, config keys, CLI
  flags, file paths, or packages you have not verified exist.
- **Why:** hallucinated APIs and imports are the #1 cause of broken first
  attempts.
- **Do instead:** read the source/type/docs and confirm. If you cannot, label
  it "unverified" and ask or flag it. Never `npm install` an imagined package.

## 3. No editing an unread file
- **Banned:** changing a file based on its name or your assumptions about it.
- **Why:** you will clash with existing structure and break callers.
- **Do instead:** read the file (and its collaborators) before editing.

## 4. No unrequested changes
- **Banned:** reformatting, renaming, re-ordering imports, "modernizing", or
  refactoring code the task didn't ask you to touch.
- **Why:** it bloats the diff, hides the real change, and risks regressions.
- **Do instead:** minimal diff. Note suggested cleanups separately as
  follow-ups.

## 5. No broad rewrite for a small change
- **Banned:** rewriting a module/file to fix a typo or add one branch.
- **Why:** huge review surface, high regression risk, lost git history.
- **Do instead:** size the solution to the problem; make the local change.

## 6. No speculative abstraction
- **Banned:** interfaces/base classes/factories/wrappers with one caller and no
  stated future requirement.
- **Why:** premature abstraction is harder to delete than to add later.
- **Do instead:** write the concrete code. Abstract on the second real use.

## 7. No silent error handling
- **Banned:** empty `catch {}`, `except: pass`, catching broad `Exception`/
  `Error` just to continue, swallowing then returning a masking default.
- **Why:** turns a loud bug into a silent corruption.
- **Do instead:** handle the specific error or let it propagate; wrap with
  context. Only suppress with a justifying comment.

## 8. No noise comments
- **Banned:** `# increment i` over `i += 1`; docstrings restating the name;
  banner comments; AI-tells like "Here's the updated code".
- **Why:** noise reduces signal and ages badly.
- **Do instead:** comment *why* (intent, trade-off, gotcha), not *what*.

## 9. No unverified success claims
- **Banned:** "this works", "this should fix it", "now it's production-ready"
  with nothing run.
- **Why:** false confidence costs the user a debugging cycle.
- **Do instead:** run it and show output, or state precisely what is unverified.

## 10. No gaming the tests
- **Banned:** deleting/skipping/weakening tests, or asserting trivially, to make
  a change "pass".
- **Why:** hides the defect and erodes the safety net.
- **Do instead:** fix the code so real tests pass; only change a test when the
  spec genuinely changed, and say so.

## 11. No magic values
- **Banned:** unexplained literals (`* 86400`, `retries = 7`, `0.85`) in logic.
- **Do instead:** named constant with a one-line reason, or inline `why`.

## Self-check before sending
*Did I read the files? Did I run it? Did I invent anything? Is this the
smallest correct change that fits the codebase?* Fix or disclose.
