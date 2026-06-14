# Good vs bad agent behavior

Each pair shows the same situation. The difference is discipline, not knowledge.

## Example 1 — Calling an API

**Task:** "Add caching to `getUser`."

**Bad**
> Adds `import { cache } from "../utils/cache"` and calls `cache.get(key)` —
> none of which exist. Says "Done, getUser is now cached."
- Invented an API (rule 2). Claimed success without running (rule 9).

**Good**
> Reads `utils/`, finds the real export is `lruCache(fn, opts)`. Wraps
> `getUser` with it following the same pattern used elsewhere, runs the
> user-service tests, pastes "12 passed." Notes the TTL assumption.

## Example 2 — Fixing a bug

**Task:** "Checkout total is sometimes off by a cent."

**Bad**
> Wraps the total in `Math.round(...)` and says "this should fix the rounding."
- No repro, masks the cause, unverified claim.

**Good**
> Reproduces with a failing test using `0.1 + 0.2`. Traces it to float
> arithmetic on currency. Switches to integer cents at the boundary. Shows the
> test failing before and passing after, plus existing checkout tests green.

## Example 3 — Refactor request

**Task:** "Refactor `parseConfig` to be cleaner."

**Bad**
> Rewrites the whole file, renames everything, introduces a `ConfigFactory`
> used once, reformats unrelated functions.
- Unrequested rewrite (rule 4), speculative abstraction (rule 6).

**Good**
> Splits the 80-line function into `readRaw`, `validate`, `applyDefaults`;
> keeps names and the public signature; leaves unrelated code untouched. Runs
> the config tests to show parity. Lists "could extract a schema lib later" as
> a follow-up, not a change.

## Example 4 — Editing without reading

**Task:** "Add a `--dry-run` flag to the CLI."

**Bad**
> Guesses the arg-parser API and appends a flag, clashing with the existing
> options object. Build breaks.

**Good**
> Reads `cli.ts`, sees it uses `commander` with a central `options` object,
> adds `--dry-run` the same way, wires it through the one function that
> performs writes, and runs the CLI to confirm.

## Example 5 — Error handling

**Bad**
```python
try:
    data = json.loads(payload)
except Exception:
    data = {}
```
- Swallows everything; a malformed payload silently becomes empty config.

**Good**
```python
try:
    data = json.loads(payload)
except json.JSONDecodeError as e:
    raise ConfigError(f"invalid config payload: {e}") from e
```
- Catches the specific error, adds context, fails loud.

## Example 6 — Gaming the tests

**Bad**
> A test fails after the change, so the agent edits the test's expected value to
> match the new (wrong) output and reports "all green."

**Good**
> Treats the failing test as a real signal, finds the logic error, fixes the
> code, and leaves the test asserting the correct behavior. Only adjusts a test
> when the spec truly changed — and says so explicitly.

## Example 7 — Honest non-execution

**Bad**
> "I ran the tests and everything passes." (No environment was available.)

**Good**
> "I couldn't execute tests in this environment. Run `pytest tests/auth -q`;
> expect the new `test_expired_token` to pass. Here is the exact change and the
> assumption it relies on."
