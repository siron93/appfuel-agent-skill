# App Fuel Agent Skill

Public agent instructions for using App Fuel app, paid ad, organic reel, and saved research intelligence from Codex, Claude Code, Cursor, Windsurf, and other agent clients.

Recommended repository name:

```text
appfuel-agent-skill
```

## What This Gives Agents

- How to search App Fuel apps, paid ads, and organic Instagram Reels.
- When to use `query` versus structured filters.
- How to return App Fuel gallery links for human review.
- How to paginate and save useful findings to App Fuel collections.
- How to avoid exposing internal/admin App Fuel surfaces.

## Hosted MCP

First create an App Fuel API key in the App Fuel app under `/api`.

Use the hosted Streamable HTTP MCP server:

```text
https://new.theappfuel.com/api/elite/v1/elite/mcp
```

Codex:

```bash
export APPFUEL_API_KEY='<account-api-key>' && \
codex mcp add appfuel-data --url 'https://new.theappfuel.com/api/elite/v1/elite/mcp' --bearer-token-env-var APPFUEL_API_KEY
```

Claude Code:

```bash
claude mcp add --transport http appfuel-data 'https://new.theappfuel.com/api/elite/v1/elite/mcp' --header 'Authorization: Bearer <account-api-key>' --scope user
```

Cursor or other MCP clients:

```json
{
  "mcpServers": {
    "appfuel-data": {
      "url": "https://new.theappfuel.com/api/elite/v1/elite/mcp",
      "headers": {
        "Authorization": "Bearer <account-api-key>"
      }
    }
  }
}
```

## Codex Skill

Install the skill folder from:

```text
https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data
```

In Codex, ask:

```text
Use $skill-installer to install appfuel-data from https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data
```

Manual install:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R appfuel-data "${CODEX_HOME:-$HOME/.codex}/skills/appfuel-data"
```

## Agent Handoff

Paste this into an agent after the MCP connector is configured:

```text
Use App Fuel as an agent data source for app, paid ad, organic reel, and saved research intelligence.

API base: https://new.theappfuel.com/api/elite/v1/elite
Agent instructions: https://new.theappfuel.com/api/elite/v1/elite/agent/instructions.md/
OpenAPI spec: https://new.theappfuel.com/api/elite/v1/elite/agent/openapi.json/
Agent schema: https://new.theappfuel.com/api/elite/v1/elite/agent/schema/
Hosted MCP URL: https://new.theappfuel.com/api/elite/v1/elite/mcp
Skill source: https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data

Before answering App Fuel questions:
1. Read the agent instructions URL.
2. Use the OpenAPI/schema URLs to discover current endpoints and filters.
3. If App Fuel MCP tools are available, use them instead of raw HTTP.
4. If the current session cannot see the hosted MCP server after configuration, say that a new agent session or MCP client restart/refresh is needed.
5. Use query only for creative/content search inside ads or reels. Put category, running status, dates, grouping, and sorting in filters.
6. Use pagination.next_request when a response has more results.
7. Return view_url links when present so the user can inspect the matching creative gallery.
8. Use collection tools when the user asks to save or organize research.
9. Do not print the API key in status messages or final notes.
```

## Notes

The hosted instruction document is the source of truth for non-Codex agents that can fetch Markdown but do not support Codex skills.

The skill keeps detailed endpoint and field references in `appfuel-data/references/` so agents can load that detail only when they need it.
