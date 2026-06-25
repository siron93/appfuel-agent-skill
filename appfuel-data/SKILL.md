---
name: appfuel-data
description: Use App Fuel enriched app, paid ad, organic reel, and saved research intelligence through the App Fuel MCP tools. Trigger when a user asks for app market research, app discovery, competitor lists, paid ad examples, organic content examples, creative patterns, revenue-filtered app research, or saved App Fuel research collections.
---

# App Fuel Data

Use the App Fuel MCP tools for questions about apps, competitors, paid ads, organic reels, creative patterns, revenue-filtered markets, and saved App Fuel research.

Important: `query` is only for creative/content search inside ads or reels. Do not put app category, market, active/running status, media type, dates, revenue, grouping, sorting, or pagination into `query`; send those as explicit tool filters or typed arguments. If the user only asks for an app/category/status list and gives no creative-content constraint, pass `query=""`.

For endpoint, request, and returned-field details, read `references/endpoints.md` and `references/response-fields.md`. Prefer the live schema tools when available, because the API contract can add fields over time.

## Workflow

1. Call `describe_agent_schema` when you need the full current surface.
2. Call `describe_ads_schema`, `describe_reels_schema`, `describe_apps_schema`, or `describe_collections_schema` before guessing filters or fields.
3. Use `search_apps` for app discovery, competitor discovery, audiences, categories, jobs-to-be-done, and product concepts.
4. Use `app_detail` when the user asks about one app's revenue, rankings, intelligence, similar apps, or ad/reel entry points.
5. Use `search_ads` for paid ad questions and `search_reels` for organic content questions.
6. Extract explicit filters first: category, active status, media type, revenue, people labels, hook types, duration, grouping, dates, sorting, and pagination.
7. Put only the actual creative-content constraint in `query`, such as visible UI, hook/copy, scene, claim, offer, pain point, product moment, or reel mechanic.
8. Ask for `return_view=true` when visual inspection matters or when the user wants examples.
9. Return the `view_url` when present.
10. Use `list_collections`, `create_collection`, `update_collection`, `save_item`, and `save_filter` when the user wants to save, organize, share, or revisit research.

For revenue-band requests such as "apps doing 20k a month and running ads", use `min_app_revenue` and `max_app_revenue` tool arguments or filters. For "around 20k", start with a narrow band such as 15000-30000; for "20k+" use `min_app_revenue=20000` and add `max_app_revenue` when the user asks for smaller apps or a tight band. Do not inspect local config files or credentials to discover hidden API options; use schema tools and documented filters.

Schema tools are public documentation surfaces. Search, app detail, usage, and saved research tools require an App Fuel API key. If the backend returns "Agent authentication credentials were not provided", tell the user to create a key in App Fuel's `/api` section and install/configure the MCP server with that key.

The hosted instruction document lives at `https://new.theappfuel.com/api/elite/v1/elite/agent/instructions.md/` for clients that can fetch plain Markdown but do not support Codex skills.

## Install Guidance

When App Fuel MCP tools are missing, pointed at an old local URL, or unauthenticated, tell the user to create an API key in App Fuel's `/api` section and run:

```bash
read -rsp "App Fuel API key: " ELITE_API_KEY; echo && \
export ELITE_API_KEY && \
APPFUEL_INSTALLER="$(mktemp -t appfuel-mcp-install.XXXXXX.py)" && \
curl -fsSL 'https://new.theappfuel.com/api/elite/v1/elite/mcp/install.py' -o "$APPFUEL_INSTALLER" && \
python3 "$APPFUEL_INSTALLER" --api-base 'https://new.theappfuel.com/api/elite/v1/elite'
```

For cautious/manual setup, download the installer to a temporary file first, run `python3 "$APPFUEL_INSTALLER" --help`, and inspect it before executing. Do not run remote code by piping curl directly into Python.

The installer writes the local MCP adapter and prints generic MCP server JSON with absolute local paths for Claude Code, Cursor, Windsurf, VS Code, Codex, and other MCP clients. For Codex, it also installs this skill into `~/.codex/skills/appfuel-data` and updates Codex MCP config by default. If MCP tools do not reload in the current thread, ask the user to open a new session or restart the MCP client.

## Search Guidance

For requests like "Find Health & Fitness apps that are running ads":

- Use `category="fitness"` or `category="HEALTH_AND_FITNESS"`.
- Use `active_status="active"` when the wording implies currently showing/running ads.
- Use `group_by_app=true` when the user asks for apps rather than individual creatives.
- Use `limit=30` unless the user asks for a different page size.
- Use `query=""` because there is no creative-content search term.

Correct:

```json
{
  "query": "",
  "category": "HEALTH_AND_FITNESS",
  "active_status": "active",
  "group_by_app": true,
  "limit": 30
}
```

Incorrect:

```json
{
  "query": "health and fitness apps running ads"
}
```

For requests like "fitness apps running ads that show a male presenter explaining a meal plan":

- Use `category="HEALTH_AND_FITNESS"`.
- Use `active_status="active"`.
- Use `people_gender=["male"]`.
- Use `query="meal plan explanation"` for the creative-content part.

People labels are AI creative-content labels. Describe them as "male-presenting/female-presenting creative labels" or "detected people mix"; do not imply identity recognition.

For hook and duration requests:

- Use `query` for exact hook wording, hook ideas, caption text, spoken transcript, OCR text, scenes, or product moments.
- When the user asks to search the hook itself, set `search_scope="hook"` so `query` searches only hook fields rather than the broad AI creative index.
- Use `hook_type` when the user asks for hook categories or patterns rather than exact copy.
- Use `min_video_duration_seconds` and `max_video_duration_seconds` when the user asks for short, long, or duration-banded video creatives or reels.
- Keep category, active status, revenue, dates, grouping, sorting, and pagination in filters or typed arguments.

## Pagination

Default search pages return about 30 results. Search responses include `pagination.has_more`, `pagination.next_offset`, `pagination.next_app_offset`, and usually `pagination.next_request`.

When `pagination.has_more=true`, call the same tool again with `pagination.next_request`. For grouped app results, the next page normally uses `app_offset`; for flat creative results, it normally uses `offset`.

## Saved Research

Use `list_collections` before saving if the user asks to add something to an existing collection. Use `create_collection` when the user names a new collection. Use `save_item` for apps, paid ads, and organic reels. Use `save_filter` when the user wants to reuse a research query such as "active Health & Fitness paid ads between 15k and 30k revenue."

Collections are private by default. Create or update a collection with `is_public=true` only when the user asks for a public/shareable collection or direct link. Public collections include `collection.url`; return that direct link when it is present. Private collections do not include a public URL.

Users can keep up to 20,000 saved items inside collections across all collections. If saving or moving an item returns a collection-limit error, explain that the item was not added to a collection and suggest removing older collected items or using saved filters for larger research sets.

## Video Review Guidance

Video review is optional, not required. When a user wants to inspect many video creatives or organic reels, you may suggest opening the `view_url` and using `2x` speed for a first-pass scan across hooks, pacing, UI moments, creator style, and repeated angles. Suggest slowing to `1x` only for examples the user wants to study closely, unclear spoken hooks, fine on-screen text, offer details, or final selected references.

## Response Style

Lead with the count and the shareable App Fuel gallery link when available. Then summarize the strongest examples or patterns with evidence from the tool response.

If the tool response is empty or weak, say the App Fuel data did not contain enough matching evidence and suggest a broader query or fewer filters.
