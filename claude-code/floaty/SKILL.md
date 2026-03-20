---
name: floaty
description: Read and write Floaty.dev playground and plugin scripts via the API. Requires Floaty API tokens.
allowed-tools: Bash(curl *), Read, Write, Edit, Glob, Grep, AskUserQuestion
user-invocable: true
---

# Floaty.dev Playground & Plugin API

REPO_URL: https://github.com/assertchris/skills.floaty.dev
RAW_URL: https://raw.githubusercontent.com/assertchris/skills.floaty.dev/main/claude-code/floaty/SKILL.md

## Self-Update Check

Before making any API calls, check for skill updates:

1. Fetch the latest version: `curl -s $RAW_URL`
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

## Additional Resources

- For full API endpoint details (curl examples, request/response formats), see [api-reference.md](api-reference.md)
- For the Floaty engine game loop pattern and docs links, see [engine-reference.md](engine-reference.md)
