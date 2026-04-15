# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A **documentation-only GitBook** — a Korean translation and adaptation of [thebrisly/42-guide-website](https://github.com/thebrisly/42-guide-website), covering 42 school curriculum projects from Rank 00 to Rank 06 plus exam prep. There is no application code, no package manifest, and no build/test/lint toolchain. The "source" is the tree of Markdown files, rendered by GitBook.

Because this is a translation/adaptation of someone else's work (see `README.md`), treat substantive content rewrites conservatively — prefer clarifying translations and structural fixes over opinion changes.

## Navigation invariant: `SUMMARY.md`

GitBook builds its sidebar **only** from `SUMMARY.md`. Any new `.md` file you add is invisible in the rendered site until it is linked here. When you:

- **add a new page** → add a corresponding bullet in `SUMMARY.md` under the right rank/section, matching the existing indentation and emoji conventions
- **rename or move a page** → update every `SUMMARY.md` entry that points to it (grep the file — some entries wrap filenames with backslashes, e.g. `ft\_isalpha`)
- **delete a page** → remove its `SUMMARY.md` entry too, otherwise the sidebar link 404s

Section order in `SUMMARY.md` matters — it mirrors the 42 curriculum progression (Rank 00 → 06, then exams).

## GitBook-flavored Markdown

Content files use GitBook extensions that are **not** standard CommonMark. Do not "fix" them:

- YAML frontmatter at the top of pages (`description:`, `layout:` blocks) — drives GitBook page metadata
- `{% hint style="warning" %}…{% endhint %}` / `style="danger"` / `style="info"` — callout boxes
- `{% code title="foo.c" overflow="wrap" lineNumbers="true" %}…{% endcode %}` — code block with metadata
- `{% embed url="…" %}…{% endembed %}` — rich link embeds
- `<details><summary>…</summary>…</details>` — collapsible solution reveals (used heavily in libft pages to hide answers by default)
- Inline color spans: `<mark style="color:green;">…</mark>`

When editing, preserve these exactly. When authoring new pages, match the style of neighboring files in the same rank — each rank has its own emoji conventions (`▪️`, `🗡️`, `📑`, etc.).

## Repository layout (big picture)

- `rank-00/` … `rank-06/` — per-project guides, one folder per 42 project. Each project typically has a `README.md` overview plus topical sub-pages (`understand-*.md`, `core-concepts.md`, `building-the-thing.md`, `functions-used.md`, `commented-solution.md`).
- `exams/` — rank-scoped exam prep. `exam-rank-02/` is broken out by level (`level-1/` … `level-4/`), one file per exercise. `exam-rank-03.md` / `exam-rank-04.md` are single files.
- `useful-tools/` — cross-cutting C/programming topics referenced from many projects (Makefiles, headers, file descriptors, linked lists, C structures, switch statements).
- `minilibx/` — shared graphics-library notes referenced by `so_long`, `fdf`, `miniRT`, etc.
- `.gitbook/assets/` — **all images and static assets live here**. Reference them with relative paths. Do not scatter images into project folders.
- `README.md` — landing page (also the attribution to the original authors; treat carefully).
- `SUMMARY.md` — table of contents (see above).
- `team.md` — author credits.
- `GEMINI.md` — a sibling agent file with a similar intent; keep this `CLAUDE.md` in sync with it when the structural story changes.

## Language and tone

- Primary language: **Korean**. Technical terms, function names, and 42-specific jargon are usually kept in English and annotated in Korean.
- Tone is encouraging and educational — the guide explicitly discourages copy-paste and hides full solutions behind `<details>` reveals. Preserve that philosophy when adding content.

## Gitignored translation helpers

`.gitignore` excludes two local Python scripts used for translation workflow: `gemini_translate.py` and `fix_all.py`. They are **not** part of the repo and should not be committed. If you see them referenced but missing, that's expected.

## There are no build/test commands

No `make`, no `npm`, no CI. To preview changes locally, render with any GitBook-compatible viewer or a Markdown previewer that tolerates the `{% … %}` extensions. Do not invent build steps.
