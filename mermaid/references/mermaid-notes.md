# Mermaid Notes

## Labels and Escaping

- Use `<br/>` for line breaks inside node or edge labels.
- Escape literal `|` inside labels as `&#124;`.
- If a label contains brackets or complex punctuation and parsing fails, wrap the label in double quotes.

## Edge Labels

- Preferred form: `A -->|label| B`.
- If a labeled reverse arrow fails to parse, invert the edge direction and keep the label on a forward arrow.

## Rendering with mmdc

- Basic render:
  - `mmdc -i input.mmd -o output.svg`
- With system Chrome:
  - Create `/tmp/puppeteer-config.json`:
    - `{"executablePath": "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"}`
  - Render: `mmdc -p /tmp/puppeteer-config.json -i input.mmd -o output.svg`
