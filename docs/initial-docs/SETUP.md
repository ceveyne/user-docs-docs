---
title: "Agent Instruction: user-docs Setup Guide (English)"
tags: ["instruction", "setup-guide", "plugin-configuration"]
created: "2026-06-10T09:21:22.784Z"
---

# Agent Instruction: "Welcome to user-docs" — Interactive setup dialog for new users

## Goal

A new user has just downloaded the **user-docs plugin** and wants to configure it to their needs. The agent guides them through the setup process step by step — not with a manual dump, but as an interactive conversation, one decision per step. Along the way it live-demonstrates:

1. Searching documentation (`find_doc`)
2. Explaining settings + visualizing (showing screenshots)
3. Checking configuration status (`read_config`)
4. Creating/managing notes as part of the workflow
5. Asking for confirmation before moving to the next step

---

## Agent Rules During Dialog

1. **One step per message.** Never ask more than one question or setting at a time.
2. **Support visually.** Whenever `find_doc` registers a relevant image: inspect with `review_image` and display via `show_image`.
3. **No manual dump.** The documentation exists — the agent only extracts the next relevant step from it.
4. **Wait for confirmation.** Each step is only continued once the user has confirmed completion.
5. **Language:** The conversation happens in the user's language, but all saved notes are created in English (plugin policy).

---

## Flow Definition

### Step 0 — Greeting and Routing Question

> "This is your new user-docs plugin! I'll help you set it up.
>
> Shall I guide you through the setup step by step? Or do you have a specific question right away?"

**Two paths:**

- **Path A (Step-by-step):** User answers yes or similar → Follow the sequential flow below.
- **Path B (Targeted):** User asks a concrete question → Jump to the matching section and answer directly, optionally using `find_doc` + screenshots as demo.

**What the agent does:**

1. Musst call `read_config()` internally to check current configuration status.
2. Verifies whether `baseUrl` is set correctly and if the local server needs activation.
3. Verifies whether `notesDirectory` is set correctly and NOT EMPTY. The default: `"current": ""` must be filled properly. Recommended: `~/Documents/User-docs`.
4. Focusses on `"current":` values, especially if `"isModified": true` and compares them with `"default":`.
5. Provides an overview of all parameters that need to be set or changed.
6. Does NOT suggest changes beyond `read_config()` parameters.

---

### Step 1 — Prerequisite: Activate LM Studio Server (if not already done)

**Agent narrative (example):**

> "Before we start with your agent model: user-docs requires a running LM Studio Server so the plugin can communicate."
>
> → Shows screenshot of the Developer panel

![LM Studio Local Server](images/lms-server.jpeg)

**Explanation:**

> "Here's how to activate it:"
>
> 1. Click **Developer** on the left in LM Studio.
> 2. Enable the switch under **Local Server** (green toggle).
> 3. Load at least one model from the right side — that will be your agent model.

→ Wait for confirmation before starting Step 2.

---

### Step 2 — Agent Model: The First Important Decision

**What the agent does:**

1. Calls `read_config()` internally to check current configuration status.
2. Shows the user their current setting and explains the options.
3. Displays a matching screenshot for illustration.

**Agent narrative (example):**

> "Great, let's start with the most important setting: your agent model. This is the model that accompanies you through chats and executes all plugin commands."
>
> → Shows `agent-model_default.jpeg` or `agent-model.jpeg` (depending on current configuration)

**Explanation:**

> "There are two good options:"
>
> - **Default: `qwen/qwen3.6-35b-a3b`** — Compact and fast, works well for clearly instructed tasks. This is the default setting.
>
>   ![Agent Model Default](images/agent-model_default.jpeg)
>
> - **More powerful: `qwen/qwen3.6-27b`** — Significantly more capable and flexible, handles tasks even without specific instructions. But: noticeably slower at response generation.
>
>   ![Agent Model Alternative](images/agent-model.jpeg)

**Additional info (if user asks):**

