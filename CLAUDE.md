# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

LaTeX templates for arc42 canvases (AIC - Architecture Inception Canvas, ACC - Architecture Communication Canvas). Each canvas has a base template and a hints variant, with content separated by language.

## Build Commands

Build a specific variant (requires `danteev/texlive` or equivalent TeXLive installation):

```bash
pdflatex "\def\contentfile{de}\input{acc-template.tex}"
```

Parameters: canvas `{acc,aic}`, variant `{template,template-hints}`, language `{de,en,plain}`.

Use `-jobname` for CI or batch builds to control output filename:

```bash
pdflatex -interaction=nonstopmode -jobname="acc-template-de" "\def\contentfile{de}\input{acc-template.tex}"
```

Run pdflatex twice per document to resolve cross-references.

## Architecture

Templates use a content-injection pattern: `{acc,aic}-template.tex` and `{acc,aic}-template-hints.tex` define layout and styling, then `\include{content/{acc,aic}-sample-\contentfile.tex}` pulls in language-specific content. The `\contentfile` macro selects the variant — `de`, `en`, or `plain` (blank for custom use).

The hints templates add marker annotations (circled letters via TikZ) to guide workshop participants; they use the same content files as the base templates.

Content files define macros (`\systemname`, `\valuepropositioncontent`, etc.) that the templates consume — adding a new language means adding a new `content/*-sample-{lang}.tex` file pair.

## CI/CD

- **PR workflow** (`.github/workflows/build.yml`): builds all 12 PDF variants in `danteev/texlive` container, uploads as artifact with 1-day retention
- **Release workflow** (`.github/workflows/release.yml`): on merge to main, determines semver bump from conventional commits, builds PDFs in container job, creates GitHub release with PDFs attached from a separate `ubuntu-latest` job (because `gh` CLI is not available in the texlive container)

## Conventions

- Commits follow conventional commits (`feat:`, `fix:`, etc.) — these drive automatic releases
- Templates use `draft` document class option (removes images, shows overfull boxes)
- Babel language in templates is hardcoded to `german` — content language and babel language are independent concerns
