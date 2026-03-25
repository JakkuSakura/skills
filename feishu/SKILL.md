---
name: feishu
description: Use when the user mentions Feishu. Handles Feishu Docs/Drive/Wiki operations in Chrome via Playwright CLI, including login with QR, reusing saved auth state when possible, and executing read/write tasks such as search, open, summarize, comment, edit, import, export, and other document operations. Treat delete and other irreversible actions as high risk and confirm intent carefully.
---

# Feishu

Use this skill whenever the user mentions `Feishu`, a `feishu.cn` URL, Feishu Docs/Drive/Wiki, or asks to browse, search, read, summarize, edit, comment on, import, export, move, or delete Feishu content.

This skill is Playwright-only.
- Assume Chrome.
- Prefer `npx -y @playwright/cli`.
- Prefer a visible browser for login and any workflow that benefits from user observation.

## Session Defaults

- Session name: `feishu-login`
- Saved auth state: `.playwright-cli/feishu-login-state.json`
- Default workspace root for artifacts: the current repo root

Reuse the existing session if it is open. Otherwise:
1. Open Chrome with a persistent profile.
2. If a saved state file exists, load it.
3. Navigate to the requested Feishu URL or a neutral Feishu Docs entry point.
4. If the session is stale and Feishu redirects to login, switch to QR login flow.

## Login Workflow

Open a visible Chrome window with a persistent profile:

```bash
npx -y @playwright/cli -s=feishu-login open \
  'https://w00rwzmdhev.feishu.cn/drive/home/' \
  --browser=chrome \
  --headed \
  --persistent
```

If a saved state exists, load it before navigation:

```bash
npx -y @playwright/cli -s=feishu-login state-load .playwright-cli/feishu-login-state.json
```

Detect stale auth by checking whether the page lands on `accounts.feishu.cn` or shows login text such as:
- `Log In With QR Code`
- `Scan the QR code`
- `Switch to Lark to log in`

If login is required:
1. Leave the visible browser open.
2. Let the user scan the QR code manually.
3. After the user confirms login, verify with:

```bash
npx -y @playwright/cli -s=feishu-login eval "location.href"
npx -y @playwright/cli -s=feishu-login eval "document.title"
```

4. Save auth state:

```bash
npx -y @playwright/cli -s=feishu-login state-save .playwright-cli/feishu-login-state.json
```

## Operating Pattern

For any Feishu request:
1. Ensure the authenticated session is usable.
2. Navigate to the target URL or workspace area.
3. Capture a snapshot before interacting:

```bash
npx -y @playwright/cli -s=feishu-login snapshot
```

4. Prefer stable interactions in this order:
- direct URL navigation
- semantic `run-code` selectors
- `click` with snapshot refs
- text evaluation with `eval`

5. After each meaningful step, verify the new page state with:
- `eval "location.href"`
- `eval "document.title"`
- `eval "document.body.innerText.slice(0,N)"`

6. Save artifacts only when they help with continuity:
- snapshots for navigation-heavy flows
- storage state after login refresh

## Common Tasks

### Search / Browse

- Open Drive home: `https://<tenant>.feishu.cn/drive/home/`
- Open Wiki: `https://<tenant>.feishu.cn/wiki/`
- Use page text extraction to enumerate visible docs or spaces.
- Use snapshots to identify clickable refs.

### View / Summarize

- Open the doc or wiki page.
- Extract title, URL, visible headings, and key body text.
- Summarize from the rendered page, not from assumptions.
- If content is lazy-loaded, scroll or use tab/section navigation first.

### Edit / Comment / Write

- Confirm the target page and editing context before changing anything.
- Prefer opening the exact document first, then interacting with visible editor controls.
- After edits, verify the new visible content or comment thread text.
- For text-heavy edits, prefer deterministic `fill`, `type`, or `run-code` actions over brittle mouse automation.

### Import / Export

- Import: navigate to the relevant Upload or Import control, then use `upload`.
- Export: use the page menus and download/export controls exposed in the UI.
- Verify the action started or completed from visible UI feedback.

### Delete / Move / Other Destructive Changes

Treat these as high risk:
- delete doc
- delete wiki page or wiki space
- move content between spaces
- clear comments or permissions
- bulk actions

Rules:
- Require explicit user intent for the exact target.
- Restate the target object before acting.
- Prefer opening the target page first and verifying title/URL/owner.
- If the UI presents a final destructive confirmation dialog, stop and make sure the intended target still matches.
- After the action, verify the result immediately.

## Playwright CLI Patterns

Open visible Chrome:

```bash
npx -y @playwright/cli -s=feishu-login open --browser=chrome --headed --persistent
```

Navigate:

```bash
npx -y @playwright/cli -s=feishu-login goto 'https://w00rwzmdhev.feishu.cn/drive/home/'
```

Read page state:

```bash
npx -y @playwright/cli -s=feishu-login eval "location.href"
npx -y @playwright/cli -s=feishu-login eval "document.title"
npx -y @playwright/cli -s=feishu-login eval "document.body.innerText.slice(0,2500)"
```

Take snapshot:

```bash
npx -y @playwright/cli -s=feishu-login snapshot
```

Use robust selectors:

```bash
npx -y @playwright/cli -s=feishu-login run-code "async page => {
  await page.locator('[data-e2e=main-menu-wiki]').first().click({ force: true });
}"
```

List/select tabs:

```bash
npx -y @playwright/cli -s=feishu-login tab-list
npx -y @playwright/cli -s=feishu-login tab-select 1
```

## Feishu UI Notes

- Feishu Drive uses virtualized lists. Text nodes often are not the correct click target.
- If normal `click` fails, use `run-code` with row-level or `data-*` selectors and `force: true` when justified.
- Feishu often opens wiki spaces or docs in new tabs. Always check `tab-list` after navigation.
- Console warnings are common and usually low-signal. Focus on URL, title, and rendered content.

## Response Style

Execute the user's requested Feishu operation instead of stopping at analysis.
- Browse when they ask to explore.
- Open and summarize when they ask to read.
- Search when they ask to find.
- Edit/comment/import/export when they ask to change content.

Be concise in status updates, but include:
- the current page or target object
- whether auth is valid or QR login is needed
- any destructive-action confirmation gate
