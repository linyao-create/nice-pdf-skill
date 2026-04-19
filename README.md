# nice-pdf

A Claude Code skill that converts Markdown or text content into beautiful, print-ready HTML files. Open in browser and export as PDF directly.

## Install

```bash
claude skill install github:linyao-create/nice-pdf-skill
```

## Usage

Just tell Claude:

> 帮我把这个 MD 文件做成 PDF

> /nice-pdf [file path]

Claude will:
1. Read and analyze your content
2. Plan page layout (estimate block heights to avoid excessive whitespace)
3. Generate a styled HTML file
4. Tell you how to export as PDF from the browser

## Features

- Professional typography — Playfair Display + DM Sans + Crimson Pro
- Warm color palette, clean layout
- Smart pagination — calculates content height before splitting pages
- Print-ready CSS — colors preserved, no content cut off at page breaks
- Common components: module cards, flow diagrams, tables, pricing tables

## Export as PDF

1. Open the generated HTML in Chrome / Safari
2. `Cmd + P` → Print
3. Check **Background graphics**
4. Save as PDF

## Requirements

- [Claude Code](https://claude.ai/code)
