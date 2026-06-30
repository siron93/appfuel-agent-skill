---
name: appfuel-data
version: 2026-06-30.agent-guide.5
description: Use App Fuel enriched app, Apple App Store review, paid ad, organic reel, and saved research intelligence through the App Fuel MCP tools. Trigger when a user asks for app market research, app discovery, competitor lists, review pain points, paid ad examples, organic content examples, creative patterns, revenue-filtered app research, saved App Fuel research collections, or visual research canvases.
---

# App Fuel Data

Use the App Fuel MCP tools for questions about apps, competitors, Apple App Store reviews, paid ads, organic reels, creative patterns, revenue-filtered markets, saved App Fuel research, and visual research canvases.

Important: `query` searches the whole AI creative/content profile inside ads or reels. It can be a string for one idea or a string array for 2-8 focused alternatives. It is not a hook-only or field-specific embedding search. Do not put app category, app id, market, app product concept, active/running status, media type, dates, revenue, grouping, sorting, or pagination into `query`; send those as explicit tool filters or typed arguments. If the user only asks for an app/category/status list and gives no creative-content constraint, pass `query=""`.

For app-level semantic matching, use `app_product_query` for the full app intelligence/profile: what the app does, product category, job-to-be-done, or app description, such as `app_product_query="photo and video editing apps"`. It is not a separate hook-only, positioning-only, or field-specific app embedding. If the product query already scopes the market, do not add a broad category filter unless the user explicitly asks for that category.

For endpoint, request, and returned-field details, read `references/endpoints.md` and `references/response-fields.md`. For visual board layout, node state, groups, arrows, html insight nodes, and snapshots, read `references/canvases.md` before creating or updating canvases. Prefer the live schema tools when available, because the API contract can add fields over time. If MCP is connected and this installed skill might be stale, call `get_agent_instructions`; if the hosted guide disagrees with this local skill, follow the hosted guide.

## Workflow

1. Call `get_agent_instructions` when local skill guidance may be stale or when tool behavior and local instructions disagree.
2. Call `describe_agent_schema` when you need the full current surface.
3. Call `describe_ads_schema`, `describe_reels_schema`, `describe_apps_schema`, `describe_app_reviews_schema`, `describe_collections_schema`, or `describe_canvases_schema` before guessing filters or fields.
4. Use `search_apps` for app discovery, competitor discovery, audiences, categories, jobs-to-be-done, and product concepts.
5. Use `app_detail` when the user asks about one app's revenue, rankings, intelligence, similar apps, or ad/reel entry points.
6. Use `app_store_reviews` when the user asks for review text, 1-star/5-star themes, pain points, objections, praise language, or hook inspiration from public Apple App Store reviews.
7. Use `search_ads` for paid ad questions and `search_reels` for organic content questions.
8. Use `ad_detail` when the user asks to inspect, explain, or extract details from one paid ad returned by search.
9. Use `similar_ads` when the user asks for more paid creatives like a specific ad.
10. Extract explicit filters first: category, active status, app ids, media type, revenue, people labels, hook types, duration, grouping, dates, sorting, and pagination.
11. Put only the actual creative-content constraint in `query`, such as visible UI, scene, claim, offer, pain point, product moment, reel mechanic, or hook-like wording inside the full creative profile.
12. Send `query` as a string array for 2-8 focused creative-content alternatives when one user request naturally contains several concepts.
13. Ask for `return_view=true` when visual inspection matters or when the user wants examples.
14. Return the `view_url` when present.
15. Use `list_collections`, `create_collection`, `update_collection`, and `save_item` when the user wants to save, organize, share, or revisit research.
16. Use `list_canvases`, `create_canvas`, `get_canvas`, `update_canvas`, and `get_canvas_snapshot` when the user wants a visual board, mapped findings, grouped examples, arrows, visual inspection, or a link to a research workspace.

For revenue-band requests such as "apps doing 20k a month and running ads", use `min_app_revenue` and `max_app_revenue` tool arguments or filters. For "around 20k", start with a narrow band such as 15000-30000; for "20k+" use `min_app_revenue=20000` and add `max_app_revenue` when the user asks for smaller apps or a tight band. Do not inspect local config files or credentials to discover hidden API options; use schema tools and documented filters.

Schema tools are public documentation surfaces. Search, app detail, usage, and saved research tools require an authenticated App Fuel connection. Prefer hosted MCP with OAuth browser login. If a client does not support OAuth for remote MCP, use the API-key fallback from App Fuel's `/api` section.

The hosted instruction document lives at `https://new.theappfuel.com/api/elite/v1/elite/agent/instructions.md` for clients that can fetch plain Markdown but do not support Codex skills.

