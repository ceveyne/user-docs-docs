# user-docs

> **Documentation-only repository**
>
> This repository contains _only_ the user documentation for **user-docs** (Markdown + images).
> It intentionally contains **no LM Studio plugin code** and is **not installable**.
>
> Looking for the actual plugin? See: https://lmstudio.ai/ceveyne/user-docs

> **LM Studio Plugin — Documentation RAG with Image Co-Retrieval**

Search your personal conversations, Markdown and PDF documentation from within an LM Studio chat. The plugin retrieves the most relevant text sections and, where available, delivers the images embedded in those sections directly into the conversation.

## What it does

When you ask a question, `user-docs` searches your local documentation directories using hybrid retrieval (BM25 + embeddings). For each retrieved text chunk the plugin extracts image references — including embedded base64 images and Obsidian wikilinks — generates previews, and registers them as `pN` entries in `chat_media_state.json`. This means `review_image`, `analyse_image`, `annotate_image` can operate on retrieved screenshots without any extra configuration.

Known source files or conversations can be image-fetched directly via `fetch_image`. PDF pages can be rendered on demand via `extract_image`.

## Tools

| Tool              | Description                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| `find_doc`        | Hybrid RAG search. Returns text context + registers embedded images as `pN` candidates.                             |
| `read_doc`        | Read a note file in full or partial form. Registers document images by default; base64 payloads are stripped.       |
| `fetch_image`     | Register all images from one exact filename, Markdown document, image file, or LM Studio conversation as `pN`.      |
| `extract_image`   | Render a PDF page to PNG via PyMuPDF and register it as `iN`.                                                       |
| `review_image`    | Present image candidates to the user for selection.                                                                 |
| `review_sequence` | Step through a sequence of images interactively.                                                                    |
| `analyse_image`   | Describe the pixel content of a `pN`/`iN`/`aN` image via FastVLM or the configured LM Studio Vision API backend.    |
| `annotate_image`  | Detect objects and draw bounding boxes via Qwen3-VL.                                                                |
| `show_image`      | Promote a `pN`/`iN` candidate to a visible chat image.                                                              |
| `skip_doc`        | Remove a `read_doc` result from the API context (token saving).                                                     |
| `memorize_doc`    | Write a new note to `notesDirectory` with YAML frontmatter.                                                         |
| `rewrite_doc`     | Overwrite an existing note.                                                                                         |
| `forget_doc`      | Delete a note from `notesDirectory`.                                                                                |
| `export_doc`      | Export the current chat as a Markdown note into `notesDirectory`.                                                   |
| `read_config`     | Return the current plugin settings as JSON so an agent can inspect live configuration values and assist with setup. |

## Workflow example

```
find_doc("How do I activate a plugin?")
→ returns text context + registers p1, p2 (embedded screenshots)

read_doc({"filename":"plugin-guide.md", "show_images":false})
→ reads text only; use default show_images=true to register images too

fetch_image({"filename":"plugin-guide.md"})
→ registers all images from that exact source as pN entries

extract_image("manual.pdf", page=42, dpi=200)
→ renders page 42 as i1

review_image({"targets":["p1"]})    # read pixel content
annotate_image({"targets":["i1"]})  # detect objects on PDF page
show_image({"targets":["p2"]})      # display screenshot to user
```

## Setup

### 1. Install the plugin

Install and activate `user-docs` in LM Studio.

### 2. Configure document locations

In the plugin settings, set:

| Setting                 | Required | Value                                                                                           |
| ----------------------- | -------- | ----------------------------------------------------------------------------------------------- |
| **Notes Directory**     | yes      | Directory for notes created by `memorize_doc` / `export_doc`. This directory is always indexed. |
| **Content Directories** | yes      | Folders containing your Markdown, TXT, or PDF documentation, e.g. `~/Documents/my-docs`.        |

### 3. Load the embedding model

`find_doc` needs the embedding model to be available through LM Studio's OpenAI-compatible API before the first search.

1. In LM Studio, download/load `ggml-org/bge-m3-Q8_0-GGUF`.
2. Keep **Embedding Model** set to `ggml-org/bge-m3-Q8_0-GGUF` unless you deliberately rebuilt the index for a different embedding model.
3. Keep **API server base-URL** at `http://127.0.0.1:1234/v1` unless your LM Studio server uses a different address.

If the embedding model is missing or not loaded, `find_doc` fails during index setup with a visible tool error instead of silently falling back.

### 4. Configure vision, if you want `analyse_image` or `annotate_image`

`find_doc`, `read_doc`, `fetch_image`, `memorize_doc`, and `extract_image` do not require a vision model. The tools `analyse_image` and `annotate_image` do require a local **Qwen3-VL-8B-Instruct** model and a valid **Qwen3-VL: Model Path** before first use.

Choose one backend:

**Option A — MLX (recommended for Apple Silicon)**

