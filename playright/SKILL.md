---
name: playright
description: Use when validating web UI runtime behavior with browser automation, including JavaScript errors, console logs, and rendered DOM checks.
---

# Playright

Use this skill when a user asks to verify that a web UI actually works in a browser (not just build-time checks).

## Core Rules
- Prefer real browser runtime checks over static assumptions.
- Capture `pageerror` and console `error` output before reporting success.
- Assert key DOM elements after load.
- Verify HTTP status and basic route health.
- Report exact runtime failures and repro context.

## Implementation Pattern
1) Launch headless browser.
2) Open target URL with timeout and `networkidle`.
3) Collect `pageerror` and console error events.
4) Assert critical UI selectors exist.
5) Return a compact JSON-style result summary.

## Verification Checklist
- Page responds with HTTP `200`.
- Browser title is as expected.
- Main UI landmarks are rendered.
- No unhandled JS runtime errors.
- No console error events (or errors are explicitly listed).

