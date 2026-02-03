---
name: python-uv
description: Use when creating or updating Python projects that should be managed by uv with the uv_build backend, including pyproject.toml layout, scripts entrypoints, and uv run/tool usage.
---

# Python Uv

## Overview
Use this skill to standardize Python project setup with uv, including `pyproject.toml` fields, uv_build backend, and
CLI entry points. Prefer a `src/` layout for installable packages.

## Workflow
1) Create or update `pyproject.toml`
2) Ensure package layout (`src/`) and entry points
3) Use uv for dependency sync and executable installation

## Project Template
Use the template in `references/pyproject.toml` as the baseline. Update:
- `[project].name`, `version`, and `description`
- `dependencies`
- `[project.scripts]` entry points

## Layout Guidance
- Prefer `src/<package_name>/...` layout
- Ensure `__init__.py` exists in package dirs
- Keep CLI entry points under `src/<package_name>/cli/`

## uv Usage
- Install deps: `uv sync`
- Run without venv: `uv run <script>`
- Install CLI: `uv tool install --editable .`

## References
- `references/pyproject.toml` for the uv/uv_build template
