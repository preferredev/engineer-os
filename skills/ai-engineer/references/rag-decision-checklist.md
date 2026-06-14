# RAG decision checklist

RAG is retrieval plumbing, not a default. Most "we need RAG" situations are
solved by pasting the document or a single tool lookup. Use this gate before
building a pipeline.

## Do you even need retrieval?
Answer NO to RAG if:
- [ ] The relevant content fits comfortably in the context window.
- [ ] There is a single known source you can fetch directly (use a tool call).
- [ ] The knowledge is static and small (put it in the prompt).
- [ ] The task is reasoning/transformation, not knowledge lookup.

Consider RAG only if ALL hold:
- [ ] The corpus is too large or changes too often to keep in context.
- [ ] Answers genuinely depend on retrieving the right subset per query.
- [ ] You can measure retrieval quality (not just final-answer vibes).

## If you build RAG, design these explicitly
### Ingestion & chunking
- [ ] Chunking strategy fits the content (semantic/structural over fixed-size
      when possible); chunk size + overlap chosen deliberately.
- [ ] Metadata kept (source, title, section, timestamp) for filtering + cites.
- [ ] Re-indexing / freshness strategy defined for changing data.

### Retrieval
- [ ] Retrieval method chosen (vector / keyword / hybrid) and justified;
      hybrid is often the stronger default.
- [ ] top_k tuned via eval, not guessed.
- [ ] Metadata filtering / access control applied (no leaking restricted docs).
- [ ] Re-ranking considered if precision matters.

### Generation & grounding
- [ ] Prompt instructs: answer only from retrieved context; cite sources.
- [ ] Model must abstain when context lacks the answer (no hallucinated fill).
- [ ] Retrieved content treated as untrusted data (injection-safe).

## Evaluate retrieval separately from generation
- [ ] Retrieval metrics: recall@k / precision@k / hit rate on a labeled set.
- [ ] Generation metrics: groundedness (claims supported by retrieved chunks),
      answer correctness, citation accuracy.
- [ ] End-to-end eval on real queries incl. "answer not in corpus" cases.
- [ ] Latency + cost of retrieval + generation within budget.

## Common RAG failure modes to test
- Right answer exists but wasn't retrieved (recall failure).
- Retrieved but the model ignored it or contradicted it.
- Hallucinated a citation or cited the wrong chunk.
- Stale index served outdated answers.
- Injection via a poisoned document in the corpus.

## Decision output
State: whether retrieval is needed, why simpler options were rejected, the
retrieval method + top_k, and the retrieval + groundedness scores.
