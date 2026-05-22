# LLM Wiki Pattern

**Type:** Topic
**Status:** developing
**Last updated:** 2026-05-20
**Sources:** [karpathy-llm-wiki-2026]

## Summary

A knowledge management pattern where an LLM acts as a compiler: raw documents (articles, papers, notes) are "source code" fed in, and the LLM produces a structured, cross-referenced markdown wiki as the "compiled" artifact. Queries run against the compiled wiki, not the raw sources — making repeated queries far more token-efficient and richer in synthesized context.

## Key Facts / Claims

- Introduced by Andrej Karpathy in April 2026 via GitHub Gist
- The post received 16M views and 5,000 GitHub stars within days
- Reported to be ~70x more token-efficient than RAG for repeated queries
- Does not require vector databases, embeddings, or retrieval infrastructure
- Works with any long-context LLM (Karpathy uses Claude)
- Karpathy grew one research topic to ~100 articles / 400,000 words without writing a single word manually
- Three core operations: **ingest**, **query**, **lint**
- Planned extension: fine-tuning a model on the compiled wiki to bake knowledge into weights

## Connections

- [[rag-retrieval-augmented-generation]] — LLM Wiki is an alternative to RAG for personal/research knowledge bases
- [[obsidian]] — recommended tool for browsing and visualizing the compiled wiki
- [[claude-code]] — recommended agent runtime for running ingest/query/lint operations

## Open Questions

- How does performance degrade past ~500 wiki pages?
- What is the optimal page granularity (fine-grained entities vs. broad topic pages)?
- How to handle conflicting sources without introducing bias?
- What is the right cadence for lint runs?

## Raw Notes

Three-layer architecture: raw sources (immutable) → wiki (LLM-owned markdown) → schema (agent instructions).
Supporting files: `index.md` (catalog) and `log.md` (append-only activity trail).
Community has produced Obsidian plugins and a v2 extension (agentmemory architecture).
