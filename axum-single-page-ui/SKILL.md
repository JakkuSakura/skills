---
name: axum-single-page-ui
description: Build and serve a single-page web UI from an Axum service, including plain-JS mode and optional TypeScript build/fallback patterns.
metadata:
  short-description: Axum single-page UI workflow
---

# Axum Single Page UI

Use this skill when the user wants a single-page UI served by `axum` from the same Rust service as API routes.

## When to use

- Axum backend needs to serve one UI page at `/`
- UI and API are in one service (`/api/*` + browser UI)
- User wants plain JavaScript first, with optional TypeScript path later
- User asks for runtime/browser transpile trade-offs or fallback design

## Defaults

- Prefer **plain JS + single HTML** for fastest, lowest-risk delivery.
- Keep API unchanged; only adjust UI asset serving and frontend script.
- Use TypeScript only when user explicitly asks for it.
- For production reliability, prefer build-time compilation over runtime transpilation.

## Canonical architecture

1. `axum` routes:
   - `GET /health`
   - `GET /api/...` for JSON APIs
   - `GET /` for SPA shell
2. Serve static frontend assets with `tower-http` (`ServeDir`/`ServeFile`) or embed via `include_str!`/`include_bytes!`.
3. Keep frontend as single page (one `index.html`) with client-side rendering.

## Implementation patterns

### Pattern A: Plain JS single-file page (default)

Use when speed and operability matter more than tooling.

- One HTML page with inline `<script type="module">`
- Optional CDN dependencies (e.g., SolidJS, Tailwind)
- Axum returns the page directly or as embedded static asset

Pros:
- Zero frontend toolchain
- Easiest deploy/debug

Cons:
- No TS type checking
- Harder to scale frontend complexity

### Pattern B: TS build + Axum static serving

Use when frontend complexity grows.

- Author UI in `tsx/ts`
- Build to static assets (`index.html`, `app.js`, `app.css`)
- Axum serves built assets

Pros:
- Type safety and better maintainability
- Better performance/caching

Cons:
- Adds build pipeline

### Pattern C: Build-first + runtime transpile fallback (opt-in)

Use only when explicitly requested.

- Primary: prebuilt bundle from build/deploy
- Fallback: runtime/browser transpile path (feature-flagged)

Rules:
- Never enable fallback silently in production
- Gate fallback with explicit env flag (e.g., `UI_TRANSPILE_FALLBACK=1`)
- Add visible UI banner when fallback mode is active

## Route structure guidance

- Keep API and UI concerns separate:
  - `Router::new().nest("/api", api_router)`
  - UI fallback for `/` and frontend routes
- Do not let SPA fallback intercept `/api/*`
- Keep `/health` independent and always fast

## Frontend data contract guidance

When table/count mismatch appears (e.g., count shows rows but table is empty):

1. Verify API payload shape matches renderer field names.
2. Confirm rendering loops receive arrays (`Array.isArray(payload)`).
3. Validate filter/query param names on both sides.
4. Check runtime JS errors in browser console before patching backend.
5. Prefer robust field fallback mapping in UI (`currency || symbol`, etc.).

## Deployment guidance

When a package has `deployment.yaml`, deploy with:

- `infra deploy --package <app>`

After deploy:

- Check rollout status
- Check `/health`
- Check one `/api/...` endpoint and `/` page rendering

## Output checklist for Codex

For implementation tasks, return:

1. Files changed and why
2. Commands run for verify/deploy
3. API/UI smoke-check results
4. Any fallback flags or operational caveats
