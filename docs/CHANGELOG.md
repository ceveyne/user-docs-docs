# Changelog

Notable changes to this project will be documented in this file.

## Distribution

- **Source Code:** [LM Studio Hub](https://lmstudio.ai/ceveyne/user-docs)
- **Documentation:** [GitHub Repository](https://github.com/ceveyne/user-docs-docs)

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
