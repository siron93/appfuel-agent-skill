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

Use the hosted Streamable HTTP MCP server:

```text
https://new.theappfuel.com/api/elite/v1/elite/mcp
```

Codex:

```bash
codex mcp add appfuel-data --url 'https://new.theappfuel.com/api/elite/v1/elite/mcp'
codex mcp login appfuel-data
```

Claude Code:

```bash
claude mcp add --transport http appfuel-data 'https://new.theappfuel.com/api/elite/v1/elite/mcp' --scope user
claude mcp login appfuel-data
```

Cursor or other MCP clients:

```json
{
  "mcpServers": {
    "appfuel-data": {
      "url": "https://new.theappfuel.com/api/elite/v1/elite/mcp"
    }
  }
}
```

When the client asks for authorization, approve App Fuel in the browser. API keys remain available in the App Fuel `/api` page for REST tests and fallback clients that do not support remote MCP OAuth.

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
Agent instructions: https://new.theappfuel.com/api/elite/v1/elite/agent/instructions.md
OpenAPI spec: https://new.theappfuel.com/api/elite/v1/elite/agent/openapi.json
Agent schema: https://new.theappfuel.com/api/elite/v1/elite/agent/schema
Hosted MCP URL: https://new.theappfuel.com/api/elite/v1/elite/mcp
OAuth metadata: https://new.theappfuel.com/api/elite/v1/elite/.well-known/oauth-authorization-server
Skill source: https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data

Before answering App Fuel questions:
1. For Codex, install the skill with: Use $skill-installer to install appfuel-data from https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data
2. Read the agent instructions URL and use the OpenAPI/schema URLs to discover current endpoints and filters.
3. If the MCP client asks for authorization, open the browser login and approve App Fuel.
4. If App Fuel MCP tools are available, use them instead of raw HTTP.
5. If the current session cannot see the hosted MCP server after configuration, say that a new agent session or MCP client restart/refresh is needed.
6. Use query for whole creative-profile search inside ads or reels, not hook-only or field-specific search. Put category, running status, dates, grouping, sorting, pagination, and app-level semantic constraints in filters or typed arguments.
7. Use query as a string array for several short creative-content alternatives instead of one long keyword string.
8. Use app_product_query for full app-profile embedding search such as "photo and video editing apps"; it is not a hook/positioning-only embedding. Do not add a broad category filter unless the user asked for it.
9. Flat search pages are capped at 50 paid creatives or organic reels. Grouped app pages are capped at 20 apps, with up to 24 ads/reels per app. Use pagination.next_request when a response has more results; for deeper examples from one app, make a follow-up flat request with include_app_ids for that app and offset pagination.
10. Return view_url links when present so the user can inspect the matching creative gallery.
11. Use collection tools when the user asks to save or organize research.
12. Do not ask the user to paste an App Fuel API key into chat. If OAuth is unavailable, ask the user to use the API-key fallback from the App Fuel API page.
13. Do not print OAuth tokens, API keys, authorization codes, or refresh tokens in status messages, command transcripts, or final notes.
```

## Notes

The hosted instruction document is the source of truth for non-Codex agents that can fetch Markdown but do not support Codex skills.

The skill keeps detailed endpoint and field references in `appfuel-data/references/` so agents can load that detail only when they need it.