## Hosted MCP Setup

Prefer the hosted Streamable HTTP MCP server:

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

When the MCP client asks for authorization, open the browser login and approve App Fuel. Do not print OAuth tokens, API keys, authorization codes, or refresh tokens in status messages, command transcripts, or final notes.

If MCP tools do not reload in the current thread after configuration, ask the user to open a new session or restart/refresh the MCP client.

## Search Guidance

For paid ad search results, read each result's `overview` first. It gives the hook, hook type/source, main claim, value proposition, pain points, target personas, offer, strategy summary, ad description, and structure without needing a detail call. Use `ad_detail` only when the user selects one ad or asks for deeper single-ad analysis.

For requests like "Find Health & Fitness apps that are running ads":

- Use `category="fitness"` or `category="HEALTH_AND_FITNESS"`.
- Use `active_status="active"` when the wording implies currently showing/running ads.
- Use `group_by_app=true` when the user asks for apps rather than individual creatives.
- Use `limit=20` for grouped app pages unless the user asks for fewer apps.
- Use `query=""` because there is no creative-content search term.

Correct:

```json
{
  "query": "",
  "category": "HEALTH_AND_FITNESS",
  "active_status": "active",
  "group_by_app": true,
  "limit": 20
}
```

Incorrect:

```json
{
  "query": "health and fitness apps running ads"
}
```

For requests like "find active ads with travel features from photo and video editing apps":

- Use `query="travel features"` for the creative-content constraint.
- Use `app_product_query="photo and video editing apps"` for the app/product constraint.
- Use `active_status="active"` for currently running ads.
- Do not add a broad category filter unless the user asked for that category; the product query already scopes the app market.

```json
{
  "query": "travel features",
  "filters": {
    "app_product_query": "photo and video editing apps",
    "active_status": "active"
  },
  "group_by_app": true,
  "limit": 20
}
```

For requests like "fitness apps running ads that show a male presenter explaining a meal plan":

- Use `category="HEALTH_AND_FITNESS"`.
- Use `active_status="active"`.
- Use `people_gender=["male"]`.
- Use `query="meal plan explanation"` for the creative-content part.

For requests with several related creative ideas, keep each query focused:

```json
{
  "query": ["dog training tip", "dog walking routine", "puppy care app demo"],
  "category": "LIFESTYLE",
  "active_status": "active",
  "media_type": "video",
  "group_by_app": true,
  "limit": 20
}
```

People labels are AI creative-content labels. Describe them as "male-presenting/female-presenting creative labels" or "detected people mix"; do not imply identity recognition.

For one paid ad selected from results:

- Use `ad_detail` with `public_id` (`ad_...`) when available; pass it as `creative_key`. Internal `creative_key` and representative `ad_id` also work.
- Use `similar_ads` with `public_id` (`ad_...`) when available; pass it as `creative_key`.
- Use `ad_id` only when `creative_key` is not available.

For app-specific ad or reel research:

- Use `search_apps` when the app id is unknown.
- Use `app_detail` when one app has been selected and the user needs context or gallery entry points.
- Use `app_store_reviews` when the user needs public Apple App Store review text, complaints, praise, objections, trust gaps, missing features, desired outcomes, or hook inspiration for one app. Countries are required two-letter storefronts such as `us`, `gb`, or `de`. Use `ratings=1`, `ratings=5`, `ratings=[1,2,3]`, or `ratings="1-3"` for specific star buckets. One call scans at most 1,000 total reviews and counts against account API usage.
- If App Store reviews return `error.code="app_reviews_limit_exceeded"`, reduce `reviews_per_country` or split countries into separate calls; do not retry the same oversized request.
- Use `include_app_ids` with `search_ads` or `search_reels` to restrict results to specific apps.
- Use `exclude_app_ids` with `search_ads` when the user wants to remove known apps from a market scan.
- Keep app ids out of `query`.

For hook and duration requests:

- Use `query` for creative ideas, caption text, spoken transcript, OCR text, scenes, product moments, or hook-like wording, knowing it searches the full creative profile rather than only a hook field.
- Use `hook_type` only when the user asks for hook-category labels or patterns rather than semantic search over hook text.
- Use `min_video_duration_seconds` and `max_video_duration_seconds` when the user asks for short, long, or duration-banded video creatives or reels.
- Keep category, active status, revenue, dates, grouping, sorting, and pagination in filters or typed arguments.

## Review-To-Hook Guidance

Use reviews as real user evidence, not generic sentiment filler. For creative strategy or brief generation, pull recent reviews for selected app(s) and countries, then use low-star reviews for pains, objections, churn triggers, confusing UX, missing features, and trust gaps. Use high-star reviews for desired outcomes, proof language, recommendation language, and moments of delight.

