# ContextSpace Schema

This file governs how the LLM agent maintains this wiki. Read it before any ingest, query, or lint operation.

---

## Wiki Conventions

### Page Types

| Type | Location | Purpose |
|---|---|---|
| **Topic** | `wiki/topics/<slug>.md` | A concept, method, or technology |
| **Entity** | `wiki/topics/<slug>.md` | A person, organization, or project |
| **Synthesis** | `wiki/topics/<slug>.md` | A cross-cutting analysis or comparison |

### File Naming
- Lowercase, hyphen-separated: `transformer-architecture.md`, `andrej-karpathy.md`
- No dates in filenames (use `log.md` for timeline)
- Prefer specific names over generic: `attention-mechanism.md` not `important-concept.md`

### Page Structure

Every topic page must follow this template:

```markdown
# <Title>

**Type:** Topic | Entity | Synthesis
**Status:** stub | developing | mature
**Last updated:** YYYY-MM-DD
**Sources:** [source-slug-1], [source-slug-2]

## Summary
One paragraph. The most important thing to know about this topic.

## Key Facts / Claims
- Bullet list of discrete, citable facts
- Each fact should reference a source where possible

## Connections
- [[related-topic-1]] — one sentence on how they relate
- [[related-topic-2]] — one sentence on how they relate

## Open Questions
- Questions the current sources leave unanswered

## Raw Notes
Overflow space for unstructured observations not yet synthesized.
```

### Cross-Links
- Always use `[[page-slug]]` syntax for internal links (within this repo only)
- Link generously — the graph view is a feature, not clutter
- When creating a new page, search existing pages for mentions of the new topic and back-link them

### External References (Cross-Repo)
Each subject lives in its own repo. When a topic in this wiki is closely related to a topic
tracked in a different subject repo, add a human-readable pointer in **Raw Notes** — do NOT
use `[[link]]` syntax for cross-repo references (those files don't exist here):

```markdown
## Raw Notes
See also: [research-<other-subject> repo](<github-url>) — brief note on the relationship.
```

---

## Agent Operating Rules

### Ingest
1. Read ALL files in `raw/` that are not yet listed in `log.md`
2. For each source:
   a. Identify the main topics, entities, and claims
   b. For each: find existing wiki page or create a new one
   c. Add new facts under **Key Facts** with `[source-slug]` citations
   d. Add new cross-links under **Connections**
   e. Upgrade **Status** if the page has grown substantially
3. Update `wiki/index.md` with any new pages
4. Append to `wiki/log.md`: date, source(s) processed, pages created/updated

### Query
1. Read `wiki/index.md` to identify relevant pages
2. Load those pages in full
3. Synthesize an answer with inline citations `[page-slug]`
4. If the answer is novel and reusable, offer to save it as a new synthesis page

### Lint
1. Scan all pages for contradictions (fact A on page X conflicts with fact B on page Y)
2. Find orphan pages (no incoming links from other pages)
3. Find missing pages (topic mentioned via `[[link]]` but file doesn't exist)
4. Find stale stubs (Status: stub, Last updated > 60 days ago)
5. Report findings in a lint report; do NOT auto-fix without user approval

---

## Source Naming Convention

When logging sources, use a short slug derived from the filename or URL:
- `paper-attention-2017` (for "Attention Is All You Need")
- `karpathy-llm-wiki-2026`
- `my-notes-2026-05-20`

---

## What the LLM Must NOT Do
- Modify files in `raw/` — they are immutable source of truth
- Delete existing wiki pages (deprecate with a status note instead)
- Invent facts not present in sources — flag gaps as **Open Questions**
- Remove cross-links without verifying the link is truly irrelevant
