# API Reference

Base URL: `https://floaty.dev/api/v1`

All requests require:
- `Authorization: Bearer <token>` header
- `Content-Type: application/json` header
- `Accept: application/json` header

## Workflow: Editing an Existing Playground

When you read a playground that already has game content and plugins attached, read each attached plugin's script before making changes. Plugins expose helper functions, constants, and behaviours that the game script depends on.

1. `GET /api/v1/playground` — read the playground script and see which plugins are attached
2. For each plugin in the `plugins` array, `GET /api/v1/playground/plugins/{uuid}` — this returns the script the playground actually runs (automatically resolves draft vs. pinned release)
3. Now you understand the full context — write your changes with `PUT /api/v1/playground`

**Comparing plugin versions:** If you need to debug a plugin issue or compare what changed between versions, use `GET /api/v1/playground/plugins/{uuid}/releases/{releaseUuid}` to read a specific release's script. The plugin detail response includes a `releases` array with all available versions.

**Finding plugins:** To browse available public plugins, direct the user to https://floaty.dev/en/plugins. There is no plugin search API.

## Playground Endpoints (requires playground token)

**Read playground**
```bash
curl -s https://floaty.dev/api/v1/playground \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "playground": { "uuid", "title", "engine_version", "background", "is_public", "updated_at" }, "script": "...", "plugins": [{ "uuid", "title", "sort_order", "use_draft", "plugin_release_id" }, ...] }`

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

**Read attached plugin** (owner + collaborators)
```bash
curl -s https://floaty.dev/api/v1/playground/plugins/PLUGIN_UUID \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "plugin": { "uuid", "title", "description", "is_public", "use_draft", "plugin_release_id" }, "script": "...", "releases": [...] }`

The `script` field returns the draft script if `use_draft` is true, or the pinned release script otherwise. Use this to understand what a plugin does without needing a separate plugin token.

**Read attached plugin release** (owner + collaborators)
```bash
curl -s https://floaty.dev/api/v1/playground/plugins/PLUGIN_UUID/releases/RELEASE_UUID \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "release": { "uuid", "version", "notes", "created_at" }, "script": "..." }`

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

**Create a new version** (owner only)
```bash
curl -s -X POST https://floaty.dev/api/v1/plugin/release \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"notes": "Description of what changed in this version"}'
```
Release notes are required. Write a clear, human-readable description of what changed — no empty or generic notes.

**List releases** (owner only)
```bash
curl -s https://floaty.dev/api/v1/plugin/releases \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "releases": [{ "uuid", "version", "notes", "created_at" }, ...] }` (ordered by version, newest first)

**Read a specific release** (owner only)
```bash
curl -s https://floaty.dev/api/v1/plugin/releases/RELEASE_UUID \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```
Returns: `{ "release": { "uuid", "version", "notes", "created_at" }, "script": "..." }`
