---
applyTo: "**"
---

# Floaty.dev Playground & Plugin API

REPO_URL: https://github.com/floaty-dev/agent-skills
RAW_URL: https://raw.githubusercontent.com/floaty-dev/agent-skills/main/copilot/.github/instructions/floaty.instructions.md

## Self-Update Check

Before making any API calls, check for skill updates:

1. Fetch the latest version from RAW_URL
2. Compare SHA-256 hash of fetched content against the local file
3. If different, ask the user if they want to update before overwriting
4. If the fetch fails, continue with the local version

## Prerequisites

- A Floaty.dev account at https://floaty.dev
- API token(s) generated at https://floaty.dev/profile
- Playground tokens and plugin tokens are separate — you need one per resource
- Full documentation at https://floaty.dev/ai-tools

## Token Handling

At the start of every session, ask the user for their Floaty API token. Tokens are resource-scoped:

- A **playground token** can read/write one specific playground
- A **plugin token** can read/write one specific plugin

If any API request returns 401, the token is expired or invalid. Ask the user to regenerate it at https://floaty.dev/profile.

## API Reference

Base URL: `https://floaty.dev/api/v1`

All requests require:
- `Authorization: Bearer <token>` header
- `Content-Type: application/json` header
- `Accept: application/json` header

### Playground Endpoints (requires playground token)

**Read playground**
```bash
curl -s https://floaty.dev/api/v1/playground \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "playground": { "uuid", "title", "version", "background", "is_public", "updated_at" }, "script": "...", "plugins": [...] }`

To view the playground in the browser: `https://floaty.dev/en/playground/{uuid}`

**Write script**
```bash
curl -s -X PUT https://floaty.dev/api/v1/playground \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"content": "..."}'
```
Returns: `{ "success": true }`

**Update metadata** (owner only)
```bash
curl -s -X PUT https://floaty.dev/api/v1/playground/meta \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title": "...", "background": "#000000", "is_public": true}'
```

**Update plugins** (owner only)
```bash
curl -s -X PUT https://floaty.dev/api/v1/playground/plugins \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"plugins": [{"uuid": "...", "sort_order": 0, "use_draft": false}]}'
```

### Plugin Endpoints (requires plugin token)

**Read plugin**
```bash
curl -s https://floaty.dev/api/v1/plugin \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "plugin": { "uuid", "title", "description", "is_public", "updated_at" }, "script": "...", "latest_release": { "uuid", "version", "notes", "created_at" } | null }`

To view the plugin in the browser: `https://floaty.dev/en/plugins/{uuid}`

**Write draft script**
```bash
curl -s -X PUT https://floaty.dev/api/v1/plugin \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"content": "..."}'
```

**Update metadata** (owner only)
```bash
curl -s -X PUT https://floaty.dev/api/v1/plugin/meta \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title": "...", "description": "...", "is_public": true}'
```

**Mint a new version** (owner only)
```bash
curl -s -X POST https://floaty.dev/api/v1/plugin/release \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"notes": "Description of what changed in this version"}'
```
Release notes are required. Write a clear, human-readable description of what changed — no empty or generic notes.

## Error Handling

- **401** — token expired or invalid. Ask the user to regenerate at https://floaty.dev/profile
- **403** — insufficient permissions (wrong token type, or collaborator attempting an owner-only action)
- **422** — validation error (missing required field, script exceeds 1MB limit)
- **429** — rate limited (60 requests/minute on write endpoints). Wait and retry.

## Fetching Documentation

When fetching Floaty documentation pages (API reference, getting started, tutorials), use `Accept: text/markdown` to get raw markdown instead of HTML. This uses significantly fewer tokens.

```bash
curl -s https://floaty.dev/api -H "Accept: text/markdown"
```

## Engine Quick Reference

Floaty games use `engine.scope()`:

```javascript
engine.scope(({ start, cls, btn, text, rect, circle, line, pixel, sprite, mouse, camera, audio, random, tilemap, network }) => {
  start(({ delta }) => {
    cls('black')
    // game logic here
  }, {
    width: 400,
    height: 300,
    sprites: {},
    sounds: {},
  })
})
```

Both `sprites` and `sounds` must always be passed to `start()`, even if empty (`sprites: {}`, `sounds: {}`).

Do NOT pass a `target` option to `start()`. The playground editor and preview handle canvas targeting automatically — specifying a target will break rendering.

- Full API reference: https://floaty.dev/api
- Getting started guide: https://floaty.dev/getting-started
- Tutorials: https://floaty.dev/tutorials