For a strong hook workflow, deconstruct winning ads into hook, body, CTA, format, target user, pain point, and proof. Then cluster App Store reviews into real user pains, connect those clusters to competitor ad angles, compare against the user's own top performers when available, and output brief candidates with pain cluster, review evidence, recommended hook/body/CTA, format, why it should work, example first 3 seconds, and confidence. AI drafts; a human approves. Missing reviews or weak evidence should lower confidence, not break the job.

## Pagination

Flat search pages return up to 50 paid creatives or organic reels. Grouped app pages return up to 20 apps, with up to 24 ads or reels per app. Search responses include `pagination.has_more`, `pagination.next_offset`, `pagination.next_app_offset`, and usually `pagination.next_request`.

When `pagination.has_more=true`, call the same tool again with `pagination.next_request`. For grouped app results, the next page normally uses `app_offset` to fetch more apps. To inspect more than 24 ads or reels from one selected app, make a follow-up flat request with `include_app_ids` for that app, `group_by=""`, and `offset` pagination; that counts as another request.

## Saved Research

Use `list_collections` before saving if the user asks to add something to an existing collection. Use `create_collection` when the user names a new collection. Use `save_item` for apps, paid ads, and organic reels. Use search result `public_id` as `item_key` for ads (`ad_...`) and reels (`reel_...`) when present; use app `id` for apps. App Fuel hydrates saved item title, media, links, metadata, and source payload from `item_type` plus `item_key`; do not provide override fields for those generated values.

Collections are private by default. Create or update a collection with `is_public=true` only when the user asks for a public/shareable collection or direct link. Public collections include `collection.url`; return that direct link when it is present. Private collections do not include a public URL.

Users can keep up to 20,000 saved items inside collections across all collections. If saving or moving an item returns a collection-limit error, explain that the item was not added to a collection and suggest removing older collected items or using saved filters for larger research sets.

## Research Canvases

Use canvases when the user wants findings laid out visually. A canvas can contain media/video nodes, label nodes, static html insight nodes, groups, and arrows.

Read `references/canvases.md` before creating a new canvas, updating an existing canvas, adding html insight nodes, arranging many findings, or using snapshots.

- Use `list_canvases` before modifying an existing board.
- Use `get_canvas` to inspect current nodes, arrows, groups, and viewport before updating.
- Use stable node, group, and edge ids so future updates can move or annotate the same items.
- Use `name` in `create_canvas` or `update_canvas` when the user asks to name or rename a board.
- Use arrows with `fromNodeId`/`toNodeId` to attach to nodes, `start`/`end` points for freeform arrows, or one attached endpoint plus one free point.
- Place findings in readable columns or groups. Keep media nodes large enough to preview, and add short label or static html nodes for takeaways and compact analytics.
- Use `get_canvas_snapshot` after large layout changes or when a visual check is useful. Pass `crop: {x, y, width, height}` to inspect one specific area.
- Return `canvas.workspaceUrl` so the signed-in user can open the board.
- If the user asks for a public/shareable board, create or update the canvas with `is_public=true` and return `canvas.url` when present.

## Interactive Follow-Ups

After a useful research result, offer 2-3 concrete next actions that fit the result. Phrase them as short questions the user can answer with yes/no or a number. Do not offer every possible action; choose the most helpful next steps.

Good follow-ups include:

- "Should I save these to a collection?"
- "Should I create an infinite canvas with the strongest examples and pattern notes?"
- "Should I find the repeated hooks, offers, and visual trends across these results?"
- "Should I pull similar ads for the top 3 examples?"
- "Should I narrow this to smaller apps, higher revenue apps, or a specific country/category?"

When the user says yes to a follow-up, use the relevant MCP tools immediately. For collections, save the selected apps/ads/reels or reusable filter. For canvases, create or update a board and return `canvas.workspaceUrl`; make it public only if the user asks for a shareable link.

## Video Review Guidance

Video review is optional, not required. When a user wants to inspect many video creatives or organic reels, you may suggest opening the `view_url` and using `2x` speed for a first-pass scan across hooks, pacing, UI moments, creator style, and repeated angles. Suggest slowing to `1x` only for examples the user wants to study closely, unclear spoken hooks, fine on-screen text, offer details, or final selected references.

## Response Style

Lead with the count and the shareable App Fuel gallery link when available. Then summarize the strongest examples or patterns with evidence from the tool response.

If the tool response is empty or weak, say the App Fuel data did not contain enough matching evidence and suggest a broader query or fewer filters.
