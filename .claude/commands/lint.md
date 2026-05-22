Read SCHEMA.md first to understand all conventions.

Perform a full health check of the wiki. Do NOT auto-fix anything — report findings only, then ask the user which issues to address.

**Checks to run:**

1. **Broken links** — scan every page for [[link]] references where the target file does not exist in wiki/topics/
2. **Orphan pages** — find pages with no incoming links from any other wiki page
3. **Contradictions** — find cases where two pages make conflicting factual claims about the same topic
4. **Stale stubs** — find pages with Status: stub and Last updated older than 60 days
5. **Missing summaries** — find pages where the Summary section is empty or one sentence
6. **Index drift** — find pages in wiki/topics/ that are not listed in wiki/index.md (and vice versa)
7. **Uncited facts** — find Key Facts bullets that have no [source-slug] citation

**Report format:**

## Lint Report — <date>

### Broken Links (N)
- [[missing-page]] referenced in [page-that-references-it.md]

### Orphan Pages (N)
- [page.md] — no incoming links

### Contradictions (N)
- [page-a.md] claims X; [page-b.md] claims Y — recommend review

### Stale Stubs (N)
- [page.md] — Status: stub since YYYY-MM-DD

### Other Issues (N)
...

### Recommended Actions
Prioritized list of what to fix first.
