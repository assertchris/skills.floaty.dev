# API Reference

Base URL: `https://floaty.dev/api/v1`

All requests require:
- `Authorization: Bearer <token>` header
- `Content-Type: application/json` header
- `Accept: application/json` header

## Playground Endpoints (requires playground token)

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

## Plugin Endpoints (requires plugin token)

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
