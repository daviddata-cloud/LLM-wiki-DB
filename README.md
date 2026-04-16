# LLM-wiki-DB

```
LLM Wiki
A pattern for building personal knowledge bases using LLMs.
The LLM Wiki Knowledge Database implements a knowledge compilation architecture that complements LLM reasoning by introducing a persistent, structured knowledge layer.

Conceptually:

LLM (First Brain) → probabilistic reasoning engine
LLM Wiki DB (Second Brain) → deterministic, persistent knowledge store
1. System Architecture

The system shifts from query-time retrieval (RAG) to compile-time knowledge structuring:

Pipeline:

Ingestion
Source data: markdown, documents, code, web content
Preprocessing and normalization
Knowledge Compilation
Entity extraction (people, concepts, artifacts)
Relationship mapping (links, backlinks, references)
Structured output (e.g., index.md, topic pages)
Storage Layer
File-based (markdown/wiki) + database-backed indexing
Supports versioning, updates, and incremental rebuilds
Query Layer
Natural language interface (LLM-driven)
Structured retrieval (graph traversal, indexed lookup)
Hybrid reasoning (LLM + deterministic context)
2. Agent-Oriented Design

The system leverages multi-agent orchestration:

IngestAgent
Parses and normalizes raw input
Triggers knowledge compilation
QueryAgent
Translates user queries into structured retrieval steps
Combines graph traversal with LLM reasoning
LintAgent
Ensures consistency (links, schema, formatting)
Detects contradictions and gaps
Orchestrator
Manages parallel execution
Applies cost controls, retries, and backoff strategies
3. Knowledge Model

Unlike vector-only systems, this approach builds a structured knowledge graph layer:

Nodes: entities, topics, documents
Edges: relationships (references, dependencies, semantic links)
Artifacts: markdown pages, indexes, logs

Key features:

Bidirectional linking (backlinks)
Centralized indexing (index.md)
Incremental updates instead of full recomputation
4. RAG vs. Knowledge Compilation
Aspect	Traditional RAG	LLM Wiki DB
Retrieval	Similarity search (vector)	Structured + indexed lookup
Timing	Query-time (lazy)	Pre-compiled (eager)
Context	Fragmented chunks	Coherent, linked knowledge
Reasoning	Limited cross-doc	Graph-aware traversal
Consistency	Variable	Higher (persistent structure)

Limitations of RAG:

Weak multi-hop reasoning
No persistent memory layer
Recomputes context per query

Advantages of LLM Wiki:

Deterministic structure + probabilistic reasoning
Better contradiction detection
Reusable and evolvable knowledge base
5. Local-First + Deployment Model
Local-first architecture
Data remains on localhost
No external storage dependency
Execution environment
Docker-based MCP server
CLI + REST interfaces
LLM abstraction layer
Pluggable providers (e.g., Gemini, Groq)
Vendor-agnostic design
6. Human-in-the-Loop Design

The system is not fully autonomous—it is human-guided:

Humans:
Curate sources
Define questions and priorities
Validate outputs
Agents:
Perform transformation and structuring
Maintain consistency and linkage
Automate repetitive knowledge tasks
7. Key Insight

This architecture redefines the role of LLM systems:

From stateless retrieval assistants (RAG)
→ to stateful knowledge systems with persistent memory

The result is a self-evolving knowledge base that improves over time and supports:

Complex reasoning workflows
Research and analysis pipelines
Personalized knowledge environments

This is an idea file, it is designed to be copy pasted to your own LLM Agent (e.g. OpenAI Codex, Claude Code, OpenCode / Pi, or etc.). Its goal is to communicate the high level idea, but your agent will build out the specifics in collaboration with you.

The core idea
Most people's experience with LLMs and documents looks like RAG: you upload a collection of files, the LLM retrieves relevant chunks at query time, and generates an answer. This works, but the LLM is rediscovering knowledge from scratch on every question. There's no accumulation. Ask a subtle question that requires synthesizing five documents, and the LLM has to find and piece together the relevant fragments every time. Nothing is built up. NotebookLM, ChatGPT file uploads, and most RAG systems work this way.

The idea here is different. Instead of just retrieving from raw documents at query time, the LLM incrementally builds and maintains a persistent wiki — a structured, interlinked collection of markdown files that sits between you and the raw sources. When you add a new source, the LLM doesn't just index it for later retrieval. It reads it, extracts the key information, and integrates it into the existing wiki — updating entity pages, revising topic summaries, noting where new data contradicts old claims, strengthening or challenging the evolving synthesis. The knowledge is compiled once and then kept current, not re-derived on every query.

This is the key difference: the wiki is a persistent, compounding artifact. The cross-references are already there. The contradictions have already been flagged. The synthesis already reflects everything you've read. The wiki keeps getting richer with every source you add and every question you ask.

You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it. You're in charge of sourcing, exploration, and asking the right questions. The LLM does all the grunt work — the summarizing, cross-referencing, filing, and bookkeeping that makes a knowledge base actually useful over time. In practice, I have the LLM agent open on one side and Obsidian open on the other. The LLM makes edits based on our conversation, and I browse the results in real time — following links, checking the graph view, reading the updated pages. Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

This can apply to a lot of different contexts. A few examples:

Personal: tracking your own goals, health, psychology, self-improvement — filing journal entries, articles, podcast notes, and building up a structured picture of yourself over time.
Research: going deep on a topic over weeks or months — reading papers, articles, reports, and incrementally building a comprehensive wiki with an evolving thesis.
Reading a book: filing each chapter as you go, building out pages for characters, themes, plot threads, and how they connect. By the end you have a rich companion wiki. Think of fan wikis like Tolkien Gateway — thousands of interlinked pages covering characters, places, events, languages, built by a community of volunteers over years. You could build something like that personally as you read, with the LLM doing all the cross-referencing and maintenance.
Business/team: an internal wiki maintained by LLMs, fed by Slack threads, meeting transcripts, project documents, customer calls. Possibly with humans in the loop reviewing updates. The wiki stays current because the LLM does the maintenance that no one on the team wants to do.
Competitive analysis, due diligence, trip planning, course notes, hobby deep-dives — anything where you're accumulating knowledge over time and want it organized rather than scattered.
Architecture
There are three layers:

Raw sources — your curated collection of source documents. Articles, papers, images, data files. These are immutable — the LLM reads from them but never modifies them. This is your source of truth.

The wiki — a directory of LLM-generated markdown files. Summaries, entity pages, concept pages, comparisons, an overview, a synthesis. The LLM owns this layer entirely. It creates pages, updates them when new sources arrive, maintains cross-references, and keeps everything consistent. You read it; the LLM writes it.

The schema — a document (e.g. CLAUDE.md for Claude Code or AGENTS.md for Codex) that tells the LLM how the wiki is structured, what the conventions are, and what workflows to follow when ingesting sources, answering questions, or maintaining the wiki. This is the key configuration file — it's what makes the LLM a disciplined wiki maintainer rather than a generic chatbot. You and the LLM co-evolve this over time as you figure out what works for your domain.

Operations
Ingest. You drop a new source into the raw collection and tell the LLM to process it. An example flow: the LLM reads the source, discusses key takeaways with you, writes a summary page in the wiki, updates the index, updates relevant entity and concept pages across the wiki, and appends an entry to the log. A single source might touch 10-15 wiki pages. Personally I prefer to ingest sources one at a time and stay involved — I read the summaries, check the updates, and guide the LLM on what to emphasize. But you could also batch-ingest many sources at once with less supervision. It's up to you to develop the workflow that fits your style and document it in the schema for future sessions.

Query. You ask questions against the wiki. The LLM searches for relevant pages, reads them, and synthesizes an answer with citations. Answers can take different forms depending on the question — a markdown page, a comparison table, a slide deck (Marp), a chart (matplotlib), a canvas. The important insight: good answers can be filed back into the wiki as new pages. A comparison you asked for, an analysis, a connection you discovered — these are valuable and shouldn't disappear into chat history. This way your explorations compound in the knowledge base just like ingested sources do.

Lint. Periodically, ask the LLM to health-check the wiki. Look for: contradictions between pages, stale claims that newer sources have superseded, orphan pages with no inbound links, important concepts mentioned but lacking their own page, missing cross-references, data gaps that could be filled with a web search. The LLM is good at suggesting new questions to investigate and new sources to look for. This keeps the wiki healthy as it grows.

Indexing and logging
Two special files help the LLM (and you) navigate the wiki as it grows. They serve different purposes:

index.md is content-oriented. It's a catalog of everything in the wiki — each page listed with a link, a one-line summary, and optionally metadata like date or source count. Organized by category (entities, concepts, sources, etc.). The LLM updates it on every ingest. When answering a query, the LLM reads the index first to find relevant pages, then drills into them. This works surprisingly well at moderate scale (~100 sources, ~hundreds of pages) and avoids the need for embedding-based RAG infrastructure.

log.md is chronological. It's an append-only record of what happened and when — ingests, queries, lint passes. A useful tip: if each entry starts with a consistent prefix (e.g. ## [2026-04-02] ingest | Article Title), the log becomes parseable with simple unix tools — grep "^## \[" log.md | tail -5 gives you the last 5 entries. The log gives you a timeline of the wiki's evolution and helps the LLM understand what's been done recently.

Optional: CLI tools
At some point you may want to build small tools that help the LLM operate on the wiki more efficiently. A search engine over the wiki pages is the most obvious one — at small scale the index file is enough, but as the wiki grows you want proper search. qmd is a good option: it's a local search engine for markdown files with hybrid BM25/vector search and LLM re-ranking, all on-device. It has both a CLI (so the LLM can shell out to it) and an MCP server (so the LLM can use it as a native tool). You could also build something simpler yourself — the LLM can help you vibe-code a naive search script as the need arises.

Tips and tricks
Obsidian Web Clipper is a browser extension that converts web articles to markdown. Very useful for quickly getting sources into your raw collection.
Download images locally. In Obsidian Settings → Files and links, set "Attachment folder path" to a fixed directory (e.g. raw/assets/). Then in Settings → Hotkeys, search for "Download" to find "Download attachments for current file" and bind it to a hotkey (e.g. Ctrl+Shift+D). After clipping an article, hit the hotkey and all images get downloaded to local disk. This is optional but useful — it lets the LLM view and reference images directly instead of relying on URLs that may break. Note that LLMs can't natively read markdown with inline images in one pass — the workaround is to have the LLM read the text first, then view some or all of the referenced images separately to gain additional context. It's a bit clunky but works well enough.
Obsidian's graph view is the best way to see the shape of your wiki — what's connected to what, which pages are hubs, which are orphans.
Marp is a markdown-based slide deck format. Obsidian has a plugin for it. Useful for generating presentations directly from wiki content.
Dataview is an Obsidian plugin that runs queries over page frontmatter. If your LLM adds YAML frontmatter to wiki pages (tags, dates, source counts), Dataview can generate dynamic tables and lists.
The wiki is just a git repo of markdown files. You get version history, branching, and collaboration for free.
Why this works
The tedious part of maintaining a knowledge base is not the reading or the thinking — it's the bookkeeping. Updating cross-references, keeping summaries current, noting when new data contradicts old claims, maintaining consistency across dozens of pages. Humans abandon wikis because the maintenance burden grows faster than the value. LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass. The wiki stays maintained because the cost of maintenance is near zero.

The human's job is to curate sources, direct the analysis, ask good questions, and think about what it all means. The LLM's job is everything else.

The idea is related in spirit to Vannevar Bush's Memex (1945) — a personal, curated knowledge store with associative trails between documents. Bush's vision was closer to this than to what the web became: private, actively curated, with the connections between documents as valuable as the documents themselves. The part he couldn't solve was who does the maintenance. The LLM handles that.


**Engineered for solo users and enterprises alike, providing a domain-specific knowledge base that scales seamlessly while maintaining accuracy through autonomous self-optimization.**

> Built for individuals, small teams, and large organizations who need a knowledge base that stays accurate as documents accumulate.

LLM-wiki-DB reads your raw source documents — PDFs, spreadsheets, web pages, images, Word files — and uses an LLM to synthesize them into a persistent, structured wiki. Cross-references are built automatically, contradictions are detected and surfaced, orphan pages are flagged, and every answer cites its sources. Outputs are stored as local Markdown files, ensuring seamless integration and autonomous management within [Obsidian](https://obsidian.md) or any wiki-compliant ecosystem.

---

## Who Is It For?

LLM-wiki-DB scales from a single researcher to a company-wide knowledge platform:

| Team size | Typical use case |
|-----------|-----------------|
| **Solo / 1–2 people** | Personal research wiki, freelance knowledge base, indie hacker documentation — run it free on Gemini Flash or a local Ollama model with zero ongoing cost |
| **Small team (3–20)** | Centralized internal knowledge base for startups and departments that aggregates diverse individual data sources into a unified, high-integrity wiki. The system automatically resolves contradictions and scales autonomously, ensuring organizational intelligence grows in tandem with your team |
| **Medium / enterprise** | Compliance-sensitive knowledge bases that must stay local; per-department wikis on separate ports; audit trail for every ingest and cost event; hook system for CI/CD integration; OpenTelemetry for ops dashboards |

No cloud account. No vendor lock-in. The wiki is plain Markdown — open it in any editor, back it up with git, sync it with any cloud drive.

---

## Inspiration and Vision

> *"The LLM should be able to maintain a wiki for you."*
> — Andrej Karpathy, [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

Most knowledge-management tools retrieve and summarize at query time. LLM-wiki-DB inverts this: it **compiles knowledge at ingest time**. Every new source enriches and cross-links the entire corpus, not just appends a new chunk. The wiki is the artifact — readable, editable, and browsable without any tool running.

**Long-term alignment:**

| Direction | How LLM-wiki-DB moves there |
|-----------|--------------------------|
| Agent orchestration | Orchestrator dispatches parallel IngestAgent, QueryAgent, LintAgent sub-agents with cost guards and retry backoff |
| Sub-agent skills/plugins | Featuring a 3-tier lazy-load capability system, the platform allows for the injection of custom skills and hooks via a plug-and-play interface, ensuring core stability is never compromised during extension |
| LLM wiki vs. RAG | Pre-compiled structured knowledge beats query-time synthesis for contradiction detection, graph traversal, and offline access |
| CLI / HTTP | A unified interface via CLI and RESTful endpoints, the system streamlines full-spectrum integration: from data ingestion and querying to automated linting, security auditing, and job orchestration |
| Local-first | All data stays on your machine; localhost-only network binding; no cloud dependency except the LLM API itself |
| Provider choice | Five LLM backends including free-tier Gemini and Groq — no single-vendor dependency |

---

## Problems Addressed

### 1. RAG conflates contradictions; LLM-wiki-DB surfaces them

When two sources disagree, vector search returns both and the LLM silently blends them. LLM-wiki-DB detects the conflict during ingest, flags the page with `status: contradicted`, preserves both claims with citations, and either auto-resolves (if confidence ≥ threshold) or queues the conflict for human review.

### 2. Knowledge fragments; LLM-wiki-DB links it

RAG chunks are isolated. LLM-wiki-DB builds `[[wikilinks]]` between related pages during every ingest pass. The resulting graph is visible in Obsidian's Graph view and queryable with Dataview.

### 3. Orphan knowledge has no address; LLM-wiki-DB finds it

Pages that exist but are referenced by nothing are surfaced by the lint system, with ready-to-paste index entries so you can quickly integrate them.

### 4. Re-synthesis is expensive; LLM-wiki-DB caches it

A 3-layer cache (embedding, LLM response, provider prompt cache) means repeated lint runs on unchanged pages cost near-zero tokens.

### 5. Knowledge is locked in tools; LLM-wiki-DB escapes it

Every page is a plain Markdown file with YAML frontmatter. No proprietary format. Open the folder in any editor, put it in git, sync it with any cloud drive.

### Business values

| Value | How |
|-------|-----|
| **Faster onboarding** | New team members query the wiki instead of digging through documents |
| **Audit trail** | Every ingest recorded in `audit.db` with source hash, token count, and timestamp |
| **Cost control** | Configurable soft-warn and hard-gate thresholds; 3-layer cache reduces repeat spend |
| **Compliance** | Local-first — source documents and compiled knowledge never leave your machine |
| **Extensibility** | Hooks fire on every event; custom skills load without a server restart |

---

## Why LLM-wiki-DB?

### Competitive advantages

| Capability | LLM-wiki-DB | Typical RAG | NotebookLM | Notion AI |
|------------|-----------|-------------|------------|-----------|
| Ingest-time synthesis | **Yes** | No | Partial | No |
| Contradiction detection | **Yes** | No | No | No |
| Orphan page detection | **Yes** | No | No | No |
| Persistent wikilink graph | **Yes** | No | No | No |
| Local-first (no cloud data) | **Yes** | Varies | No | No |
| Custom skill plugins | **Yes** | Limited | No | No |
| Obsidian integration | **Yes** | No | No | No |
| Cost guard + audit trail | **Yes** | No | No | No |
| Hook / CI integration | **Yes** (2 events) | No | No | No |
| Offline browsable artifact | **Yes** | No | No | No |
| Multi-wiki isolation | **Yes** | No | No | No |
| Web search → wiki pages | **Yes** | No | No | No |
| Free LLM tier support | **Yes** (Gemini, Groq) | No | No | No |
| Auto wiki overview page | **Yes** | No | No | No |
| Resumable job queue + retry | **Yes** | No | No | No |

### Key differentiators vs. RAG

RAG chunks documents and retrieves them at query time. LLM-wiki-DB **compiles** knowledge: every new source is synthesized into the existing wiki graph at ingest time.

- **Contradictions are caught, not blended.** When two sources disagree, LLM-wiki-DB flags the page — RAG silently averages both claims.
- **Knowledge is linked, not scattered.** `[[wikilinks]]` connect related pages into a navigable graph visible in Obsidian and queryable with Dataview.
- **The artifact outlives the tool.** Close the server, open the wiki folder in any Markdown editor — the knowledge is all there, human-readable, no proprietary format.
- **Cost-efficient at scale.** Two-step ingest with cached analysis means repeated ingest of similar sources costs near-zero tokens. Three cache layers stack for lint and query too.
- **Ingest is durable, not fragile.** Every ingest request becomes a queued job with automatic retry and a persistent audit record. Batch a hundred documents and resume after a crash — no work is lost.

---

## Architecture

![LLM-wiki-DB Architecture](docs/architecture.png)

For full architecture details, data models, API reference, and plugin development guide see **[docs/design.md](docs/design.md)**.

---

## What's Included in v0.1

- **3 agents** — IngestAgent (two-step cached synthesis), QueryAgent (BM25 + LLM), LintAgent (contradiction + orphan detection + auto-resolution)
- **8 built-in skills** — PDF, URL, Markdown/TXT, DOCX, PPTX, XLSX/CSV, Image (vision), **Web search (Tavily — fully live)**
- **Folder-based skill system** — each skill is a self-contained folder with a `SKILL.md` manifest; intent-based dispatch alongside extension matching; drop a folder in `skills/` to add a new format without touching core code
- **2 access surfaces** — CLI (thin HTTP client), HTTP REST API
- **Obsidian plugin** — ingest (with file picker when no note is active), query (responsive modal, stays open), lint report, jobs list — all from the command palette; ribbon shows engine health + page count
- **5 LLM providers** — Anthropic, OpenAI, **Gemini** (free tier), **Groq** (free tier), Ollama (local); switch with one config line
- **Two-step ingest** — `_analyse()` caches entity extraction + summary; decision prompt uses summary instead of full text; reduces cost on large documents
- **purpose.md scope filtering** — define what belongs in your wiki; the LLM skips out-of-scope sources cleanly
- **overview.md auto-summary** — 2-paragraph wiki overview regenerated automatically after every ingest that creates or updates pages
- **Audit CLI** — `LLM-wiki-DB audit history / cost / events` query `audit.db` without needing direct access; `--analyse-only` flag previews ingest analysis before writing pages
- **3-layer cache** — embedding cache, LLM response cache, provider prompt cache
- **Cost guards** — configurable soft-warn and hard-gate USD thresholds
- **Hook system** — shell commands on `on_ingest_complete` and `on_lint_complete` lifecycle events; blocking or background; context passed as JSON on stdin; community hook library in [`hooks/`](hooks/)
- **Job queue** — SQLite-backed, persistent, retry with exponential backoff; non-retryable errors (`failed`) distinguished from exhausted-retry errors (`dead`)
- **Startup banner** — ASCII logo with version, port, wiki, and PID on `LLM-wiki-DB serve`; plain-text version served at `GET /`; `--background` flag detaches the server and returns the shell immediately (logs → file only)
- **Multi-wiki** — unlimited isolated wikis, each on its own port
- **OpenTelemetry** — traces, metrics, structured logs; OTLP export optional
- **Cross-platform** — Windows, Linux, macOS

---

## Installation

### Prerequisites

| Requirement | Version | Notes |
|-------------|---------|-------|
| Python | 3.11+ | |
| Node.js | 18+ | Obsidian plugin build only |
| Git | any | |
| LLM API key | — | At least one required (see below) |
| Tavily API key | — | Optional — web search feature only |

**LLM API key — at least one required:**

| Provider | Free tier | Get key |
|----------|-----------|---------|
| **Gemini Flash** | Yes — 15 RPM / 1M tokens/day, no credit card | [aistudio.google.com](https://aistudio.google.com/app/apikey) |
| Groq | Yes — rate-limited | [console.groq.com](https://console.groq.com/keys) |
| Ollama | Yes — runs locally, no key | [ollama.com](https://ollama.com) |
| Anthropic | No | [console.anthropic.com](https://console.anthropic.com/) |
| OpenAI | No | [platform.openai.com](https://platform.openai.com/api-keys) |

**Tavily API key (optional — enables web search):**
Get a free key at [tavily.com](https://tavily.com). Without it, web search jobs will fail but all other features work normally.

---

### Step 1 — Clone and install

```bash
git clone https://github.com/paulmchen/LLM-wiki-DB.git
cd LLM-wiki-DB
pip3 install -e ".[dev]"
```

### Step 2 — Run the Python test suite

Validate that the Python engine builds and all tests pass before proceeding:

```bash
pytest --ignore=tests/performance/ -q
```

Expected: all tests pass, 0 failures. If any fail, check the error output before continuing.

Performance benchmarks (optional — Linux/macOS, measures SLOs):

```bash
pytest tests/performance/ -v --benchmark-disable
```

### Step 3 — Build and test the Obsidian plugin

```bash
cd obsidian-plugin
npm install
npm run build    # produces main.js
npm test         # runs Vitest unit tests
cd ..
```

### Step 4 — Set your API keys

The demo wiki defaults to using **Anthropic** (`ANTHROPIC_API_KEY`) as the LLM provider. Web search uses **Tavily** (`TAVILY_API_KEY`). You can switch to a free-tier provider (Gemini Flash or Groq) by editing `<wiki-root>/.LLM-wiki-DB/config.toml` after installation.

```bash
# macOS / Linux — add to ~/.bashrc or ~/.zshrc to persist
export ANTHROPIC_API_KEY=sk-ant-…    # default LLM provider for the demo
export GEMINI_API_KEY=AIza…          # free-tier alternative — 15 RPM / 1M tokens/day
export TAVILY_API_KEY=tvly-…         # web search (LLM-wiki-DB ingest "search for: …")

