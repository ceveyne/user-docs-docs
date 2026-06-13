# User Docs

> **LM Studio Plugin — Personal Knowledge Management supported by vision-capable Agents**
>
> Search your personal Markdown, PDF and LM Studio conversation documentation from within a chat. The plugin retrieves the most relevant text sections and delivers embedded screenshots directly into the conversation.

---

## Table of Contents

- [TL;DR - Quick start](#quick-start)
- [Key benefits and use cases](#key-benefits-and-use-cases)
- [What you can expect](#what-you-can-expect)
- [Fun factor — Knowledge that actually finds itself](#the-fun-factor)
- [Setup](#setup)
- [Special features](#special-features)
- [Known issues and solutions](#known-issues-and-solutions)
- [What's next?](#whats-next)
- [Changelog](#changelog)
- [License](#license)

---

<a id="key-benefits-and-use-cases"></a>

## Key benefits

Probably, you have documents. Lots of them? Plugin guides, device manuals, personal notes, old conversations with useful information buried in them? And when you actually need something specific, good luck finding it again — especially if the answer sits on page 327 of a PDF or in a chat from three weeks ago.

**LM Studio Plugin: user-docs — Personal Knowledge Management supported by vision-capable Agents.**

- Hybrid semantic search (BM25 + embeddings) across your local documents
- Image co-retrieval: screenshots and diagrams embedded in documentation are delivered alongside text
- Full PDF support with on-demand page rendering
- LM Studio conversation history as a searchable knowledge base
- Brave image search integration for visual research — results registered directly as `pN` candidates
- All processing is local — no data leaves your machine
- Agent can write, update, and manage notes autonomously

> **TL;DR:** You ask a question. The agent searches through all your docs, finds the answer, shows you the relevant screenshot if one exists, and gives you a short precise response. No more Ctrl+F across 47 files.

---

## Use cases

### Personal Knowledge Management (PKM)

The most common use case: You collect documentation — plugin guides, how-tos, recipes, notes from research sessions. **user-docs** turns that collection into an actively searchable knowledge base. The agent doesn't just find text; it finds the _right_ screenshot to go with it.

![agent's-capabilities(1)](<images/agent's-capabilities(1).jpeg>)

![agent's-capabilities(2)](<images/agent's-capabilities(2).jpeg>)

![agent's-capabilities(3)](<images/agent's-capabilities(3).jpeg>)

### Device and product manuals (PDF)

Got a thick PDF manual for your camera, audio interface, or smart home device? Index it alongside everything else. When you ask about dimensions, settings, or troubleshooting steps, the agent extracts the exact page as an image so you can see diagrams, tables, and illustrations in context.

### Conversation continuity

Add `~/.lmstudio/conversations` as a content directory and all your previous LM Studio chats become searchable. Found useful information three weeks ago? The agent retrieves it — including any images that were part of those conversations. No manual export needed.

### Remote documentation sources

GitHub repositories, static documentation pages, blog posts — **user-docs** can index remote Markdown sources alongside local files. Ask about LM Studio features or plugin configuration and get answers from the actual docs, not hallucinated guesses.

![online-query(1)](<images/online-query(1).jpeg>)

![online-query(2)](<images/online-query(2).jpeg>)

![online-query(3)](<images/online-query(3).jpeg>)

![online-query(4)](<images/online-query(4).jpeg>)

---

<a id="what-you-can-expect"></a>

## What you can expect

In terms of maturity: **production-ready core** based on draw-things-chat rev. 42, with some edges still being polished. The RAG pipeline (search → retrieve → answer) is stable and reliable. Image co-retrieval works well for Markdown documents; PDF page extraction is precise. Vision analysis (`analyse_image`, `annotate_image`) is amazing but relies on the agent's instruction-following capabilities for the prompts used. Built-in instructions and notes are subject to continuous improvement.

What this plugin does **not** do: It doesn't replace a proper search engine for web research of text-heavy topics. It searches _your_ documentation, not the internet. For broad textual web research you'd add additional tools like `brave_web_search`. However — **user-docs** has **full native support for `brave_image_search`**. Image search results are registered directly as `pN` candidates that can be inspected with `review_image`, described via `analyse_image`, annotated with bounding boxes using `annotate_image`, or displayed to you with `show_image`. This means the agent can do visual research and immediately work with those images — no manual download needed.

---

<a id="the-fun-factor"></a>

## Fun factor — Knowledge that actually finds itself

The real magic happens when you stop thinking of this as a "search tool" and start treating it as an active research assistant.

![user-docs-setup(1)](<images/user-docs-setup(1).jpeg>)

![user-docs-setup(2)](<images/user-docs-setup(2).jpeg>)

### Scenario 1: Setup troubleshooting

You're configuring something complex — maybe the user-docs plugin itself, or Draw Things, or your home automation stack. Instead of re-reading a 40-page guide from scratch, you ask: _"What do I need to configure for feature X?"_ The agent finds the exact section, shows the relevant screenshot with settings highlighted, and gives you three bullet points instead of making you wade through ten pages of text.

### Scenario 2: Cross-document research

You have information scattered across a plugin guide (Markdown), a device manual (PDF), and an old chat where someone explained something well. **user-docs** doesn't care about file types — it searches them all simultaneously and synthesizes the answer from whatever sources are relevant.

### Scenario 3: The living note system

The agent can write notes during your conversation — capturing decisions, summarizing research sessions, or documenting workflows you discover together. These notes become part of the indexed documentation immediately. Your knowledge base grows organically with every useful chat.

> 💡 **Tip:** Use a capable agent model (27B+ parameters) and let it do what it does best: think about your question, search appropriately, evaluate results visually when needed, and give you an answer that's actually scoped to what you asked. Small models tend to over-fetch or under-filter.

### Scenario 4: Visual research with Brave Image Search 🔍

Need inspiration? Reference images? Ask the agent to search for images — it calls **brave_image_search**, registers all results as `pN` candidates, inspects them via `review_image`, and shows you the most relevant one. The entire image workflow (inspect → describe → annotate → display) works seamlessly with Brave search results just like with locally retrieved documentation images.

### Scenario 5: Stop reading this guide 👀

> **Stop reading this USER_GUIDE.md right now and let your user-docs agent guide you through the setup step by step instead.**
>
> _That's_ what **user-docs** was made for. The agent reads this documentation itself, shows you the relevant screenshots, explains each setting live — and checks whether you're ready for the next step. No manual dump. No copy-pasting. Simply: start a chat, get going.
>
> If the **Auto-read user-docs Guide** switch is enabled (default), the agent comes fully pre-configured in every new session — it knows all tools, workflows and rules without you having to explain them. _This guide exists to orient you._ The interactive agent experience is where it actually lives.

---

<a id="setup"></a>

## Setup

The setup is straightforward and takes about 5–10 minutes for basic functionality, a bit longer if you want vision analysis as well. The interactive agent guide (enabled by request) walks you through it step by step — but here's the overview:

See [README.md](../README.md#setup) for step-by-step walk-through.

---

<a id="special-features"></a>

## Special features

### Image co-retrieval

This is what makes **user-docs** different from a simple document search tool: whenever retrieved text chunks reference images (embedded base64, Markdown links, Obsidian wikilinks), those images are automatically registered and made available to the agent. The agent can then inspect them with `review_image`, describe them via `analyse_image`, mark regions with `annotate_image`, or simply show you the most relevant one with `show_image`.

**The result:** Answers come with visual evidence, not just text citations. When documentation says "enable this setting", you see a screenshot of that exact screen — if it exists in your docs.

### Brave Image Search integration 🔍

**brave_image_search** is fully supported and works seamlessly with the entire image pipeline:

1. The agent calls `brave_image_search(query)` to find images on the web
2. All search results are registered as `pN` candidates — just like locally retrieved documentation images
3. The agent inspects them via `review_image`, evaluates relevance, and shows you the best match with `show_image`
4. From there: full access to `analyse_image` (visual description), `annotate_image` (object detection / bounding boxes), or any other image tool

**Example workflow:** _"Find me some reference images for minimalist product photography."_ → The agent searches, reviews results, and displays the most fitting ones — all without you downloading a single file.

### PDF page extraction (`extract_image`)

PDF pages aren't indexed automatically (the parsing would be too expensive for every page). Instead, when the agent finds relevant text from a PDF but needs to show you diagrams or tables on a specific page, it renders that page as an image at configurable DPI:

```
find_doc("AV10 dimensions")
  → finds reference in manual.pdf, page 327
extract_image(source="manual.pdf", page=327, dpi=200)
  → shows you the actual specification table with measurements
```

### Remote documentation sources

**user-docs** supports indexing external sources:

- **GitHub Markdown repositories** — entire repos or individual files via `github://owner/repo/ref/path` syntax. GitHub issues and PR pages are also supported as direct URL lookups.
- **Static HTML documentation pages** — cooperative docs sites like LM Studio's own documentation
- **Hugging Face model cards and repository pages** (`huggingface.co`)

This is document retrieval, not web crawling. For broad internet research you'd need complementary tools like `brave_web_search`.

### Note management by the agent

The agent can autonomously:

- Write new notes with proper YAML frontmatter (`memorize_doc`)
- Update existing notes without regenerating them from scratch (`rewrite_doc`)
- Delete outdated notes after confirming their existence (`forget_doc`)
- Export complete chat conversations as reusable documentation (`export_doc`)
- Manage context window by removing documents no longer needed (`skip_doc`)

All created notes are immediately indexed and become searchable in the next RAG cycle. Your knowledge base grows with every productive conversation.

### Configuration inspection via `read_config`

The agent can inspect live plugin settings at any time — checking current directory paths, model configurations, API endpoints, etc. This is particularly useful during interactive setup or troubleshooting when you want to verify what's actually configured without navigating through UI panels manually.

---

<a id="known-issues-and-solutions"></a>

## Known issues and solutions

### Image attachments blocked when non-vision model is loaded (issue #1949)

The LM Studio client only shows image upload controls when a vision-capable model is loaded in the Model Loader. Since **user-docs** registers itself as a tool plugin rather than a VLLM, image attachments may be blocked even though your agent can process images through its tools and workflows.

> **Workaround:** Load a small vision-capable model (e.g., `qwen/qwen3-vl-4b`) in the Model Loader alongside your main agent model to unlock the attachment UI. This is tracked upstream:
> https://github.com/lmstudio-ai/lmstudio-bug-tracker/issues/1949

### Agent model context window too small

For documentation-heavy workflows, a small context window fills up quickly with retrieved chunks and image data. Use models with 64K+ token context (128K or more if your hardware allows it). If the agent starts losing track of conversation thread or ignoring earlier results, this is often the culprit.

### Troubleshooting quick links

See [README.md](../README.md#troubleshooting-quick-links) for additional troubleshooting quick links.

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
4. Load `ggml-org/bge-m3-Q8_0-GGUF` as embedding model on local server
5. Set up vision model for image analysis and annotation
6. Install Python 3.9+ if needed
7. Start a chat — the agent guides you through any remaining configuration

See [README.md](../README.md) for detailed setup notes and technical specifications.
See [DEPLOYMENT.md](DEPLOYMENT.md) for common deployment scenarios.

---

<a id="changelog"></a>

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and release notes.

---

<a id="license"></a>

## License

MIT
