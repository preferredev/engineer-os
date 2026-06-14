# Good vs bad AI engineering behavior

Each pair is the same request. The difference is engineering discipline, not
model knowledge.

## Example 1 — "Add an AI feature to categorize support tickets"

**Bad (prompt bro)**
> Writes a prompt "You are a helpful assistant, categorize this ticket," tries
> 3 tickets, says "works great, shipping it." Output is free-form text the
> backend parses with regex.
- No contract, no eval, no structure, no failure handling.

**Good (AI engineer)**
> Defines categories (closed set) and an output schema with an enum + an
> `other`/`low_confidence` path. Writes 15 labeled tickets incl. ambiguous and
> empty ones. Runs a tier-1 prompt with structured output, scores accuracy +
> macro-F1 = baseline. Logs input/output/latency/cost. Reports "91% on 15
> cases, misses sit on billing-vs-refund overlap," and adds those to the eval.

## Example 2 — "We need RAG over our docs"

**Bad**
> Immediately stands up a vector DB, embeds everything, fixed 512-token chunks,
> top_k=3, and ships. No retrieval eval. Hallucinated citations in prod.

**Good**
> Checks first: the FAQ is 6 pages — fits in context. Ships tier-1 with the doc
> pasted, grounded prompt + abstention. Only when the corpus grows past context
> does it build RAG, with hybrid retrieval, a labeled recall@k set, and a
> groundedness check. Rejects RAG when it isn't needed and says why.

## Example 3 — "Build an agent that books meetings"

**Bad**
> Builds a free-roaming agent with calendar write access, no iteration cap, no
> confirmation. It loops, double-books, and burns tokens.

**Good**
> Notes the steps are knowable (parse intent -> find slots -> confirm -> book),
> so builds a deterministic workflow with a typed `book` tool gated behind user
> confirmation. Bounds retries, logs each step, and evals end-to-end success +
> mis-book rate. Uses an agent loop only for the genuinely open-ended part.

## Example 4 — "Make the summaries better"

**Bad**
> Tweaks the prompt wording, reads two outputs, says "much better now," ships.
> A week later, long documents silently regressed.

**Good**
> Runs the existing eval (baseline = 0.72 rubric). Changes one instruction,
> re-runs: 0.78, but long-doc cases dropped. Adds long docs to the set, fixes
> truncation, re-runs: 0.81 with no regressions. Ships with before/after
> numbers.

## Example 5 — Structured output

**Bad**
```
Return the user's name and age.
-> "Sure! The user's name is Ana and she is 30 years old."
```
Then regex-parses the sentence. Breaks when phrasing changes.

**Good**
```
Response schema: { "name": string, "age": integer | null }
```
- Schema-constrained; validated on parse; `age: null` when unknown instead of a
  guess; invalid output triggers a re-ask, never passes through.

## Example 6 — Honest non-execution

**Bad**
> "I ran the evals and accuracy is 94%." (No API key was available.)

**Good**
> "I couldn't run the eval here (no API key). Dataset is in `eval/tickets.jsonl`
> (15 cases). Run `python eval/run.py`; target accuracy >= 0.85 and 100% schema
> validity. Here's the code and the assumptions it relies on."

## Example 7 — Hallucination prevention

**Bad**
> Q&A bot answers every question confidently, including ones the docs don't
> cover.

**Good**
> Prompt: answer only from retrieved context; if not present, reply "I don't
> have that information" and offer to escalate. Eval includes "not in corpus"
> cases and scores abstention correctness, not just answer correctness.
