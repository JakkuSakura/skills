---
name: macos-gui-automation
description: Use Peekaboo on macOS for GUI-level automation (screenshots, UI inspection, clicks/typing, window/app control). Trigger this skill when tasks require real GUI interaction, screenshot automation, or browser-level automation on macOS.
---

# macOS GUI Automation (Peekaboo)

## Overview

This skill guides reliable macOS GUI automation using Peekaboo (CLI/MCP). Use it for screenshot-driven UI validation, input automation, app/window control, and browser-level tests when headless automation is insufficient.

## When to Use

- GUI-level tests that require real app rendering and user input.
- Browser automation with visual verification or non-DOM interactions.
- Capturing screenshots or inspecting UI elements for automation logic.
- Running end-to-end tests that require Accessibility or Screen Recording APIs.

## Preconditions

- macOS 15+ with Screen Recording and Accessibility permissions granted.
- Peekaboo installed (Homebrew or `npx`). If using `npx`, Node 22+ is required.

## Installation

Prefer Homebrew for persistent use:

```bash
brew install steipete/tap/peekaboo
```

For one-off usage:

```bash
npx -y @steipete/peekaboo
```

## Core Workflow

1. Verify permissions.
2. Capture or inspect the UI to locate targets.
3. Perform actions (click, type, hotkey, scroll).
4. Capture screenshots for verification.
5. Repeat for the next step in the flow.

## Command Patterns

Check permissions:

```bash
peekaboo permissions status
peekaboo list permissions
# If using an older build, try:
# peekaboo permissions check
```

Request permissions (if needed):

```bash
peekaboo permissions grant screen-recording
peekaboo permissions grant accessibility
# If using an older build, try:
# peekaboo permissions request screen-recording
# peekaboo permissions request accessibility
```

See the screen and identify elements (capture + snapshot id):

```bash
peekaboo see --json-output
```

Click by text using a snapshot id:

```bash
peekaboo click --on "OK" --snapshot "<snapshot_id>"
```

Example snapshot capture pipeline:

```bash
SNAPSHOT=$(peekaboo see --app Safari --json-output | jq -r '.data.snapshot_id')
peekaboo click --on "Reload this page" --snapshot "$SNAPSHOT"
```

Type text:

```bash
peekaboo type "hello world"
```

Send hotkeys:

```bash
peekaboo hotkey cmd,shift,4
```

Scroll:

```bash
peekaboo scroll down
```

Target a specific app or window:

```bash
peekaboo app list
peekaboo window list
```

Capture a screenshot:

```bash
peekaboo image
```

Run a natural-language automation:

```bash
peekaboo "Open Notes and create a TODO list with three items"
```

## Reliability Tips

- Use `peekaboo see` to confirm the current UI state before clicking.
- Prefer text-based clicks for stability; fall back to coordinates only when required.
- Capture a screenshot after every meaningful step for test evidence.
- Keep automation steps small and re-check state after each interaction.

## Troubleshooting

- If `see` or `image` returns empty output, re-check Screen Recording permission.
- If `click` or `type` fails, re-check Accessibility permission.
- Use the permissions commands to verify the system state before proceeding.