# Windows cmd — current session
set ANTHROPIC_API_KEY=sk-ant-…
set TAVILY_API_KEY=tvly-…

# Windows cmd — permanent (open a new cmd window after running)
setx ANTHROPIC_API_KEY sk-ant-…
setx TAVILY_API_KEY tvly-…
```

### Step 5 — Verify

```bash
LLM-wiki-DB --version
```

### Step 6 — Install a demo wiki, then start the engine

A **wiki** is a self-contained, structured knowledge base — a folder of Markdown pages linked by topic, maintained and cross-referenced automatically by LLM-wiki-DB. Think of it as a living document that grows smarter with every source you feed it: each ingest pass adds new pages, updates existing ones, and flags contradictions. For your own work, you can build and grow a domain-specific wiki — whether that's market research, a technical knowledge base, or a team handbook — and query it in plain English at any time.

A wiki must be installed before the engine can serve it. The fastest way to get started is the **History of Computing** demo, which ships with 10 pre-built pages and sample source files — no LLM API key required to browse it.

**Install the demo wiki:**

```bash
# Linux / macOS
LLM-wiki-DB install history-of-computing --target ~/wikis --demo

# Windows (cmd.exe)
LLM-wiki-DB install history-of-computing --target %USERPROFILE%\wikis --demo
```

**Then start the engine:**

```bash
# Foreground — keeps the terminal; logs stream to the console
LLM-wiki-DB serve -w history-of-computing

