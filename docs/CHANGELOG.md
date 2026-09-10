# Changelog

Notable changes to this project will be documented in this file.

## Distribution

- **Source Code:** [LM Studio Hub](https://lmstudio.ai/ceveyne/user-docs)
- **Documentation:** [GitHub Repository](https://github.com/ceveyne/user-docs-docs)

---

## [0.1.14] - 2026-09-10 Revision 14

### Added

- Added support for image extraction from DOCX. DOCX images can now be registered for review and processing.

### Changed

- Upgraded the docling parsing library.

### Fixed

- PDF parsing could silently extract far less text and fewer embedded images than a PDF actually contained; upgraded the underlying parsing library to fix this.
- Fixed RAG chunking failing on Markdown documents with embedded Base64 images: raw image payload could be chunked as text and could exceed the embedding model's context window. Base64 image data is now replaced with a placeholder before chunking.
- LM Studio conversations stored in subfolders are no longer unnecessarily re-indexed.

---

## [0.1.13] - 2026-09-08 Revision 13

### Added

- Added support for image extraction from PDF. PDF images can now be registered for review and processing.

---

## [0.1.12] - 2026-09-06 Revision 12

### Changed

- Detected object labels no longer contain commas or other punctuation.
- Selecting stored detections by label (`detectLabel`) in `annotate_image` now works reliably when passing a list of labels.

## [0.1.11] - 2026-08-16 Revision 11

### Added

- Added support for Qwen3.8 27B.

### Fixed

- Fixed rare text corruption (scrambled or missing words) that could appear right after a thinking phase, especially just before a tool call.

---

## [0.1.10] - 2026-07-23 Revision 10

### Added

- Added support for image tags. When using the [find-image plugin](https://lmstudio.ai/ceveyne/find-image), images can now be tagged and found with exact tag filters.

---

## [0.1.9] - 2026-07-18 Revision 9

### Changed

- New image previews now stay compact and consistent across chat media.
- Picture search results now include their usable `pN` indexes.
- Improved tool descriptions

### Fixed

- `read_doc` now returns readable text for PDF files instead of binary gibberish.
- Images found via other tools (e.g. **find-image**) could incorrectly show up as newly generated variants.

---

## [0.1.8] - 2026-07-09 Revision 8

### Added

- Added support for new plugin **find-image**.

### Changed

- Moved persistent RAG data (`.rag-data`) and the managed RAG Python environment (`.rag-venv`) to `~/.user-docs`, so plugin updates no longer remove the local index or virtual environment.

### Fixed

- `export_doc` now copies LM Studio attachment previews from the chat working directory into `images/` and rewrites the exported Markdown links to those preview files, so exported attachment images render reliably.

---

## [0.1.7] - 2026-06-26 Revision 7

### Changed

- Vision object detection now always applies the concise label-format rule, including task-specific detection prompts, so generated detection labels stay short and specific.
- Updated user documentation (README.md, DEPLOYMENT.md)

---

## [0.1.6] - 2026-06-20 Revision 6

### Changed

- `annotate_image` tool-result JSON no longer echoes the caller-supplied detection `task`; detection state and audit metadata still keep the internal task where needed.

---

## [0.1.5] - 2026-06-20 Revision 5

### Changed

- Vision inference for `analyse_image` and `annotate_image` now uses only the configured `embeddingBaseUrl` / LM-Studio-compatible Vision API path; the legacy local Python/FastVLM backend was removed.
- Cleaned up settings by removing legacy local vision server endpoint, TTL, and port options while keeping API token, model, prompt, max-token, and temperature controls.

---

## [0.1.4] - 2026-06-17 Revision 4

### Changed

- Vision primer now uses a two-phase lazy-init flow (core 4B at startup, policy-resolved model on first generate) to avoid duplicate model loading and ensure correct config resolution.
- Updated user documentation (README.md)

---

## [0.1.3] - 2026-06-14 Revision 3

### Changed

- Enhanced tool descriptions.
- Fine-tuned tool results.

---

## [0.1.2] - 2026-06-13 Revision 2

### Changed

- Updated user documentation (README.md, USER_GUIDE.md, DEPLOYMENT.md).

---

## [0.1.1] - 2026-06-12 Revision 1

### Added

- Initial release
- User-centered knowledge-support
- PDF as document-type (including complex, table and image-loaded PDF files)
- indexing various content directories
- indexing LM Studio conversations
- image-based answers including annotated screenshots
- host, develop and use complex agentic workflows
- enable your vision-capable agent to review images from your personal knowledge base
- generate ready-to-use image-based documents
