---
name: gui-automation
description: Use when Codex is asked to automate local GUI workflows with SakuraProj using skpr-local/skpr-cli scripts, including probe-driven readiness and repeatable local task orchestration.
---

# GUI Automation (SakuraProj)

## Overview

Automate local GUI workflows by treating `skpr-local` as the default entrypoint.
Keep business logic in `skpr-cli` and runtime hosting in `skprd`; use this skill to
compose reliable local automation commands and scripts.

## Default Architecture

- Prefer `skpr-local` for local task orchestration.
- Keep `skpr-local` thin: bootstrap daemon, readiness probe, delegate to `skpr-cli`.
- Do not re-implement client or daemon semantics inside automation wrappers.

## Command Patterns

Use these patterns first, then adapt args:

```bash
# Run a local interactive session
skpr-local -- display 0

# Run a local headless probe (CI-friendly smoke check)
skpr-local -- --backend headless probe

# Override daemon backend while preserving CLI pass-through
skpr-local --skprd-arg=--backend --skprd-arg=mock -- --backend headless probe

# Connect to a custom local endpoint
skpr-local --endpoint quic://127.0.0.1:48220 -- --backend headless probe
```

## Workflow

1) Pick task intent: interactive viewing, attach/open, or headless probe.
2) Encode daemon-only options via `--skprd-arg`.
3) Pass client behavior after `--` unchanged to `skpr-cli`.
4) For automation, prefer `probe` checks before longer GUI actions.

## Guardrails

- Keep wrapper logic minimal and reversible.
- Prefer deterministic commands and explicit endpoints for scripted runs.
- For flaky startup environments, increase wrapper startup timeout/poll values.
- If a workflow needs heavy branching or state machines, move logic to dedicated
  scripts rather than inflating wrapper behavior.