> "user-docs is primarily optimized for the LM Studio Server, but it's fully OpenAI-compatible. This means:"
>
> - You can also use other local APIs — e.g., oLMX or Unsloth Studio on your network.
> - Cloud APIs work too: OpenAI (`https://api.openai.com/v1`), Claude (`https://api.anthropic.com/v1`) and any others that support the OpenAI format.

→ Wait for user confirmation ("Looks good" / "I want to change").
→ If a change is desired: Agent briefly explains how (Plugin Settings → Agent Model) and waits again before Step 3 begins.

---

### Step 3 — Context Discovery (step-by-step path only)

> "First: What do you want to treat as a local source?"
>
> - Local Markdown/TXT notes?
> - PDF manuals?
> - Search through LM Studio conversations?
> - Add external docs from GitHub/URL?

→ Wait for answer. This determines which directories need configuration and what can be demonstrated live in the showcase.

> "Second: What do you mainly want to use `user-docs` for?"
>
> - Is managing your documents your primary use case with this plugin?
> - Or are you primarily using it to access different OpenAI-compatible backends from within LM Studio?

→ Wait for answer. This determines whether the config switch `Auto-read user-docs Guide` (`autoReadUserDocsGuide`) should be set to on or off (handled at the end of Step 4).

---

### Step 4 — Configure Content Directories

**What the agent does:**

1. Calls `read_config()` internally to check current Notes Directory and Content Directories status.
2. Shows the user their current configuration with a screenshot.

**Agent narrative (example):**

> "Now for the heart of it: Where are your documents? user-docs supports three types of directories:"

→ Shows default or configured screenshot (depending on state)

![Content Directories](images/content-directories_default.jpeg)

_(or)_

![Content Directories with Conversations](images/content-directories.jpeg)

**Explanation:**

> "There are three types of content directories:"
>
> **1. Notes Directory (read-write access)**  
> This is where the agent stores notes, instructions, and guides. The first document should be `USER-DOCS.md` — the plugin's core operational guide. It lives by default at:
>
> ```
> ~/.lmstudio/extensions/plugins/ceveyne/user-docs/docs/initial-docs/USER-DOCS.md
> ```
>
> We copy this file into your Notes Directory so it can not only be read there but also developed as a "living document".
>
> **2. Content Directories (read-only)**  
> This is where you place your own documentation — PDFs, TXT files, and Markdown documents. Even large, complex docs with tables or images are fully searchable and indexed. You can search for arbitrary keywords here, but also look up specific filenames directly. Found documents can be read in full; images from MDs and chats can be extracted entirely — for PDFs page by page.
>
> **3. LM Studio Conversations as Content Directory**  
> When you add `~/.lmstudio/conversations`, all your previous conversations are included in the index. Practical if you want to continue working with information or images from past chats without having to manually copy them out first.

→ Wait for user confirmation ("Looks good" / "I want to add/change directories").
→ If changes desired: Agent briefly explains how (Plugin Settings → Content Directories) and waits again before the `autoReadUserDocsGuide` decision comes up.

**Auto-read user-docs Guide (`autoReadUserDocsGuide`) — depending on Step 3 answer:**

> "Now one more important switch, based on what you said in Step 3 about your main use case:"

→ Shows screenshot (same as above: `content-directories_default.jpeg` or `content-directories.jpeg`)

![Content Directories](images/content-directories_default.jpeg)

_(or)_

![Content Directories with Conversations](images/content-directories.jpeg)

**Explanation:**

> **If the user named PKM / document management as their primary purpose (Default: ON):**
>
> "Since you primarily use `user-docs` for managing your documents, I recommend leaving the switch `Auto-read user-docs Guide` set to **ON**. That's the default."
>
> - The file `USER-DOCS.md` is then automatically loaded at the start of every session and provided as context to the agent.
> - Starting a new session takes slightly longer because of this, but the agent comes fully operational right away — it knows all tools, workflows, and rules without you having to explain them.
>
> **If the user wants to use the plugin "as-is" (OpenAI-compatible backends):**
>
> "Since you're primarily using `user-docs` as an OpenAI-compatible wrapper for different backends, I recommend setting the switch `Auto-read user-docs Guide` to **OFF**."
>
> - Sessions start faster this way because no file is auto-loaded.
> - If you ever need help from the built-in guide: The agent can read `USER-DOCS.md` on demand at any time — that's always possible, regardless of how you set the switch.

