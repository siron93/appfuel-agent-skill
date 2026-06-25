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

## Install

First create an App Fuel API key in the App Fuel app under `/api`.

For the production App Fuel API:

```bash
read -rsp "App Fuel API key: " ELITE_API_KEY; echo && \
export ELITE_API_KEY && \
APPFUEL_INSTALLER="$(mktemp -t appfuel-mcp-install.XXXXXX.py)" && \
curl -fsSL 'https://new.theappfuel.com/api/elite/v1/elite/mcp/install.py' -o "$APPFUEL_INSTALLER" && \
python3 "$APPFUEL_INSTALLER" --api-base 'https://new.theappfuel.com/api/elite/v1/elite'
```

This installs the local App Fuel MCP adapter and, for Codex users, can also install the `appfuel-data` skill.
By default it installs that skill into `~/.codex/skills/appfuel-data`; use `--no-skill` only if you do not want that.
For cautious/manual setup, download the installer, run `python3 "$APPFUEL_INSTALLER" --help`, and inspect it before executing.

## Install From GitHub

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
Skill source: https://github.com/siron93/appfuel-agent-skill/tree/main/appfuel-data

If App Fuel MCP tools are missing, pointed at a dead local service, or not configured for this API base, run this installer. It installs the local MCP adapter and installs the appfuel-data Codex skill by default:

```bash
read -rsp "App Fuel API key: " ELITE_API_KEY; echo && export ELITE_API_KEY && APPFUEL_INSTALLER="$(mktemp -t appfuel-mcp-install.XXXXXX.py)" && curl -fsSL 'https://new.theappfuel.com/api/elite/v1/elite/mcp/install.py' -o "$APPFUEL_INSTALLER" && python3 "$APPFUEL_INSTALLER" --api-base 'https://new.theappfuel.com/api/elite/v1/elite'
```

Before answering App Fuel questions:
1. Read the agent instructions URL.
2. Use the OpenAPI/schema URLs to discover current endpoints and filters.
3. If App Fuel MCP tools are available, use them instead of raw HTTP.
4. If the current session cannot reload a newly installed MCP server, say that a new agent session or MCP client restart is needed.
5. Use query only for creative/content search inside ads or reels. Put category, running status, dates, grouping, and sorting in filters.
6. Use pagination.next_request when a response has more results.
7. Return view_url links when present so the user can inspect the matching creative gallery.
8. Use collection tools when the user asks to save or organize research.
9. Do not print the API key in status messages or final notes.
```

## Notes

The hosted instruction document is the source of truth for non-Codex agents that can fetch Markdown but do not support Codex skills.

The skill keeps detailed endpoint and field references in `appfuel-data/references/` so agents can load that detail only when they need it.
