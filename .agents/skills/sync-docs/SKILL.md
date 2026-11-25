---
name: sync-docs
description: Synchronize documentation between root QUESTIONS.md/answers and Docusaurus content. Use when the user adds, updates, or deletes questions or detailed answers in any of the locations.
---

# Documentation Synchronization

This skill teaches the agent how to perform bi-directional synchronization between the main content and Docusaurus.

## Synchronization Workflow

1. **Root to Docs Sync**:
   - If `QUESTIONS.md` is updated, find the corresponding `.mdx` files in `docs/docs/questions/` and update them.
   - If an `answers/*.md` file is updated, update its counterpart in `docs/docs/answers/`.

2. **Docs to Root Sync**:
   - If a file in `docs/docs/questions/` is updated, propagate changes to `QUESTIONS.md`.
   - If a file in `docs/docs/answers/` is updated, propagate changes to `answers/*.md`.

3. **Format Conversion**:
   - Ensure level highlighting (Junior, Middle, Senior) is correctly converted:
     - `QUESTIONS.md`: Use bold (e.g., **Junior**).
     - `docs/docs/questions/*.mdx`: Use Docusaurus badges (e.g., `<span className="badge badge--success margin-bottom--md">Junior</span>`).

## Instructions

- Use `Read` to check existing files and ensure you don't overwrite important formatting.
- When creating new question files in `docs/docs/questions/`, use the naming convention `NN-topic-name.mdx`.
- Ensure links are maintained correctly (relative paths in Docusaurus, relative paths from the project root in `QUESTIONS.md`).
- After synchronization, use `ReadLints` if possible to ensure documentation is correctly formatted.
