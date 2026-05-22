# ContextSpace — LLM-Powered Research Memory System

Inspired by [Andrej Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), this repository is a **persistent, self-evolving knowledge base** where an LLM reads raw sources and compiles them into a structured, cross-referenced wiki you can query, browse, and grow over time.

---

## The Core Insight: Compile, Don't Retrieve

Traditional RAG re-reads raw documents on every query. This system flips the model:

```
Raw Sources  →  [LLM compiles]  →  Wiki  →  [LLM queries]  →  Answers
(immutable)                      (living)
```

Think of it like a compiler: your raw articles are **source code**, the wiki is the **compiled executable**. You compile once, keep it current, and query the compiled artifact — not the raw source — every time.

**Why this beats RAG for personal research:**
- No vector database, no embedding pipeline, no infrastructure
- Knowledge *compounds* — every ingest enriches existing pages
- Full context: the LLM sees the entire wiki, not just retrieved chunks
- Human-readable: browse it in Obsidian, edit by hand, or query with any LLM
- 70× more token-efficient than naive RAG on repeated queries

---

## Repository Structure

```
ContextSpace/
├── README.md              ← You are here
├── SCHEMA.md              ← Wiki conventions & agent instructions
│
├── raw/                   ← Drop source material here (never modified)
│   └── (articles, PDFs, papers, web clips, notes)
│
├── wiki/                  ← LLM-maintained knowledge base
│   ├── index.md           ← Master catalog of all wiki pages
│   ├── log.md             ← Append-only activity log
│   └── topics/            ← One .md file per concept / entity / topic
│
└── .claude/
    └── commands/          ← Claude Code slash commands
        ├── ingest.md      ← /ingest  — add new sources to the wiki
        ├── query.md       ← /query   — ask questions against the wiki
        └── lint.md        ← /lint    — health check & consistency pass
```

---

## The Three Operations

### 1. `/ingest` — Add new knowledge

Drop files into `raw/`, then run `/ingest`. The LLM will:
- Read each new source
- Extract key claims, entities, and concepts
- Update or create pages in `wiki/topics/`
- Maintain cross-links between related pages
- Update `wiki/index.md`
- Append an entry to `wiki/log.md`

### 2. `/query` — Ask questions

Run `/query` and type your question. The LLM will:
- Search the wiki for relevant pages
- Synthesize a cited answer
- Optionally create a new wiki page from the answer if it adds value

### 3. `/lint` — Keep the wiki healthy

Run `/lint` periodically. The LLM will:
- Flag contradictions between pages
- Identify orphaned pages with no cross-links
- Surface knowledge gaps (topics mentioned but not yet covered)
- Suggest merges for overlapping pages

---

## Quickstart

**Prerequisites:** Claude Code CLI (`claude` in your PATH)

```bash
# 1. Clone this repo
git clone https://github.com/rbenhgit/contextspace
cd contextspace

# 2. Drop your first sources into raw/
cp ~/Downloads/paper.pdf raw/
# or paste text into raw/my-notes.md

# 3. Run the first ingest
claude /ingest

# 4. Browse the wiki
# Open wiki/ in Obsidian, or just read the .md files

# 5. Ask a question
claude /query
```

---

## Use Cases

### Research Synthesis
Add papers from arXiv, blog posts, and lecture notes on a topic (e.g., "diffusion models"). After a few ingests you have a dense, cross-referenced wiki that rivals a literature review — without writing a word manually.

### Technology Radar
Feed in release announcements, changelogs, and benchmarks for frameworks you track. Query "what changed in PyTorch vs JAX since last quarter?" and get a cited answer from your compiled knowledge.

### Meeting & Decision Log
Drop meeting notes and design docs into `raw/`. The wiki builds up an institutional memory: entities (people, projects, systems), decisions made, and open questions.

### Learning Journal
As you work through a textbook or course, paste in chapter summaries and your own notes. The wiki connects concepts across chapters and lets you ask "how does concept X relate to concept Y?"

### Competitive Intelligence
Clip competitor blog posts, press releases, and forum discussions. The wiki maintains entity pages per company and surfaces pattern changes over time.

---

## Multi-Subject Strategy: One Repo per Research Area

**Short answer: yes — create a separate repo for each distinct research subject.**

The reason is LLM context coherence. When you run `/query`, the LLM loads your entire wiki
index and the most relevant pages. If unrelated subjects share the same wiki, they pollute
each other's answers and waste tokens. A focused wiki gives sharper, faster, cheaper results.

### The Rule of Thumb

| Situation | Action |
|---|---|
| Same broad subject (e.g., "attention" + "transformers") | Same repo |
| Different research questions (e.g., "LLM architectures" vs. "biotech") | Separate repo |
| Wiki grows past ~300 pages | Split into sub-repos |

### How to Spin Up a New Subject Repo

This repo is a **GitHub Template**. To create a new research wiki:

1. Go to `https://github.com/rbenhgit/contextspace`
2. Click **"Use this template"** → **"Create a new repository"**
3. Name it `research-<subject>` (e.g., `research-diffusion-models`)
4. The new repo has the full structure ready: SCHEMA.md, slash commands, empty `raw/`, seed wiki

### Linking Across Repos

Within a wiki, use `[[page-slug]]` for internal links. To reference a related subject in
another repo, add a human-readable pointer in the page's **Raw Notes**:

```markdown
## Raw Notes
See also: [research-diffusion-models repo](https://github.com/rbenhgit/research-diffusion-models)
for the generative modeling side of this topic.
```

---

## Scaling Up

| Scale | Approach |
|---|---|
| < 50 pages | Full wiki in a single LLM context window |
| 50–500 pages | Use `index.md` to locate relevant pages, load selectively |
| 500+ pages | Add semantic search (e.g., `ripgrep` + BM25) as a pre-filter |
| Expert-level | Fine-tune a model on the compiled wiki (Karpathy's next step) |

---

## Philosophy

> "A large fraction of my recent token throughput is going less into manipulating code, and more into manipulating knowledge." — Andrej Karpathy, April 2026

This system treats knowledge accumulation as a first-class engineering problem. The wiki is not a cache — it is the primary artifact. Raw sources are inputs. The LLM is the compiler.

---

## Credits

- Pattern by [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- Extended by community ([LLM Wiki v2](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2))