# Background — releases the terminal; logs go to the wiki log file
LLM-wiki-DB serve -w history-of-computing --background
```

The server binds to `http://127.0.0.1:7070` by default (port is set in `<wiki-root>/.LLM-wiki-DB/config.toml`). Leave it running while you work — the Obsidian plugin, CLI ingest commands, and query commands all talk to it.

To stop a background server:

```bash
# Linux / macOS
kill <PID>

# Windows (cmd)
taskkill /PID <PID> /F
```

The PID is printed when the background server starts and saved to `<wiki-root>/.LLM-wiki-DB/server.pid`.

---

## Quick-Start Guide

The **History of Computing** demo includes 10 pre-built pages, raw source files covering clean-merge, contradiction, and orphan scenarios, and a full walkthrough of every LLM-wiki-DB feature.

**Full step-by-step walkthrough: [docs/demo-guide.md](docs/demo-guide.md)**

The guide covers:
1. Installing the demo vault and opening it in Obsidian
2. Installing the Dataview and LLM-wiki-DB plugins
3. Starting the engine and querying pre-built content
4. Running batch ingest across all demo sources
5. Resolving a contradiction (manual and LLM auto-resolve)
6. Fixing an orphan page
7. Web search ingestion, audit commands, hooks, and scheduling

---

