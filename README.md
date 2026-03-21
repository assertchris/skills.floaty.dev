# Floaty.dev Agent Skills

Build pixel games on [Floaty.dev](https://floaty.dev) using your AI coding assistant. This repo contains skillset files for Claude Code, Cursor, and GitHub Copilot that teach your agent how to read and write playground and plugin scripts via the Floaty API.

## How It Works

1. Generate a token at https://floaty.dev/profile (one token per playground or plugin)
2. Install the skillset for your AI agent (see below)
3. Give the agent your token and start building

Tokens are short-lived (12, 24, or 48 hours) and scoped to a single resource. Playground tokens let agents read/write scripts for that playground. Plugin tokens let agents read/write plugin drafts and mint versions.

## Generating Tokens

1. Log in at https://floaty.dev and go to your [profile](https://floaty.dev/profile)
2. Scroll to **API Tokens** and click **New Token**
3. Select **Playground** or **Plugin** and pick your resource (or create a new one)
4. Choose an expiry (12h, 24h, or 48h) and click **Generate Token**
5. Copy the token — you can copy it again anytime from the token list

## API Reference

Base URL: `https://floaty.dev/api/v1`

All requests require `Authorization: Bearer <token>`, `Content-Type: application/json`, and `Accept: application/json` headers.

### Playground Endpoints

| Method | Path | Description | Access |
|--------|------|-------------|--------|
| GET | `/api/v1/playground` | Read script, metadata, plugins | Owner + collaborators |
| PUT | `/api/v1/playground` | Write script | Owner + collaborators |
| PUT | `/api/v1/playground/meta` | Update title, background, visibility | Owner only |
| PUT | `/api/v1/playground/plugins` | Update plugin associations | Owner only |
| GET | `/api/v1/playground/plugins/{uuid}` | Read attached plugin script + releases | Owner + collaborators |
| GET | `/api/v1/playground/plugins/{uuid}/releases/{releaseUuid}` | Read specific release of attached plugin | Owner + collaborators |

### Plugin Endpoints

| Method | Path | Description | Access |
|--------|------|-------------|--------|
| GET | `/api/v1/plugin` | Read draft script, metadata, latest release | Owner only |
| PUT | `/api/v1/plugin` | Write draft script | Owner only |
| PUT | `/api/v1/plugin/meta` | Update title, description, visibility | Owner only |
| POST | `/api/v1/plugin/release` | Create new version from current draft | Owner only |
| GET | `/api/v1/plugin/releases` | List all releases | Owner only |
| GET | `/api/v1/plugin/releases/{uuid}` | Read a specific release script | Owner only |

### Error Codes

| Code | Meaning |
|------|---------|
| 401 | Token expired or invalid — regenerate at https://floaty.dev/profile |
| 403 | Wrong token type or insufficient permissions |
| 422 | Validation error (missing field, script > 1MB) |
| 429 | Rate limited (60/min on write endpoints) — wait and retry |

## Installation

### Claude Code

Copy the skill files into your project:

```bash
BASE=https://raw.githubusercontent.com/floaty-dev/agent-skills/main/claude-code/floaty
for f in SKILL.md api-reference.md engine-reference.md; do
  curl -sL "$BASE/$f" --create-dirs -o ".claude/skills/floaty/$f"
done
```

Then use it with `/floaty` in Claude Code.

### Cursor

Copy the rules file into your project:

```bash
curl -sL https://raw.githubusercontent.com/floaty-dev/agent-skills/main/cursor/.cursor/rules/floaty.mdc \
  --create-dirs -o .cursor/rules/floaty.mdc
```

### GitHub Copilot

Copy the instructions file into your project:

```bash
curl -sL https://raw.githubusercontent.com/floaty-dev/agent-skills/main/copilot/.github/instructions/floaty.instructions.md \
  --create-dirs -o .github/instructions/floaty.instructions.md
```

## Prompting Your Agent

Example prompts to get started:

- "Read the current playground script and explain what it does"
- "Add keyboard controls to move the player with arrow keys"
- "Create a simple platformer with gravity and jumping"
- "Read the plugin script and add a health bar helper"

The agent will use the API to read the current script, make changes, and write it back. You can preview the result in your browser:

- Editor: `https://floaty.dev/en/playground/{uuid}`
- Preview (full screen): `https://floaty.dev/en/playground/{uuid}/preview`

You can ask your agent for the UUID — it's included in the API response when reading a playground.

> **Note:** The playground editor is not multi-user. If you have the editor open while an agent writes to the same playground via the API, clicking Run will save the version you see in the editor — overwriting whatever the agent has changed since you opened it. Use the full-screen preview URL instead when working with an agent.

## Checking for Updates

Each skill file includes a self-update check that runs at the start of every session. It compares the local file against the latest version in this repo and asks before making any changes.

To manually update, re-run the curl install command for your agent.

## Documentation

- [AI tools documentation](https://floaty.dev/ai-tools)
- [Engine API reference](https://floaty.dev/api)
- [Getting started guide](https://floaty.dev/getting-started)
- [Tutorials](https://floaty.dev/tutorials)