Download [`lmstudio-community/Qwen3-VL-8B-Instruct-MLX-4bit`](https://huggingface.co/lmstudio-community/Qwen3-VL-8B-Instruct-MLX-4bit) via the LM Studio model browser, or:

```bash
pip install huggingface-hub
huggingface-cli download lmstudio-community/Qwen3-VL-8B-Instruct-MLX-4bit \
  --local-dir ~/Documents/Models/Qwen3-VL-8B-Instruct-MLX-4bit
```

Expected: the model directory contains `config.json`.

**Option B — GGUF (any platform)**

Download a GGUF quantization of [`Qwen/Qwen3-VL-8B-Instruct-GGUF`](https://huggingface.co/Qwen/Qwen3-VL-8B-Instruct-GGUF) into a local directory.

Expected: the model directory contains both:

- one main `.gguf` model file, not starting with `mmproj-`
- one `mmproj-*.gguf` projector file

Set **Qwen3-VL: Model Path** to the model directory, not to an individual file. `~` is accepted and expanded. The plugin validates this path before starting the server; missing, unreadable, incomplete, or unrecognized model directories fail immediately with a tool-visible error.

### 5. Install Python, if needed

`find_doc`, PDF parsing, and `extract_image` require **Python 3.9+**. The vision MLX backend requires **Python 3.11+**.

The plugin tries LM Studio's own bundled Python first — no extra install required in most cases. If that isn't available, it probes well-known install locations for your OS automatically.

**If Python is not found:**

1. Download and run the standard installer from **[python.org/downloads](https://www.python.org/downloads/)** — not the Microsoft Store version.
2. When the installer asks whether to add Python to PATH, answer **Yes** (this will not help LM Studio find Python directly, but causes no harm).
3. Restart LM Studio.

All packages are installed into isolated venvs inside the plugin directory; nothing is written to your system Python.

### 6. Start a chat

On the first `find_doc` call the RAG index is built automatically. On the first vision call the local server and the selected Python backend environment are created automatically. First setup can take a few minutes; later calls use the ready environments and incremental file watcher updates.

### Python environments

The plugin creates and repairs isolated Python environments automatically:

| Environment                  | Used for                  | Minimum Python | Validation before reuse                |
| ---------------------------- | ------------------------- | -------------- | -------------------------------------- |
| `.rag-venv`                  | RAG parsing, PDF handling | 3.9+           | `python3`, `pip`, `docling`, `fitz`    |
| `.fastvlm/venv`              | local vision HTTP server  | 3.9+           | `python3`, `pip`, `fastapi`, `uvicorn` |
| `.fastvlm/qwen3vl_venv`      | MLX Qwen3-VL backend      | 3.11+          | `python3`, `pip`, `mlx_vlm`, `PIL`     |
| `.fastvlm/qwen3vl_gguf_venv` | GGUF Qwen3-VL backend     | 3.9+           | `python3`, `pip`, `llama_cpp`, `PIL`   |

Ready markers are only trusted when these checks pass. If an environment was deleted or is incomplete, the plugin rebuilds it instead of waiting until a later timeout.

## Configuration reference

| Setting                 | Default                     | Description                                                                                                |
| ----------------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `model`                 | `qwen/qwen3.6-35b-a3b`      | Agent/orchestrator model                                                                                   |
| `baseUrl`               | `http://127.0.0.1:1234/v1`  | LM Studio API base URL                                                                                     |
| `apiKey`                | _(empty)_                   | API key (only if the server requires authentication)                                                       |
| `autoReadUserDocsGuide` | `true`                      | Provide `user-docs.md` as non-persistent user-message context and seed it into `notesDirectory` if missing |
| `notesDirectory`        | `~/Documents/User-docs`     | Notes write target; always indexed                                                                         |
| `contentDirectories`    | `~/Documents`               | Directories to index (TXT, MD, PDF)                                                                        |
| `embeddingModel`        | `ggml-org/bge-m3-Q8_0-GGUF` | Embedding model for semantic retrieval (must be loaded in LM Studio)                                       |
| `embeddingBaseUrl`      | `http://127.0.0.1:1234/v1`  | Embedding/Vision API Base URL                                                                              |
| `embeddingApiKey`       | _(empty)_                   | API key (only if the server requires authentication)                                                       |
| `qwen3VlModelPath`      | `qwen/qwen3-vl-8b`          | Absolute path to the Qwen3-VL model directory                                                              |
| `visionPrompt`          | _(empty)_                   | Default prompt for `analyse_image`. Leave empty to disable automatic description                           |
| `qwen3VlOdPrompt`       | `_(built-in)_`              | Instruction sent to Qwen3-VL for object detection (`annotate_image`)                                       |

## Deployment scenarios

- **[DEPLOYMENT.md](docs/DEPLOYMENT.md)** — Some common deployment scenarios.

## Setup guide images

The interactive setup guide (`SETUP.md`) references screenshots stored in `docs/initial-docs/images/`. These images are **not bundled inside the plugin** — they are excluded from both the build output and the LM Studio Hub upload to stay within the Hub file-count limit.

Instead, the plugin fetches the images lazily from this GitHub repository the first time the setup guide is triggered after a fresh install or update. The fetch is intentional and targets a fixed path in this repository (`docs/initial-docs/images/`). LM Studio has no built-in update mechanism — to install a new version, users must manually delete the existing plugin directory first. That typically removes the entire directory, so the fetch runs again after each reinstall; within a single install it uses the locally cached copies in `docs/initial-docs/images/` inside the plugin's install directory.

Only `SETUP.md` and `docs/initial-docs/images/` are tracked in this repository; all other files under `docs/initial-docs/` are git-ignored.

## License

See [LICENSE](LICENSE).
