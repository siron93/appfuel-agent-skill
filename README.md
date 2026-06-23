# App Fuel Agent Skill

Public agent instructions for using App Fuel paid ad and organic reel intelligence from Codex, Claude Code, Cursor, Windsurf, and other agent clients.

Recommended repository name:

```text
appfuel-agent-skill
```

## What This Gives Agents

- How to search App Fuel paid Meta ads and organic Instagram Reels.
- When to use `query` versus structured filters.
- How to return App Fuel gallery links for human review.
- How to avoid exposing internal/admin App Fuel surfaces.

## Install

First create an App Fuel API key in the App Fuel app under `/elite/api`.

For the production App Fuel API:

```bash
python3 -c "$(curl -fsSL https://api.theappfuel.com/v1/elite/mcp/install.py)" \
  --api-base https://api.theappfuel.com/v1/elite \
  --api-key <account-api-key>
```

This installs the local App Fuel MCP adapter and, for Codex users, can also install the `appfuel-data` skill.

## Install From GitHub

After this repo is published, install the skill folder from:

```text
https://github.com/<owner>/appfuel-agent-skill/tree/main/appfuel-data
```

In Codex, ask:

```text
Use $skill-installer to install appfuel-data from https://github.com/<owner>/appfuel-agent-skill/tree/main/appfuel-data
```

Manual install:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R appfuel-data "${CODEX_HOME:-$HOME/.codex}/skills/appfuel-data"
```

## Agent Handoff

Paste this into an agent after the MCP connector is configured:

```text
Use App Fuel as an agent data source for paid ads and organic reel intelligence.

Agent instructions: https://api.theappfuel.com/v1/elite/agent/instructions.md/
OpenAPI spec: https://api.theappfuel.com/v1/elite/agent/openapi.json/
Agent schema: https://api.theappfuel.com/v1/elite/agent/schema/

Before answering App Fuel questions:
1. Read the agent instructions URL.
2. Use the OpenAPI/schema URLs to discover current endpoints and filters.
3. If App Fuel MCP tools are available, use them instead of raw HTTP.
4. Use query only for creative/content search inside ads or reels. Put category, running status, dates, grouping, and sorting in filters.
5. Return view_url links when present so the user can inspect the matching creative gallery.
```

## Notes

The hosted instruction document is the source of truth for non-Codex agents that can fetch Markdown but do not support Codex skills.

