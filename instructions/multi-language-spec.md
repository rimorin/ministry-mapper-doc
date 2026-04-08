# Multi-Language Support — Specification Document

## Overview

This document describes the specification for adding multi-language (i18n) support to the Ministry Mapper documentation site, built with MkDocs and the Material theme.

---

## Goals

- Enable the documentation site to be read in 7 languages
- Provide a language switcher in the site header
- Allow untranslated pages to gracefully fall back to English
- Keep the infrastructure maintainable and extensible for future languages

---

## Target Languages

| # | Language           | Locale | Script Direction | Folder       |
|---|--------------------|--------|------------------|--------------|
| 1 | English            | `en`   | LTR (default)    | `docs/en/`   |
| 2 | Chinese Simplified | `zh`   | LTR              | `docs/zh/`   |
| 3 | Indonesian         | `id`   | LTR              | `docs/id/`   |
| 4 | Malay              | `ms`   | LTR              | `docs/ms/`   |
| 5 | Japanese           | `ja`   | LTR              | `docs/ja/`   |
| 6 | Korean             | `ko`   | LTR              | `docs/ko/`   |
| 7 | Spanish            | `es`   | LTR              | `docs/es/`   |

---

## Technical Approach

### Plugin: `mkdocs-static-i18n`

- **Version:** 1.3.1
- **Installation:** `pip install mkdocs-static-i18n` (into the `mkdocs/` venv)
- **Docs structure:** `folder` — one subfolder per language under `docs/`
- **Fallback:** `fallback_to_default: true` — any page not yet translated falls back to English silently (no broken links)

### Theme: Material for MkDocs

- `theme.language: en` sets English as the default UI language
- `extra.alternate` powers the language picker in the header
- UI elements (buttons, labels, search, navigation) are translated automatically by the Material theme's built-in locale support
- `navigation.instant` was **removed** — it is incompatible with the i18n language switcher contextual links

---

## Directory Structure

```
docs/
├── assets/               ← Shared assets (images, icons) — not language-specific
├── en/                   ← English (source of truth, 13 files)
│   ├── index.md
│   ├── getting-started.md
│   ├── features.md
│   ├── user-guide.md
│   ├── architecture.md
│   ├── data-models.md
│   ├── deployment.md
│   ├── self-hosting.md
│   ├── backend-setup.md
│   ├── frontend-setup.md
│   ├── faq.md
│   ├── privacy-policy.md
│   └── terms-of-service.md
├── zh/                   ← Chinese Simplified (13 translated files)
├── id/                   ← Indonesian (13 translated files)
├── ms/                   ← Malay (13 translated files)
├── ja/                   ← Japanese (13 translated files)
├── ko/                   ← Korean (13 translated files)
└── es/                   ← Spanish (13 translated files)
```

---

## `mkdocs.yml` Configuration

See the live `mkdocs.yml` at the repository root for the full configuration. Key points:

- `plugins.i18n.docs_structure: folder` with `fallback_to_default: true`
- All 7 locales listed under `plugins.i18n.languages`
- `extra.alternate` powers the language picker (one entry per locale)
- All `nav` entries use the `en/` prefix (e.g. `en/index.md`)

---

## Content Translation

### Scope

All 13 `.md` files in `docs/en/` are translated into each of the 6 non-English languages:

| File                  | Lines |
|-----------------------|-------|
| user-guide.md         | 2100  |
| features.md           | 945   |
| frontend-setup.md     | 811   |
| deployment.md         | 781   |
| data-models.md        | 679   |
| backend-setup.md      | 569   |
| self-hosting.md       | 610   |
| faq.md                | 512   |
| architecture.md       | 441   |
| index.md              | 165   |
| getting-started.md    | 94    |
| terms-of-service.md   | 60    |
| privacy-policy.md     | 53    |
| **Total**             | **7820** |

### Translation rules

1. Translate all human-readable text
2. **Preserve** markdown formatting (headers, lists, tables, admonitions, bold, italic)
3. **Preserve** all code blocks verbatim — do not translate code
4. **Preserve** all URLs, file paths, and CLI commands
5. **Preserve** product names: Ministry Mapper, Supabase, Firebase, Docker, etc.
6. **Preserve** version numbers and technical identifiers

---

## Implementation Status

| Task                              | Status   |
|-----------------------------------|----------|
| Install `mkdocs-static-i18n`      | ✅ Done  |
| Migrate `docs/*.md` → `docs/en/`  | ✅ Done  |
| Create stub folders per language  | ✅ Done  |
| Update `mkdocs.yml`               | ✅ Done  |
| Validate `mkdocs build`           | ✅ Done  |
| Translate content → zh            | ✅ Done |
| Translate content → id            | ✅ Done |
| Translate content → ms            | ✅ Done |
| Translate content → ja            | ✅ Done |
| Translate content → ko            | ✅ Done |
| Translate content → es            | ✅ Done |

---

## Known Issues

- `en/architecture.md` contains a link to `security.md` which does not exist — this is a **pre-existing issue** unrelated to the i18n implementation.
- `id` and `ms` locales are not supported by lunr.js search indexing — search in those languages will use the default index. This is a known limitation of the search plugin.