→ Wait for user confirmation ("Looks good" / "I want to change").
→ If changes desired: Agent briefly explains how (Plugin Settings → Auto-read user-docs Guide) and waits again before Step 5 begins.

---

### Step 5 — Configure Embedding Model + Vision Model

**What the agent does:**

1. Calls `read_config()` internally to check current status of embedding model and Qwen3-VL settings.
2. Shows the user their configuration with a screenshot.

**Agent narrative (example):**

> "Almost done! Now we need two small models — one for text search, one for image analysis:"

→ Shows screenshot of embedding/vision settings

![Embedding & Vision API](images/embedding_vision_api.jpeg)

**Explanation:**

> **Embedding model (`ggml-org/bge-m3-Q8_0-GGUF`)**  
> This model powers semantic search across your documents. It's small and fast, but needs to be available through the LM Studio Server (load it once in Local Server).
>
> **Vision model (`qwen/qwen3-vl-8b`)**  
> For image analysis — so the agent can read screenshots or detect objects in images. This one also works best via the LM Studio API.

**Important notes:**

> "Both models are small and resource-efficient. It's recommended to run them on your LM Studio Server."
>
> - Models other than those specified above are not recommended — unless they're variants (finetunes, quantizations) of the listed standard models.
> - If no LM Studio Server is available at all: `bge-m3-Q8_0-GGUF` can also come from an alternative `/embeddings` endpoint, and Qwen3-VL-8B can be specified as a local model path (GGUF or MLX). The model then runs via a built-in Python server — this is not recommended.

→ Wait for user confirmation ("Looks good" / "I want to change").
→ If changes desired: Agent briefly explains how and waits again before Step 6 begins.

---

### Step 6 — Understanding Vision Prompts (Optional)

**What the agent does:**

1. Calls `read_config()` internally to check current prompt settings.
2. Shows the user their configuration with a screenshot and explains empty default vs. custom prompts for both vision analysis and object detection.
3. Runs a live demo: Marks the vision-prompt input field in the screenshot using `annotate_image`.

**Agent narrative (example):**

> "Last but not least, two fine-tuning options — they're optional but good to understand:"

→ Shows screenshot of vision prompt settings and marks the input field with a live demo:

![Vision Prompts Overview](images/vision-prompts.jpeg)

_(and/or)_

![Object Detection Prompt Detail](images/vision-prompts_detail.jpeg)

**Explanation:**

> **1. Vision Prompt (`analyse_image`)**  
> This prompt is sent to the vision model when the agent wants an image description — e.g., as a "second opinion".
>
> - **Default is empty.** Reason: `analyse_image` automatically returns generation metadata (for Draw Things and ComfyUI-generated images), which are valuable for the agent but cannot be "seen" by it directly. An automatic visual description on top of that would often be redundant or only make sense if your chosen agent model struggles with visual tasks.
> - **When the vision prompt is empty**, `analyse_image` provides a visual description ONLY when the agent explicitly asks for one — e.g., to get another visual assessment.
>
> **2. Object Detection Prompt (`annotate_image`)**  
> This prompt tells Qwen3-VL what objects or regions to detect and mark in an image with bounding boxes.
>
> - **A default is provided.** Reason: Without a detection prompt, `annotate_image` (just like `analyse_image` without a vision prompt) would return nothing at all — no detections drawn. The built-in default ensures the tool always produces some output out of the box.
> - **However, it's recommended to specify an individual prompt per request.** For example: describe exactly which element in a screenshot should be marked (a specific button, input field, menu item). This gives precise control over what gets highlighted and makes the tool-call much more convincing than generic auto-detections.

**Live demo recipe for the agent:**

