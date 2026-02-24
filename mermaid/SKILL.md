---
name: mermaid
description: Create, edit, validate, and render Mermaid diagrams (.mmd or Mermaid blocks in Markdown/HTML). Use when asked to convert visuals or specs into Mermaid, fix Mermaid syntax errors, add labels/line breaks, or render SVG/PNG via mmdc or a Mermaid CDN.
---

# Mermaid

## Overview

Convert specs or images into accurate Mermaid diagrams, preserving as many details as possible (labels, casing, punctuation, and annotations), validate syntax, and render SVG/PNG with mmdc (preferred) or CDN-backed HTML when requested.

## Workflow

### 1) Capture the source of truth

- Extract every node, edge, label, and annotation from the input (image, notes, or code); preserve as many details as possible, including exact casing, punctuation, and any callout notes.
- Preserve naming, casing, and arrow direction; note any ambiguous text for follow-up.

### 2) Draft Mermaid accurately

- Pick the diagram type explicitly (e.g., `flowchart LR`, `sequenceDiagram`).
- Use clear node IDs and keep labels faithful to the source, including exact casing and separators.
- Use HTML line breaks in labels: `<br/>`.
- Escape literal pipes in labels as `&#124;`.
- If a labeled reverse arrow causes parse issues, flip the edge direction and keep the label on a forward arrow.
- Add layout hints only when needed (e.g., subgraphs, `direction`, or invisible edges), but never at the expense of label fidelity.

### 3) Validate and render with mmdc (preferred)

- If mmdc needs a Chrome path, create a Puppeteer config like:
  - `{"executablePath": "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"}`
- Render and validate in one step:
  - `mmdc -p /tmp/puppeteer-config.json -i <input.mmd> -o <output.svg>`
- Fix any syntax errors reported by mmdc before delivering output.

### 4) Deliver outputs

- Provide the final `.mmd` and rendered `.svg` (or `.png`) in the requested directory.
- Open the rendered asset in Chrome when asked.
- Only create HTML + CDN when explicitly requested or when mmdc is unavailable.

## Common Fixes

- `|` inside labels: replace with `&#124;`.
- Multi-line labels: use `<br/>` (not `\n`).
- Edge labels: `A -->|label| B`.
- Node labels containing brackets or punctuation: wrap in `"..."` if parsing is unstable.

## Resources

### references/

- `references/mermaid-notes.md`: quick syntax/escaping tips and rendering notes.
