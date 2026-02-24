---
name: youtrack
description: Use when integrating with YouTrack via REST API (authentication, issue search/read/write, pagination, custom fields, and OpenAPI-driven clients).
---

# YouTrack REST API

Use this skill when a task needs real YouTrack data or automation through HTTP APIs.

## Base Rules
- Use YouTrack base URL + `/api` (for example: `https://<instance>/api/...`).
- Authenticate with `Authorization: Bearer <token>`.
- Request only needed fields using the `fields` query parameter.
- Use pagination with `$top` and `$skip`.
- Prefer OpenAPI-first integration from `/api/openapi.json`.

## Auth Workflow
1) Get a permanent token from YouTrack.
2) Store token in env var (for example `YOUTRACK_TOKEN`), never hardcode.
3) Send header:
   - `Authorization: Bearer ${YOUTRACK_TOKEN}`
   - `Accept: application/json`

## Data Fetch Pattern
1) Call endpoint with explicit `fields=...`.
2) Page with `$top` and `$skip` in loops for large sets.
3) For search, use `query=...` where supported (for example issues listing).
4) Keep response payloads small by selecting only required nested fields.

## Example Curl Templates
```bash
# Health check (auth + minimal payload)
curl -sS \
  -H "Authorization: Bearer $YOUTRACK_TOKEN" \
  -H "Accept: application/json" \
  "https://<instance>/api/users/me?fields=id,login,name"
```

```bash
# Issues page with query + fields + pagination
curl -sS \
  -H "Authorization: Bearer $YOUTRACK_TOKEN" \
  -H "Accept: application/json" \
  "https://<instance>/api/issues?query=project:%20ABC&fields=idReadable,summary,updated,project(name)&\$top=50&\$skip=0"
```

## Write/Update Pattern
1) Read target entity first (`id`, version-related fields if needed).
2) Send minimal mutation payload to the matching endpoint.
3) Re-read with `fields=...` and verify changed state.

## OpenAPI Workflow (Recommended)
1) Fetch schema from `https://<instance>/api/openapi.json`.
2) Generate client types/stubs for your language.
3) Wrap generated client with project-specific helpers (auth injection, retries, pagination helpers).

## Safety Checklist
- Token in env/secret manager only.
- No token logging.
- Explicit `fields` in all non-trivial reads.
- Bounded pagination loops and retry policy.
- Validate permissions if 403/404 appears on known entities.

