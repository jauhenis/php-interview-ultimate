# Repository instructions for Codex

Follow these rules for every task in this repository. Apply each content or formatting requirement whenever the task touches the files it covers.

## Keep all versions in sync

- Use `QUESTIONS.md` for questions and concise answers, and `answers/` for detailed guides. Mirror them in `docs/docs/questions/` and `docs/docs/answers/`, respectively. When an edit starts in Docusaurus, update the corresponding root content in the same change.
- Apply additions, edits, deletions, and link changes to English and Russian together. Maintain the Russian Markdown under `translate/ru/` and the Russian site content under `docs/i18n/ru/`. Keep content, topic hierarchy, difficulty levels, section numbering, and corresponding filenames aligned.
- Update `README.md` whenever you introduce a topic or add a section.
- Write idiomatic, professional Russian at C1 level. Use accurate terminology familiar to Russian-speaking developers, including terms such as «деплоить» and «коммитить» where natural. Keep explanations accessible to Junior through Senior readers.

## Format questions and answers

- Preserve the existing topic categories. In `QUESTIONS.md` and `answers/**/*.md`, use the established difficulty levels. For a question entry, use a level-three heading, a bold level (`**Junior**`, `**Middle**`, or `**Senior**`), and an answer beginning with `**Answer:**`. Separate questions with `---`.
- Link each question that has a detailed guide to its file. Use `[Detailed Guide](answers/topic.md)` in `QUESTIONS.md` and `[Detailed Guide](../answers/topic.md)` on Docusaurus question pages.
- In Docusaurus Markdown and MDX, preserve the question heading, answer label, and separator. In `docs/docs/questions/*.mdx`, put the matching badge immediately below the question heading. Mirror the format in the Russian site.

  - Junior uses `<span className="badge badge--success margin-bottom--md">Junior</span>`.
  - Middle uses `<span className="badge badge--primary margin-bottom--md">Middle</span>`.
  - Senior uses `<span className="badge badge--warning margin-bottom--md">Senior</span>`.

## Verify links after structural changes

When renaming a file, reordering sections, renumbering categories, or adding or removing a category, complete all of these checks before finishing.

1. Search the entire repository for references to each affected filename and heading anchor. Update incoming links and check relative paths, including links in `answers/`, the English site, and the Russian site.
2. Update the tables of contents in both `QUESTIONS.md` and `translate/ru/QUESTIONS.md`. Verify that each changed fragment matches the anchor generated from the destination heading in its own language.
3. Search again for each obsolete filename and anchor, for example with `rg -n -F -- 'old-filename.mdx'`. Resolve any remaining stale references in both languages.

## Run project commands

Use the Docker Compose `node` container for documentation tasks. Run the root npm scripts inside it, for example `docker-compose exec node npm run build`.

- `npm run install:docs` installs the Docusaurus dependencies.
- `npm run dev` starts the documentation development server.
- `npm run build` produces the production site.
- `npm run start` serves the built site.
- `npm run format` formats the code.