## Creating Your Own Wiki

Once you've walked through the demo, creating a wiki for your own domain takes two commands:

```bash
# "market-condition-canada" is the wiki name (used in all -w commands)
# "Market conditions and trends in Canada" is the subject domain the wiki will manage
LLM-wiki-DB install market-condition-canada --target ~/wikis --domain "Market conditions and trends in Canada"
LLM-wiki-DB serve -w market-condition-canada
```

`--target` is the parent folder where the wiki directory will be created. `--domain` is a free-text description of the subject area — the LLM uses it to tailor the scaffold content to your domain.

**Then open the wiki in Obsidian as a new vault** and install both plugins — each wiki is an independent vault, so this is required once per wiki:

1. Open Obsidian → **Open folder as vault** → select the wiki folder (e.g. `~/wikis/market-condition-canada`)
2. **Settings → Community plugins → Turn on community plugins → Browse** → install and enable **Dataview**
3. Install and enable **LLM-wiki-DB** (or copy the plugin from an existing vault's `.obsidian/plugins/` folder)

`install` creates the folder structure and, if an API key is set, runs a one-time LLM scaffold that generates four domain-aware starter files:

| File | Purpose |
|---|---|
| `wiki/index.md` | Table of contents — organises pages into domain-relevant categories with `[[wikilinks]]` |
| `wiki/purpose.md` | Scope declaration — tells the ingest agent what belongs in this wiki and what to ignore |
| `AGENTS.md` | LLM behaviour guidelines — domain-specific instructions for tone, terminology, and synthesis style |
| `wiki/dashboard.md` | Live Dataview dashboard — orphan pages, contradictions, and page count (requires Obsidian + Dataview plugin) |

These files are the wiki's "self-knowledge" — LLM-wiki-DB reads them on every ingest to decide how to classify, merge, and label new content for that domain.

**Scaffold can be re-run at any time** as your domain evolves. Pages already linked in `index.md` are protected and never overwritten:

```bash
LLM-wiki-DB scaffold -w market-condition-canada
```

**Recommended first steps after the plugins are configured and the scaffold files look right:**

**1. Seed the wiki with web searches** — pull in real content for the topics you care about:

```bash
LLM-wiki-DB ingest "search for: Economy, employment and labour market analysis and performance in Toronto GTA" -w market-condition-canada
LLM-wiki-DB ingest "search for: Bank of Canada interest rate outlook 2025" -w market-condition-canada
LLM-wiki-DB ingest "search for: Ontario housing affordability and rental market trends" -w market-condition-canada
```

Each search fans out into up to 20 URL ingest jobs. Watch them process:

```bash
LLM-wiki-DB jobs list -w market-condition-canada
```

**2. Run lint and query** — once jobs complete, check what was built and whether anything conflicts:

```bash
LLM-wiki-DB lint run -w market-condition-canada
LLM-wiki-DB lint report -w market-condition-canada
LLM-wiki-DB query "What are the current employment trends in the Toronto GTA?" -w market-condition-canada
```

**3. Re-run scaffold** — now that the wiki has real pages, scaffold can generate a much richer index with categories that reflect actual content:

```bash
LLM-wiki-DB scaffold -w market-condition-canada
```

**4. Set up a daily scheduler** — keep the wiki fresh automatically:

```bash
# Re-ingest key topics nightly at 2 AM
LLM-wiki-DB schedule add --op "ingest" --source "search for: Toronto GTA economic indicators latest" --cron "0 2 * * *" -w market-condition-canada

# Re-run scaffold weekly on Sunday at 4 AM to keep the index current
LLM-wiki-DB schedule add --op "scaffold" --cron "0 4 * * 0" -w market-condition-canada
```

See [docs/design.md](docs/design.md) for a full description of how ingest, contradiction detection, and orphan tracking work under the hood.

---

## Configuration

> **You do not need to configure anything to run the demo.** The demo wiki
> ships with its own settings and sensible built-in defaults cover everything
> else. Set your API key env var, run `LLM-wiki-DB serve`, and go.
>
> Read this section when you are ready to run a real wiki or change a default.

### How configuration works

Settings are resolved in three layers — later layers win:

```
1. Built-in defaults          (always applied)
2. ~/.LLM-wiki-DB/config.toml   (global — your preferences across all wikis)
3. <wiki-root>/.LLM-wiki-DB/config.toml   (per-project — overrides for one wiki)
```

Neither file is required. If both are absent, the built-in defaults take effect.

### Global config — `~/.LLM-wiki-DB/config.toml`

**Use this to set preferences that apply to every wiki on your machine** —
primarily your default LLM provider and the wiki registry.

```toml
[agents]
default = { provider = "gemini", model = "gemini-2.0-flash" }  # free tier
lint    = { provider = "groq",   model = "llama-3.3-70b-versatile" }  # cheaper for lint

[wikis]
research = "~/wikis/research"
work     = "~/wikis/work"
```

Common reason to edit: switching from the Anthropic default to Gemini Flash
(free tier) so all wikis use it without touching each project config.

### Per-project config — `<wiki-root>/.LLM-wiki-DB/config.toml`

**Use this when one wiki needs different settings from the global default** —
a different port, tighter cost limits, wiki-specific hooks, or web search.

```toml
[server]
port = 7071          # required if running more than one wiki simultaneously

[cost]
soft_warn_usd = 0.50
hard_gate_usd = 2.00

[web_search]
provider    = "tavily"
max_results = 20

[hooks]
on_ingest_complete = "python git-auto-commit.py"
```

Common reason to edit: each wiki needs its own port when running multiple
wikis at the same time.

Full config reference: [docs/design.md — Configuration](docs/design.md#configuration).

---

## Command Reference by Use Case

### Setting up a wiki

```bash
# Create a new empty wiki (LLM scaffold runs automatically if API key is set)
LLM-wiki-DB install my-wiki --target ~/wikis --domain "Machine Learning"

# Create a wiki on a specific port (useful when running multiple wikis)
LLM-wiki-DB install my-wiki --target ~/wikis --domain "Machine Learning" --port 7071

# Install the demo (includes pre-built pages and raw sources — no LLM call needed)
LLM-wiki-DB install history-of-computing --target ~/wikis --demo

# List available demo templates
LLM-wiki-DB demo list
```

### Refreshing wiki scaffold

After install, you can re-run the LLM scaffold at any time to regenerate domain-specific content (index categories, AGENTS.md guidelines, purpose.md scope). Pages already linked in `index.md` are protected and preserved.

```bash
# Regenerate scaffold for an existing wiki
LLM-wiki-DB scaffold -w my-wiki

# Schedule weekly refresh (runs every Sunday at 4 AM)
LLM-wiki-DB schedule add --op "scaffold" --cron "0 4 * * 0" -w my-wiki
```

`config.toml` and `dashboard.md` are never modified by `scaffold`.

### Running the server

```bash
# Start HTTP API + job worker (foreground — terminal stays attached)
LLM-wiki-DB serve -w my-wiki

# Detach to background — banner shown, then shell is released
# All logs go to <wiki>/.LLM-wiki-DB/logs/LLM-wiki-DB.log
LLM-wiki-DB serve -w my-wiki --background

# Custom port
LLM-wiki-DB serve -w my-wiki --port 7071

# Verbose debug logging to console
LLM-wiki-DB serve -w my-wiki --verbose
```

### Ingesting sources

```bash
# Single file or URL
LLM-wiki-DB ingest report.pdf -w my-wiki
LLM-wiki-DB ingest https://example.com/article -w my-wiki

# Entire folder (parallel, up to max_parallel_ingest at a time)
LLM-wiki-DB ingest --batch raw_sources/ -w my-wiki

# Manifest file — ingest a curated list of sources in one shot.
# sources.txt: one entry per line; each line is either an absolute file path
# (PDF, DOCX, PPTX, MD, …) or a URL. Blank lines and # comments are ignored.
# Each entry becomes a separate job in the queue, processed sequentially.
#
# Example sources.txt:
#   /home/user/docs/research-paper.pdf
#   /home/user/slides/keynote.pptx
#   https://en.wikipedia.org/wiki/Alan_Turing
#   # this line is ignored
LLM-wiki-DB ingest --file sources.txt -w my-wiki

# Force re-ingest (bypass deduplication and cache)
LLM-wiki-DB ingest --force report.pdf -w my-wiki

# Web search — triggers a Tavily search, then ingests each result URL as a child job.
# Prefix the query with any recognised intent: "search for:", "find on the web:",
# "look up:", or "web search:"  (prefix is stripped before the search is sent)
# Requires TAVILY_API_KEY to be set.
#
# Note: web search content is NOT saved to raw_sources/. The flow is direct:
#   query → Tavily → URLs → each URL fetched → wiki pages written
# raw_sources/ is for user-provided local files (PDF, DOCX, PPTX, etc.) only.
# The wiki pages themselves are the persistent output of a web search.
LLM-wiki-DB ingest "search for: Bank of Canada interest rate decisions 2024" -w my-wiki
LLM-wiki-DB ingest "find on the web: unemployment trends Ontario Q1 2025" -w my-wiki

# Multiple web searches at once via a manifest file
# web-searches.txt:
#   search for: Bank of Canada interest rate decisions 2024
#   find on the web: unemployment trends Ontario Q1 2025
#   look up: Toronto housing market affordability index
LLM-wiki-DB ingest --file web-searches.txt -w my-wiki
```

### Querying

```bash
# Ask a question — answer cites wiki pages
LLM-wiki-DB query "What is Moore's Law?" -w my-wiki

# Save the answer as a new wiki page
LLM-wiki-DB query "What is Moore's Law?" --save -w my-wiki
```

### Linting

```bash
# Run a full lint pass (enqueues job)
LLM-wiki-DB lint run -w my-wiki

# Only contradictions
LLM-wiki-DB lint run --scope contradictions -w my-wiki

# Auto-apply high-confidence resolutions
LLM-wiki-DB lint run --auto-resolve -w my-wiki

# Instant report (reads wiki files directly, no server needed)
LLM-wiki-DB lint report -w my-wiki
```

### Monitoring jobs

```bash
# List all jobs (most recent first)
LLM-wiki-DB jobs list -w my-wiki

# Filter by status
LLM-wiki-DB jobs list --status pending -w my-wiki
LLM-wiki-DB jobs list --status failed -w my-wiki
LLM-wiki-DB jobs list --status dead -w my-wiki

# Single job detail
LLM-wiki-DB jobs status <job-id> -w my-wiki

# Retry a dead job
LLM-wiki-DB jobs retry <job-id> -w my-wiki

# Remove old records
LLM-wiki-DB jobs purge --older-than 30 -w my-wiki
```

### Inspecting ingest results

```bash
# Preview how a source will be analysed without writing pages
LLM-wiki-DB ingest report.pdf --analyse-only -w my-wiki
# → {"entities": [...], "tags": [...], "summary": "..."}
```

### Audit trail

```bash
# Ingest history: timestamp, source file, wiki page, tokens, cost
LLM-wiki-DB audit history -w my-wiki            # last 50 records
LLM-wiki-DB audit history -n 100 -w my-wiki     # last 100 records
LLM-wiki-DB audit history --json -w my-wiki     # raw JSON for scripting

# Token usage: totals + daily breakdown (cost always $0.0000 in v0.1)
LLM-wiki-DB audit cost -w my-wiki               # last 30 days
LLM-wiki-DB audit cost --days 7 -w my-wiki      # last 7 days

# Audit events: contradictions found, auto-resolutions, cost gate triggers
LLM-wiki-DB audit events -w my-wiki             # last 100 events
LLM-wiki-DB audit events --json -w my-wiki      # raw JSON for scripting
```

### Scheduling recurring jobs

```bash
# Register a nightly ingest
LLM-wiki-DB schedule add --op "ingest --batch raw_sources/" --cron "0 2 * * *" -w my-wiki

# Weekly lint
LLM-wiki-DB schedule add --op "lint" --cron "0 3 * * 0" -w my-wiki

# List scheduled jobs
LLM-wiki-DB schedule list -w my-wiki

# Remove a scheduled job
LLM-wiki-DB schedule remove <id> -w my-wiki
```

### Removing a wiki

Stop the server for that wiki before uninstalling — the serve process must not be running
when the directory is deleted.

```bash
# Stop the background server (PID is in <wiki-root>/.LLM-wiki-DB/server.pid)
kill $(cat ~/wikis/my-wiki/.LLM-wiki-DB/server.pid)          # Linux / macOS
taskkill /PID <pid> /F                                      # Windows

# Then uninstall — two-step confirmation required, no --yes escape
LLM-wiki-DB uninstall my-wiki
```

---

## Administrative Reference

### Health and status

```bash
# Wiki statistics: pages, queue depth, cache hit rate
LLM-wiki-DB status -w my-wiki

# Liveness probe (useful in scripts and monitoring)
# Port is per-wiki — check [server] port in <wiki-root>/.LLM-wiki-DB/config.toml
# Default is 7070; each additional wiki uses its own port (7071, 7072, …)
curl http://127.0.0.1:7070/health
```

Expected `status` output:
```
Wiki:         /home/user/wikis/my-wiki
Pages:        34
Jobs pending: 0
Jobs total:   12
```

### Logs

LLM-wiki-DB writes three log artefacts per wiki:

| File | Location | Format | Use |
|------|----------|--------|-----|
| `log.md` | `<wiki-root>/log.md` | Human-readable Markdown | Read inside Obsidian; shows every ingest, contradiction, lint event |
| `LLM-wiki-DB.log` | `<wiki-root>/.LLM-wiki-DB/logs/` | JSON lines (rotating) | Structured debug/ops log; grep or pipe to jq |
| `audit.db` | `<wiki-root>/.LLM-wiki-DB/audit.db` | SQLite (append-only) | Source hashes, cost records, job history |

**Tailing the JSON log:**

```bash
# Tail and pretty-print with jq
tail -f .LLM-wiki-DB/logs/LLM-wiki-DB.log | jq .

# Filter to errors only
tail -f .LLM-wiki-DB/logs/LLM-wiki-DB.log | jq 'select(.level == "ERROR")'

# Filter to a specific job
# job_id is present only on records logged in job context (ingest/lint workers)
tail -f .LLM-wiki-DB/logs/LLM-wiki-DB.log | jq 'select(.job_id == "abc123")'
```

**Log rotation:** When `LLM-wiki-DB.log` reaches `max_file_mb`, it is renamed to `LLM-wiki-DB.log.1`; the previous `.1` becomes `.2`; files beyond `backup_count` are deleted. Total disk ≈ `max_file_mb × (backup_count + 1)`.

**Changing log level at runtime:** Edit `[logs] level` in `.LLM-wiki-DB/config.toml` and restart `LLM-wiki-DB serve`. Or pass `--verbose` to get `DEBUG` for one session without editing config.

### Audit trail

```bash
LLM-wiki-DB audit history -w my-wiki          # table: timestamp, source file, wiki page, tokens, cost
LLM-wiki-DB audit history -n 100 -w my-wiki   # last 100 records (default 50)
LLM-wiki-DB audit history --json -w my-wiki   # raw JSON for scripting

LLM-wiki-DB audit cost -w my-wiki             # total tokens + daily breakdown, last 30 days
LLM-wiki-DB audit cost --days 7 -w my-wiki    # weekly view
LLM-wiki-DB audit cost --json -w my-wiki      # {total_tokens, total_cost_usd, daily: [...]}

LLM-wiki-DB audit events -w my-wiki           # table: timestamp, job_id, event type, metadata
LLM-wiki-DB audit events --json -w my-wiki    # raw JSON
```

> **Note:** `cost_usd` is always `$0.0000` in v0.1 — per-model pricing is not yet implemented. Token counts are accurate.

### Cache management

```bash
# Remove all cached LLM responses
# Output: "Cache cleared: N entries removed."
LLM-wiki-DB cache clear -w my-wiki
```

Cache invalidation happens automatically when:
- A source file's SHA-256 hash changes (content changed)
- `CACHE_VERSION` is bumped in `core/cache.py` (after prompt template edits)
- `--force` is passed to ingest

### OpenTelemetry integration

By default, traces and metrics are written to `<wiki-root>/.LLM-wiki-DB/logs/traces.jsonl`. To send to any OTLP backend (Jaeger, Grafana Tempo, Honeycomb, Datadog):

```toml
# ~/.LLM-wiki-DB/config.toml
[observability]
exporter      = "otlp"
otlp_endpoint = "http://localhost:4317"
```

### Debugging

```bash
# Start server with DEBUG console logging
LLM-wiki-DB serve -w my-wiki --verbose

# Check for configuration problems
LLM-wiki-DB status -w my-wiki     # prints pre-flight warnings

# View recent job failures
LLM-wiki-DB jobs list --status failed -w my-wiki
LLM-wiki-DB jobs status <job-id> -w my-wiki    # shows error message + traceback

# Force a re-ingest to rule out cache issues
LLM-wiki-DB ingest --force problem.pdf -w my-wiki
```

---

## Understanding Logs and the Audit Trail

### `log.md` — the human log

Every significant event is appended as a Markdown entry:

```markdown
## 2026-04-10 14:32 | INGEST | constitutional-ai.pdf
- Created: ['constitutional-ai']
- Updated: ['ai-alignment-overview']
- Flagged: ['reward-hacking']
- Tokens: 4,820 | Cost: $0.0000 | Cache hits: 3
```

Open `log.md` in Obsidian to browse and search the full history.

### `LLM-wiki-DB.log` — the structured log

JSON lines format. Each record:

```json
{
  "ts": "2026-04-10T14:32:01",
  "level": "INFO",
  "logger": "LLM-wiki-DB.agents.ingest_agent",
  "msg": "Page created: alan-turing",
  "job_id": "abc123",
  "operation": "ingest",
  "wiki": "history-of-computing"
}
```

Standard fields: `ts`, `level`, `logger`, `msg`. Job-scoped fields (added by `get_job_logger`): `job_id`, `operation`, `wiki`. Future: `trace_id` for OTel correlation.

Log levels follow RFC 5424:

| Level | Used for |
|-------|----------|
| DEBUG | LLM prompt/response bodies, cache keys, BM25 scores |
| INFO  | Job start/complete, page created/updated, server started |
| WARNING | Soft failures (network unreachable), cache miss spikes |
| ERROR | Job failed, LLM API error, file write failed |
| CRITICAL | Server cannot start (port conflict, missing key, bad wiki root) |

### `audit.db` — the immutable record

SQLite, append-only. Records: every ingest (source path, SHA-256, cost, timestamp), every cost threshold crossing, every auto-resolution applied, every job that died. Never modified; only `jobs purge` deletes records older than a threshold.

---

## Customization

### Adding a custom skill (new file format)

Skills tell LLM-wiki-DB how to extract text from a source it doesn't understand out of the box. Add one when you have a proprietary or domain-specific format:

| You have | Skill you'd write |
|----------|------------------|
| Notion workspace export (`.zip`) | Unzip, walk pages, strip Notion-specific markup |
| Confluence space export (`.xml`) | Parse XML, extract page bodies and metadata |
| Slack export archive | Walk channels/messages JSON, format as conversation transcript |
| Internal `.docx` template with custom fields | Strip template boilerplate, extract only the filled-in sections |
| API endpoint or internal database | Fetch records, render as structured Markdown |
| Proprietary binary format (CAD, ERP data) | Convert to text using a vendor SDK, return plain content |

Skills are Apache-2.0 licensed — no AGPL obligation on your own skill code.

1. Create `<wiki-root>/skills/my_format.py` (or `~/.LLM-wiki-DB/skills/` for global availability).
2. Subclass `BaseSkill` (Apache-2.0 licensed — no AGPL obligation on your skill):

```python
# SPDX-License-Identifier: MIT   ← any licence you like
from LLM-wiki-DB.skills.base import BaseSkill, ExtractedContent, SkillMeta

class NotionSkill(BaseSkill):
    @classmethod
    def meta(cls) -> SkillMeta:
        return SkillMeta(
            name="notion",
            description="Extracts text from Notion export ZIP files",
            extensions=[".notion.zip"],
        )

    async def extract(self, source: str) -> ExtractedContent:
        # … your extraction logic …
        return ExtractedContent(text="extracted text …", source_path=source, metadata={})
```

3. Drop the file in the skills directory. LLM-wiki-DB hot-loads it on the next ingest — no restart needed.

**Intent-based dispatch** — skills can also be triggered by a text prefix instead of (or alongside) a file extension. Declare the prefix in the `triggers.intents` list in your `SKILL.md`:

```yaml
# skills/my_search/SKILL.md
---
name: my_search
version: "1.0"
description: "Web search with localised intent prefixes"
entry:
  script: scripts/main.py
  class: MySearchSkill
triggers:
  intents:
    - "搜索:"
    - "查找:"
    - "网络搜索:"
---
```

Strip the prefix in your `extract()` method:

```python
import re
_INTENT_RE = re.compile(r"^(搜索|查找|网络搜索):?\s*", re.UNICODE)

async def extract(self, source: str) -> ExtractedContent:
    query = _INTENT_RE.sub("", source).strip() or source
    # … call your search API with query …
```

Then ingest with the Chinese prefix:

```bash
LLM-wiki-DB ingest "搜索: 量子计算" -w my-wiki
```

Intent matching is a plain substring check — any Unicode text works. Localized prefixes in Chinese, Japanese, Arabic, etc. are fully supported.

To bundle resource files (prompt templates, lookup tables):

```
skills/
  my_format.py
  my_format/
    resources/
      extract_prompt.md
```

Access them inside your skill with `self.get_resource("extract_prompt.md")`.

### Adding a custom LLM provider

Subclass `LLMProvider` from `LLM-wiki-DB/providers/base.py` (also Apache-2.0):

```python
from LLM-wiki-DB.providers.base import LLMProvider, Message, CompletionResponse

class MyProvider(LLMProvider):
    async def complete(self, messages: list[Message], **kwargs) -> CompletionResponse:
        …
```

Place in `~/.LLM-wiki-DB/providers/` or the wiki `providers/` directory.

### Writing hooks

Hooks are shell commands (any language) that receive a JSON context on stdin:

```python
# hooks/auto_commit.py
import json, subprocess, sys
ctx = json.load(sys.stdin)
if ctx["pages_created"] or ctx["pages_updated"]:
    subprocess.run(["git", "add", "-A"], cwd=ctx["wiki"])
    subprocess.run(["git", "commit", "-m", f"ingest: {ctx['source']}"],
                   cwd=ctx["wiki"])
```

Register in `.LLM-wiki-DB/config.toml`:

```toml
[hooks]
on_ingest_complete = "python hooks/auto_commit.py"
```

Available events: `on_ingest_complete`, `on_lint_complete`.

Set `blocking = true` to make the hook gate the operation:

```toml
on_ingest_complete = { cmd = "python hooks/auto_commit.py", blocking = true }
```

### Cache invalidation control

| Scenario | Action |
|----------|--------|
| Source file changed | Automatic — SHA-256 changes, cache miss on next ingest |
| Prompt template edited | Bump `CACHE_VERSION` in `LLM-wiki-DB/core/cache.py` |
| Force fresh LLM call | `LLM-wiki-DB ingest --force <source> -w my-wiki` |
| Wipe all cached responses | `LLM-wiki-DB cache clear -w my-wiki` |

### Per-wiki AGENTS.md

Edit `<wiki-root>/AGENTS.md` to give the LLM domain-specific instructions — what to emphasize, how to name pages, what to cross-reference. This is the highest-priority instruction source for every agent run against this wiki.

---


| Feature | Notes |
|---------|-------|
| **Web UI** | Browser-based dashboard — view pages, run jobs, inspect contradictions and orphans without Obsidian |
| **Vector search + re-ranking** | Hybrid BM25 + `fastembed` local vectors; better recall on semantically related queries |
| **Graph-aware retrieval** | Multi-hop wikilink traversal for queries like "What connects Turing to von Neumann?" |
| **Larger corpus support** | Sharded index, incremental embedding updates, streaming ingest for very large documents |
| **Obsidian plugin: web search live view** | Watch pages appear as results stream in — job polling and live result panel (basic modal already in v0.1) |
| **Mistral + Bedrock providers** | Additional OpenAI-compatible and AWS-native LLM backends |

---

## Links
```

[![CI](https://github.com/william-Johnason/LLM-wiki-DB/actions/workflows/ci.yml/badge.svg)](https://github.com/william-Johnason/LLM-wiki-DB/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-AGPL--3.0-blue.svg)](https://github.com/william-Johnason/LLM-wiki-DB/blob/main/LICENSE)
[![Python](https://img.shields.io/badge/Python-3.11%2B-yellow.svg)](https://www.python.org/)
[![Skills](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fwilliam-Johnason%2FLLM-wiki-DB%2Fmain%2Fdocs%2Fbadges.json&query=%24.skills&label=Skills&color=purple)](https://github.com/william-Johnason/LLM-wiki-DB/tree/main/LLM-wiki-DB/skills)
[![CLI](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fwilliam-Johnason%2FLLM-wiki-DB%2Fmain%2Fdocs%2Fbadges.json&query=%24.cli_commands&label=CLI%20commands&color=darkblue)](https://github.com/william-Johnason/LLM-wiki-DB)
[![Obsidian](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fwilliam-Johnason%2FLLM-wiki-DB%2Fmain%2Fdocs%2Fbadges.json&query=%24.obsidian_commands&label=Obsidian%20commands&color=blueviolet)](https://github.com/william-Johnason/LLM-wiki-DB/tree/main/obsidian-plugin)
[![Version](https://img.shields.io/badge/Community%20Edition-v0.1.0-lightgrey.svg)](https://github.com/william-Johnason/LLM-wiki-DB/releases/tag/v0.1.0)
- Design document: [docs/design.md](docs/design.md)
- Demo walkthrough: [docs/demo-guide.md](docs/demo-guide.md)
- Contributing: [CONTRIBUTING.md](CONTRIBUTING.md)
- Issues: [GitHub Issues](../../issues)

## Acknowledgment
This project is based on code from other  GitHub repositories. I have modified and extended it significantly to fit our use case, including performance improvements and integration with our MCP-based chatbot. Ongoing changes are being managed and shared with the team through this repository.
