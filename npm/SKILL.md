---
name: npm
description: Use when handling JavaScript package management and scripts, with pnpm as the default package manager.
---

# npm (Use pnpm by default)

Use this skill for JavaScript/TypeScript dependency and script workflows.

## Core Rules
- Prefer `pnpm` over `npm` for install, run, add, remove, and global tool actions.
- Only use `npm` if the user explicitly requires it.
- Keep lockfile/package-manager usage consistent in the same workspace.
- Avoid mixing package managers in one project unless migration is intentional.

## Command Mapping
- `npm install` → `pnpm install`
- `npm i <pkg>` → `pnpm add <pkg>`
- `npm uninstall <pkg>` → `pnpm remove <pkg>`
- `npm run <script>` → `pnpm <script>` (or `pnpm run <script>`)
- `npm i -g <pkg>` → `pnpm add -g <pkg>`
- `npx <cmd>` → `pnpm dlx <cmd>`

## Verification Checklist
- `pnpm` is available and version is confirmed.
- Commands executed with `pnpm` unless explicitly overridden.
- No unintended `npm` lockfile/tooling drift introduced.

