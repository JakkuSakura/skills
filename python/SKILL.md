---
name: python
description: Use when writing or refactoring Python code under strict style constraints that require explicit field access, no reflection-style checks, no implicit defaults, and minimal exception handling.
---

# Python (Strict)

Use this skill when the user wants strict, explicit Python code with low ambiguity and fail-fast behavior.

## Core Rules
- Never use `getattr` or `hasattr`.
- Never use `isinstance` type checks.
- Do not introduce implicit defaults for required data paths.
- Avoid unnecessary `try/except`; only keep exception handling for a clearly required fallback or boundary.

## Implementation Pattern
1) Validate required keys/attributes explicitly.
2) Use direct indexing/attribute access for required fields.
3) For optional branches, use explicit `if "key" in obj` checks.
4) Prefer fail-fast errors over silent fallback behavior.
5) Keep control flow straightforward; avoid defensive swallowing of errors.

## Review Checklist
- No reflective access helpers (`getattr`, `hasattr`).
- No runtime type filtering with `isinstance`.
- No hidden default values for required fields.
- Exception blocks are minimal and justified.
- Errors are explicit and actionable.

## References
- `references/patterns.md` for quick before/after rewrite patterns.
