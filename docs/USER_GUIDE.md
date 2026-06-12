# User Docs

> **LM Studio Plugin — Documentation RAG with Image Co-Retrieval**
>
> Search your personal Markdown, PDF and LM Studio conversation documentation from within a chat. The plugin retrieves the most relevant text sections and delivers embedded screenshots directly into the conversation.

---

## Table of Contents

- [TL;DR - Quick Start](#quick-start)
- [Key Benefits and Use Cases](#key-benefits-and-use-cases)
- [What You Can Expect](#what-you-can-expect)
- [Fun Factor — Knowledge That Actually Finds Itself](#the-fun-factor)
- [Technical Requirements](#technical-requirements)
- [Setup](#setup)
- [Special Features](#special-features)
- [Known Issues and Solutions](#known-issues-and-solutions)
- [What's Next?](#whats-next)
- [Changelog](#changelog)
- [License](#license)

---

<a id="key-benefits-and-use-cases"></a>

## Key Benefits

Probably, you have documents. Lots of them? Plugin guides, device manuals, personal notes, old conversations with useful information buried in them? And when you actually need something specific, good luck finding it again — especially if the answer sits on page 327 of a PDF or in a chat from three weeks ago.

**LM Studio Plugin: user-docs — Your Documentation retrieved by vision-capable Agents.**

- Hybrid semantic search (BM25 + embeddings) across your local documents
- Image co-retrieval: screenshots and diagrams embedded in documentation are delivered alongside text
- Full PDF support with on-demand page rendering
- LM Studio conversation history as a searchable knowledge base
- Brave image search integration for visual research — results registered directly as `pN` candidates
- All processing is local — no data leaves your machine
- Agent can write, update, and manage notes autonomously

> **TL;DR:** You ask a question. The agent searches through all your docs, finds the answer, shows you the relevant screenshot if one exists, and gives you a short precise response. No more Ctrl+F across 47 files.

---

## Use Cases

### Personal Knowledge Management (PKM)

The most common use case: You collect documentation — plugin guides, how-tos, recipes, notes from research sessions. **user-docs** turns that collection into an actively searchable knowledge base. The agent doesn't just find text; it finds the _right_ screenshot to go with it.

![agent's-capabilities(1)](<images/agent's-capabilities(1).jpeg>)

![agent's-capabilities(2)](<images/agent's-capabilities(2).jpeg>)

![agent's-capabilities(3)](<images/agent's-capabilities(3).jpeg>)

### Device and Product Manuals (PDF)

Got a thick PDF manual for your camera, audio interface, or smart home device? Index it alongside everything else. When you ask about dimensions, settings, or troubleshooting steps, the agent extracts the exact page as an image so you can see diagrams, tables, and illustrations in context.

### Conversation Continuity

Add `~/.lmstudio/conversations` as a content directory and all your previous LM Studio chats become searchable. Found useful information three weeks ago? The agent retrieves it — including any images that were part of those conversations. No manual export needed.

### Remote Documentation Sources

GitHub repositories, static documentation pages, blog posts — **user-docs** can index remote Markdown sources alongside local files. Ask about LM Studio features or plugin configuration and get answers from the actual docs, not hallucinated guesses.

![online-query(1)](<images/online-query(1).jpeg>)

![online-query(2)](<images/online-query(2).jpeg>)

![online-query(3)](<images/online-query(3).jpeg>)

![online-query(4)](<images/online-query(4).jpeg>)

---

<a id="what-you-can-expect"></a>

## What You Can Expect

In terms of maturity: **production-ready core** based on draw-things-chat rev. 42, with some edges still being polished. The RAG pipeline (search → retrieve → answer) is stable and reliable. Image co-retrieval works well for Markdown documents; PDF page extraction is precise. Vision analysis (`analyse_image`, `annotate_image`) is amazing but relies on the agent's instruction-following capabilities for the prompts used. Built-in instructions and notes are subject to continuous improvement.

What this plugin does **not** do: It doesn't replace a proper search engine for web research of text-heavy topics. It searches _your_ documentation, not the internet. For broad textual web research you'd add additional tools like `brave_web_search`. However — **user-docs** has **full native support for `brave_image_search`**. Image search results are registered directly as `pN` candidates that can be inspected with `review_image`, described via `analyse_image`, annotated with bounding boxes using `annotate_image`, or displayed to you with `show_image`. This means the agent can do visual research and immediately work with those images — no manual download needed.

---

<a id="the-fun-factor"></a>

## Fun Factor — Knowledge That Actually Finds Itself

The real magic happens when you stop thinking of this as a "search tool" and start treating it as an active research assistant.

![user-docs-setup(1)](<images/user-docs-setup(1).jpeg>)

![user-docs-setup(2)](<images/user-docs-setup(2).jpeg>)

### Scenario 1: Setup Troubleshooting

You're configuring something complex — maybe the user-docs plugin itself, or Draw Things, or your home automation stack. Instead of re-reading a 40-page guide from scratch, you ask: _"What do I need to configure for feature X?"_ The agent finds the exact section, shows the relevant screenshot with settings highlighted, and gives you three bullet points instead of making you wade through ten pages of text.

### Scenario 2: Cross-Document Research

You have information scattered across a plugin guide (Markdown), a device manual (PDF), and an old chat where someone explained something well. **user-docs** doesn't care about file types — it searches them all simultaneously and synthesizes the answer from whatever sources are relevant.

### Scenario 3: The Living Note System

The agent can write notes during your conversation — capturing decisions, summarizing research sessions, or documenting workflows you discover together. These notes become part of the indexed documentation immediately. Your knowledge base grows organically with every useful chat.

> 💡 **Tip:** Use a capable agent model (27B+ parameters) and let it do what it does best: think about your question, search appropriately, evaluate results visually when needed, and give you an answer that's actually scoped to what you asked. Small models tend to over-fetch or under-filter.

### Scenario 4: Visual Research with Brave Image Search 🔍

Need inspiration? Reference images? Ask the agent to search for images — it calls **brave_image_search**, registers all results as `pN` candidates, inspects them via `review_image`, and shows you the most relevant one. The entire image workflow (inspect → describe → annotate → display) works seamlessly with Brave search results just like with locally retrieved documentation images.

### Scenario 5: Stop Reading This Guide Right Now 🎯

> **Stop reading this USER_GUIDE.md right now and let your user-docs agent guide you through the setup step by step instead.**
>
> _That's_ what **user-docs** was made for. The agent reads this documentation itself, shows you the relevant screenshots, explains each setting live — and checks whether you're ready for the next step. No manual dump. No copy-pasting. Simply: start a chat, get going.
>
> If the **Auto-read user-docs Guide** switch is enabled (default), the agent comes fully pre-configured in every new session — it knows all tools, workflows and rules without you having to explain them. _This guide exists to orient you._ The interactive agent experience is where it actually lives.

---

<a id="technical-requirements"></a>

## Technical Requirements

### Hardware

In addition to the resources your main agent model requires, keep in mind:

- **VRAM overhead:** The embedding model and vision model add approximately **6.5 GB VRAM** on top of your agent model's requirements.
- **Context window:** As with many "agentic" tasks, a large context window is needed — especially when working with agent instructions. A minimum of **32 768 tokens** is recommended; smaller windows will struggle to hold retrieved chunks and conversation history simultaneously.
- **KV Cache stability:** The larger the context window, the more important a stable cache becomes. Both MLX and GGUF backends have seen significant improvements in this area recently — make sure you're on an up-to-date build.
- **Model selection criteria** are comparable to those for coding tasks: favor models with strong instruction-following, reasoning, and long-context performance over raw parameter count alone.

### Software

- **Python 3.9+** — Required for the plugin's internal processing (embedding generation, document indexing). The plugin creates isolated virtual environments internally; nothing pollutes your system Python.
- **LM Studio** — Latest rutime engines highly recommended: `lms runtime update --all`.
- **LM Studio Server**

![lms-server](images/lms-server.jpeg)

> ⚖️ **Distributed setup:** You can run the LM Studio Server on a powerful machine in your network while using the plugin from a lightweight client. Documents are always local to where the plugin runs; only model inference is offloaded via API calls. See [DEPLOYMENT.md](DEPLOYMENT.md).

---

<a id="setup"></a>

## Setup

The setup is straightforward and takes about 5–10 minutes for basic functionality, a bit longer if you want vision analysis as well. The interactive agent guide (enabled by request) walks you through it step by step — but here's the overview:

### Step 1 — Install the Plugin

Install **[ceveyne/user-docs](https://lmstudio.ai/ceveyne/user-docs)** from the LM Studio Hub and activate it in a chat:

![Run in LM Studio](images/run_in_LM_Studio.jpeg)

Setup LM Studio Server:

![Plugin installation via LM Studio Hub](images/lms-server.jpeg)

Download the required models:

| Model                                                            | Purpose                                                              | Required?                                                 |
| ---------------------------------------------------------------- | -------------------------------------------------------------------- | --------------------------------------------------------- |
| `qwen/qwen3.6-27b` (or similar 30B+ model)                       | Agent/orchestrator — the brain that searches, evaluates, and answers | Yes                                                       |
| `ggml-org/bge-m3-Q8_0-GGUF`                                      | Embedding model for semantic search                                  | Yes                                                       |
| `qwen/qwen3-vl-8b` (via LM Studio API) or local Qwen3-VL-8B path | Vision analysis — image description and object detection             | Optional, required for `analyse_image` / `annotate_image` |

### Step 2 — Configure Document Directories

Three types of directories:

| Directory Type                                            | Purpose                                                                                  | Access                   |
| --------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------ |
| **Notes Directory**                                       | Where the agent writes notes, guides, and decisions. Always indexed.                     | Read-write (agent, user) |
| **Content Directories**                                   | Your documentation — Markdown, TXT, PDF files. Searchable but not modified by the agent. | Read-only                |
| **LM Studio Conversations** (`~/.lmstudio/conversations`) | All previous chats become searchable content. Images from those chats are included too.  | Read-only                |

> 📋 The first document in your Notes Directory should be `user-docs.md` — the plugin's core operational guide. You'll find it at:
>
> ```
> ~/.lmstudio/extensions/plugins/ceveyne/user-docs/docs/initial-docs/user-docs.md
> ```
>
> Copy it there and let it become a living document that grows with your usage patterns.

### Step 3 — Load the Embedding Model

In LM Studio, load `ggml-org/bge-m3-Q8_0-GGUF` on the Local Server. This is what powers semantic search across all indexed documents. Keep this as the embedding model unless you have a specific reason to change it (and rebuilt your index).

### Step 4 — Configure Vision Model

If you want `analyse_image` and `annotate_image`:

**Option A — Vision via LM Studio API (recommended):**
Load `qwen/qwen3-vl-8b` on your LM Studio Server. This is the simplest option if you already have a server running.

**Option B — GGUF (any platform):**
Download a GGUF quantization of [`Qwen/Qwen3-VL-8B-Instruct-GGUF`](https://huggingface.co/Qwen/Qwen3-VL-8B-Instruct-GGUF) and set the model path in plugin settings.

**Option C — MLX (Apple Silicon):**
Download [`lmstudio-community/Qwen3-VL-8B-Instruct-MLX-4bit`](https://huggingface.co/lmstudio-community/Qwen3-VL-8B-Instruct-MLX-4bit) and set the model path in plugin settings.

> ⚠️ Models other than those listed above are not recommended unless they're variants (finetunes, quantizations) of these standard models. The plugin validates model paths before starting — missing or incomplete configurations fail with clear error messages rather than silently breaking later.

### Step 5 — Install Python (if needed)

```bash
python3 --version
```

If no suitable version is found, download from [python.org](https://python.org/downloads/). The plugin creates isolated virtual environments internally; nothing pollutes your system Python.

### Step 6 — Start a Chat

The RAG index is built automatically on the first `find_doc` call. First setup may take a few minutes; subsequent sessions use incremental updates via file watching. Sessions start fast after initial indexing.

> 💡 **Auto-read user-docs Guide:** This switch determines whether `user-docs.md` (the agent's operational guide) is loaded at every session start. Leave it ON if you want the agent to come fully pre-configured with all workflows and rules. Turn it OFF for faster cold starts — the agent can still read the guide on demand when needed.

---

<a id="special-features"></a>

## Special Features

### Image Co-Retrieval

This is what makes **user-docs** different from a simple document search tool: whenever retrieved text chunks reference images (embedded base64, Markdown links, Obsidian wikilinks), those images are automatically registered and made available to the agent. The agent can then inspect them with `review_image`, describe them via `analyse_image`, mark regions with `annotate_image`, or simply show you the most relevant one with `show_image`.

**The result:** Answers come with visual evidence, not just text citations. When documentation says "enable this setting", you see a screenshot of that exact screen — if it exists in your docs.

### Brave Image Search Integration 🔍

**brave_image_search** is fully supported and works seamlessly with the entire image pipeline:

1. The agent calls `brave_image_search(query)` to find images on the web
2. All search results are registered as `pN` candidates — just like locally retrieved documentation images
3. The agent inspects them via `review_image`, evaluates relevance, and shows you the best match with `show_image`
4. From there: full access to `analyse_image` (visual description), `annotate_image` (object detection / bounding boxes), or any other image tool

**Example workflow:** _"Find me some reference images for minimalist product photography."_ → The agent searches, reviews results, and displays the most fitting ones — all without you downloading a single file.

### PDF Page Extraction (`extract_image`)

PDF pages aren't indexed automatically (the parsing would be too expensive for every page). Instead, when the agent finds relevant text from a PDF but needs to show you diagrams or tables on a specific page, it renders that page as an image at configurable DPI:

```
find_doc("AV10 dimensions")
  → finds reference in manual.pdf, page 327
extract_image(source="manual.pdf", page=327, dpi=200)
  → shows you the actual specification table with measurements
```

### Remote Documentation Sources

**user-docs** supports indexing external sources:

- **GitHub Markdown repositories** — entire repos or individual files via `github://owner/repo/ref/path` syntax. GitHub issues and PR pages are also supported as direct URL lookups.
- **Static HTML documentation pages** — cooperative docs sites like LM Studio's own documentation
- **Hugging Face model cards and repository pages** (`huggingface.co`)

This is document retrieval, not web crawling. For broad internet research you'd need complementary tools like `brave_web_search`.

### Note Management by the Agent

The agent can autonomously:

- Write new notes with proper YAML frontmatter (`memorize_doc`)
- Update existing notes without regenerating them from scratch (`rewrite_doc`)
- Delete outdated notes after confirming their existence (`forget_doc`)
- Export complete chat conversations as reusable documentation (`export_doc`)
- Manage context window by removing documents no longer needed (`skip_doc`)

All created notes are immediately indexed and become searchable in the next RAG cycle. Your knowledge base grows with every productive conversation.

### Configuration Inspection via `read_config`

The agent can inspect live plugin settings at any time — checking current directory paths, model configurations, API endpoints, etc. This is particularly useful during interactive setup or troubleshooting when you want to verify what's actually configured without navigating through UI panels manually.

---

<a id="known-issues-and-solutions"></a>

## Known Issues and Solutions

### Image Attachments Blocked When Non-Vision Model Is Loaded (Issue #1949)

The LM Studio client only shows image upload controls when a vision-capable model is loaded in the Model Loader. Since **user-docs** registers itself as a tool plugin rather than a VLLM, image attachments may be blocked even though your agent can process images through its tools and workflows.

> **Workaround:** Load a small vision-capable model (e.g., `qwen/qwen3-vl-4b`) in the Model Loader alongside your main agent model to unlock the attachment UI. This is tracked upstream:
> https://github.com/lmstudio-ai/lmstudio-bug-tracker/issues/1949

### Python Not Found

If the plugin can't locate a suitable Python installation, every tool call returns a clear error with a link to python.org. The solution: install Python 3.9+ (or 3.11+ for MLX vision). After that, restart LM Studio — no environment variables needed; the plugin searches standard locations automatically.

### Embedding Model Not Loaded

If `ggml-org/bge-m3-Q8_0-GGUF` isn't loaded on your LM Studio Server when you first call `find_doc`, the index setup fails with a visible error rather than silently degrading. Solution: load the embedding model in Local Server settings before starting searches.

### PDF Pages Not Automatically Indexed

PDF content is searchable by text, but individual pages aren't rendered as images during indexing (too expensive). You need to explicitly request visual inspection of specific pages via `extract_image`. The agent handles this automatically when it detects that a page visualization would be helpful — but if you're working with the tools directly, remember: PDF → search first, then extract.

### Vision Model Path Validation Fails Early

The plugin validates Qwen3-VL model paths before starting any server. Missing files, incomplete directories, or wrong file formats fail immediately with clear error messages rather than causing mysterious failures later during inference. If validation fails: check that your model directory contains the expected files (`config.json` for MLX; `.gguf` + `mmproj-*.gguf` for GGUF).

### Agent Model Context Window Too Small

For documentation-heavy workflows, a small context window fills up quickly with retrieved chunks and image data. Use models with 64K+ token context (128K or more if your hardware allows it). If the agent starts losing track of conversation thread or ignoring earlier results, this is often the culprit.

---

<a id="whats-next"></a>

## What's Next?

- **Expanded remote source support** — more documentation formats and better HTML normalization
- **Lazy PDF thumbnail generation** — visual browsing of indexed PDFs without full page rendering upfront
- **Smarter image search** — based on visual embeddings

---

<a id="quick-start"></a>

## Quick Start (TL;DR)

1. Set up LM Studio Server
2. Install the plugin from LM Studio Hub: [ceveyne/user-docs](https://lmstudio.ai/ceveyne/user-docs)
3. Configure your Notes Directory and Content Directories in plugin settings
4. Load `ggml-org/bge-m3-Q8_0-GGUF` as embedding model on Local Server
5. Set up vision model for image analysis and annotation
6. Install Python 3.9+ if needed
7. Start a chat — the agent guides you through any remaining configuration

See [README.md](README.md) for detailed setup notes and technical specifications.
See [DEPLOYMENT.md](DEPLOYMENT.md) for common deployment scenarios.

---

<a id="changelog"></a>

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and release notes.

---

<a id="license"></a>

## License

MIT
