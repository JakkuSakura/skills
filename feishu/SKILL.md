---
name: feishu
description: Use when the user mentions Feishu. Handles Feishu Docs/Drive/Wiki operations in Chrome via Playwright only, preferring headless execution with saved auth state. Supports browsing, reading, summarizing, editing, publishing local files as separate Feishu docs, deterministic template-center creation, and true wiki child-page creation via move-into-wiki flow. If login is stale, switch to QR login in a visible browser.
---

# Feishu

Use this skill whenever the user mentions `Feishu`, a `feishu.cn` URL, Feishu Docs/Drive/Wiki, or asks to browse, search, read, summarize, edit, comment on, import, export, move, or delete Feishu content.

This skill is Playwright-only.
- Assume Chrome.
- Prefer headless by default so work does not interrupt the user's desktop.
- Only use a visible browser when QR login is required or the user explicitly wants to watch.

## Session Defaults

- Saved auth state: `.playwright-cli/feishu-login-state.json`
- Default workspace root for artifacts: the current repo root
- Default execution mode for routine automation: fresh headless browser context

For read-only exploration, reusing an existing browser session is acceptable if it is clearly valid.
For write operations, prefer a fresh browser context with saved auth state instead of reusing old tabs.

Default order:
1. Launch a fresh headless Chrome context.
2. Load `.playwright-cli/feishu-login-state.json`.
3. Navigate to the requested Feishu URL or `https://w00rwzmdhev.feishu.cn/drive/home/`.
4. If the session is stale and Feishu redirects to login, switch to the QR login flow in a visible browser.

## Login Workflow

When login is required:
1. Open a visible Chrome window with a persistent profile.
2. Navigate to `https://w00rwzmdhev.feishu.cn/drive/home/`.
3. Let the user scan the QR code manually.
4. After login succeeds, save auth state back to `.playwright-cli/feishu-login-state.json`.

Detect stale auth by checking for:
- redirects to `accounts.feishu.cn`
- login text such as `Log In With QR Code`
- QR scan prompts

## Operating Pattern

For any Feishu request:
1. Ensure the authenticated session is usable.
2. Navigate to the target URL or workspace area.
3. Prefer stable interactions in this order:
- direct URL navigation
- fresh headless context with saved auth
- semantic selectors
- deterministic keyboard shortcuts
- DOM evaluation inside the page
- normal clicks only as fallback
4. After each meaningful step, verify with:
- URL
- title
- visible text

## Stable New Doc Creation

Use this as the default creation path for blank Feishu documents.

1. Open `https://w00rwzmdhev.feishu.cn/drive/home/` in a fresh headless context.
2. Trigger `Meta+Alt+n`.
3. Wait for a new page whose URL matches `.../docx/...`.
4. On the new page:
- `document.querySelectorAll('[contenteditable=true]')[1]` is the title editor
- `document.querySelectorAll('[contenteditable=true]')[2]` is the body editor
5. Set `innerText`, then dispatch `InputEvent('input', ...)`.
6. Wait until the page shows `Saved to cloud`.
7. Return the new URL.

This flow is the preferred default for deterministic blank-doc creation.

## Drive `New -> Docs`

The current Feishu Drive UI does not treat `New -> Docs` as a direct blank-doc action.

Verified behavior:
1. Open `https://w00rwzmdhev.feishu.cn/drive/home/`.
2. Click the `New` card.
3. Click the `Docs` menu item.
4. Feishu opens a template center overlay in the current page.
5. Choosing a visible `Use` button creates a new page, often under a `wiki/...` URL depending on the selected template.

Implications:
- `Drive -> New -> Docs` is a template-center flow, not a blank-doc flow.
- For deterministic blank-doc creation, keep using `Meta+Alt+n`.
- For deterministic template-based creation from Drive, automate:
  1. `New`
  2. `Docs`
  3. wait for the template center overlay
  4. select the intended template
  5. click its `Use` button
  6. wait for the new page to open

Do not assume the first `Use` button means a blank document. It usually maps to the first visible recommended template.

## Publishing Local Markdown

For bulk publishing local Markdown files to Feishu as separate pages:

1. Read each local Markdown file.
2. Use the first `# Heading` as the Feishu title.
3. Use the rest of the file as plain text body content.
4. Create one new `docx` page per file using `Meta+Alt+n`.
5. Paste body text directly unless the user explicitly asks for formatting fidelity.
6. Verify `Saved to cloud` and collect the resulting URLs.

Known-good result pattern:
- one local file -> one separate `docx` page
- title matches the Markdown heading
- body starts with the expected Markdown content

## Verified Working Pattern

This flow has been verified for publishing multiple local Markdown files as separate Feishu pages:
- one local file -> one separate `docx` page
- title derived from the first Markdown heading
- body written as plain text content
- save confirmed before returning the URL

## True Wiki Child Page Creation

Do not assume that creating a new `docx` page from a wiki page automatically creates a wiki child page.

Verified current behavior:
- `Meta+Alt+n` from both Drive and an open wiki page opens a standalone `docx` page
- the wiki sidebar create menu can open:
  - a template center via `Docs`
  - a move dialog via `Move Docs Here`

Reliable wiki child-page flow:
1. Create a standalone `docx` page first with `Meta+Alt+n`.
2. Write the title and body if needed.
3. Open the target wiki page.
4. In the sidebar tree area, click the visible create trigger on the relevant root or section.
5. Choose `Move Docs Here`.
6. In the move dialog, find the created doc row.
7. Select the row and click `Move`.
8. Verify the title appears in the wiki tree, for example in `.workspace-tree-view-node-content`.

This flow has been verified end to end and should be the default way to create true wiki child pages under automation.

Notes:
- the move dialog supports searching and selecting existing docs
- after a successful move, the page remains on the current wiki page while the tree updates
- verification should check the sidebar tree, not only the main document body

## Destructive Changes

Treat these as high risk:
- delete doc
- delete wiki page or wiki space
- move content between spaces
- bulk destructive edits

Before acting:
1. Restate the exact target.
2. Verify title and URL.
3. Confirm the target still matches at the final confirmation step.

## Response Style

Execute the user's requested Feishu operation instead of stopping at analysis.

Be concise in status updates, but include:
- the current page or target object
- whether auth is valid or QR login is needed
- any destructive-action confirmation gate
- whether the task is using the stable headless `Meta+Alt+n` creation flow
- whether the task is using the template-center flow or the move-into-wiki flow