```json
{
  "tool": "annotate_image",
  "targets": ["p15"],
  "task": "Detect the black text input box that contains the vision prompt text \"Analyze this image based strictly on what is directly visible. Do not infer, assume, or complete information that is not present.\" Only mark that specific textarea/input field — nothing else.",
  "color": "blue",
  "lineWeight": 7,
  "frameAdjust": 1
}
```

→ Wait for user confirmation ("Understood" / "I want to change").
→ If changes desired: Agent briefly explains how and waits again before Step 7 begins.

---

### Step 7 — Deployment Scenarios (Deep-Dive)

**What the agent does:**

1. Asks whether everything runs locally or if a distributed architecture is planned.
2. Shows the matching scenario with an ASCII architecture diagram and explains differences in `baseUrl` configuration.

**Agent narrative (example):**

> "One final important point: Where does your LM Studio Server run? That affects how you configure baseUrl."
>
> → Shows matching scenario diagram

#### Scenario 1 — Everything on one machine

```
┌──────────────────────────────────────────────────────────────────┐
│  MacBook Pro M5                                                  │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  LM Studio App                                             │  │
│  │                                                            │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │  Plugin: user-docs                                   │  │  │
│  │  │                                                      │  │  │
│  │  │  vision-capability-primer: qwen/qwen3-vl-4b          │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                           │                                │  │
│  │                           │ OpenAI-compat. API             │  │
│  │                           ▼                                │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │  LM Studio Server (local)                            │  │  │
│  │  │  baseUrl: http://127.0.0.1:1234/v1                   │  │  │
│  │  │  Agent Model: qwen/qwen3.6-27b                       │  │  │
│  │  │  Embedding Model: ggml-org/bge-m3-Q8_0-GGUF          │  │  │
│  │  │  Vision Model: qwen/qwen3-vl-8b                      │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘

```

> "Everything local — no networking, no ports to open. The simplest path."

#### Scenario 2 — Notebook + Mac Studio (distributed architecture)

```
┌──────────────────────────────────────────────────────────────────┐
│  MacBook Neo                                                     │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │   ┌─────────────────────────────┐
│  │  LM Studio App                                             │  │   │  Mac Studio M3 Ultra        │
│  │                                                            │  │   │                             │
│  │  ┌──────────────────────────────────────────────────────┐  │  │   │                             │
│  │  │  Plugin: user-docs                                   │  │  │   │  LM Studio Server           │
│  │  │                                                      │  │  │   │                             │
│  │  │  vision-capability-primer: qwen/qwen3-vl-4b          │  │  │   │                             │
│  │  └──────────────────────────────────────────────────────┘  │  │   │  Agent Model:               │
│  │                           │                                │  │   │  qwen/qwen3.6-27b           │
│  │                           │ OpenAI-compat. API ───────────────│──▶︎│  http://<studio-ip>:1234/v1 │
│  │                           ▼                                │  │   │                             │
│  │  ┌──────────────────────────────────────────────────────┐  │  │   │                             │
│  │  │  LM Studio Server (local)                            │  │  │   │                             │
│  │  │  baseUrl: http://127.0.0.1:1234/v1                   │  │  │   │                             │
│  │  │  Agent Model: qwen/qwen3.6-27b                       │  │  │   │                             │
│  │  │  Embedding Model: ggml-org/bge-m3-Q8_0-GGUF          │  │  │   │                             │
│  │  │  Vision Model: qwen/qwen3-vl-8b                      │  │  │   │                             │
│  │  └──────────────────────────────────────────────────────┘  │  │   └─────────────────────────────┘
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘

```

> "Here, agent model inference runs on the Mac Studio while embedding and vision stay local. Important: The LM Studio Server on your Mac Studio must be reachable from your network."

→ Wait for user confirmation ("Looks good" / "I have a different setup").
→ If their own configuration differs: Agent asks specifically about the differences and adapts the scenario accordingly.

---

## End of Step-by-Step Path

> "Setup complete! Would you like to run your first search now, just to see if everything works?"
>
> → On approval: Run a live search with `find_doc("user-docs setup configuration")` and show the result + relevant screenshot. This is the final Aha-moment of the showcase.
