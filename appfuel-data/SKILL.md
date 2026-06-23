---
name: appfuel-data
description: Use App Fuel enriched app and ad intelligence through the App Fuel MCP tools. Trigger when a user asks for app market research, ad creative examples, Meta ads, visual creative patterns, app categories, fitness/finance/etc. app discovery, competitor lists, or shareable galleries of matching ads.
---

# App Fuel Data

Use the App Fuel MCP tools for questions about enriched app, ad, creative, or market-intelligence data.

Important: `query` is only for creative/content search inside ads or reels. Do not put app category, market, active/running status, media type, dates, revenue, grouping, or sorting intent into `query`; send those as explicit tool filters. If the user only asks for an app/category/status list and gives no creative-content constraint, pass `query=""`.

For endpoint, request, and returned-field details, read `references/endpoints.md` and `references/response-fields.md`. Prefer the live schema endpoints when available, because the API contract can add fields over time.

## Workflow

1. Call `describe_ads_schema` when you are unsure which ad filters exist.
2. Call `describe_reels_schema` when you are unsure which organic reel filters exist.
3. Call `get_account_usage` when the user asks about remaining API quota or download/request limits.
4. Call `search_ads` for paid Meta ad creative or advertiser questions; call `search_reels` for organic Instagram/Reels questions.
5. Extract explicit filters first: category, active status, media type, revenue, people labels, hook types, grouping, dates, and sorting.
6. Put only the actual creative-content constraint in `query`, such as visible UI, hook/copy, scene, claim, offer, pain point, product moment, or reel mechanic.
7. Ask for `return_view=true` when visual inspection matters or when the user wants examples.
8. Return the `view_url` when present.

Schema tools are public documentation surfaces. Search and usage tools require an App Fuel API key. If the backend returns "Agent authentication credentials were not provided", tell the user to create a key in App Fuel's `/elite/api` section and install/configure the MCP server with that key.

The hosted instruction document lives at `https://new.theappfuel.com/api/elite/v1/elite/agent/instructions.md/` for clients that can fetch plain Markdown but do not support Codex skills.

## Search Guidance

For requests like "Find Health & Fitness apps that are running ads":

- Use `category="fitness"` or `category="HEALTH_AND_FITNESS"`.
- Use `active_status="active"` when the wording implies currently showing/running ads.
- Use `group_by_app=true` when the user asks for apps rather than individual creatives.
- Use `query=""` because there is no creative-content search term.

Correct:

```json
{
  "query": "",
  "category": "HEALTH_AND_FITNESS",
  "active_status": "active",
  "group_by_app": true
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

## Video Review Guidance

Video review is optional, not required. When a user wants to inspect many video creatives or organic reels, you may suggest opening the `view_url` and using `2x` speed for a first-pass scan across hooks, pacing, UI moments, creator style, and repeated angles. Suggest slowing to `1x` only for examples the user wants to study closely, unclear spoken hooks, fine on-screen text, offer details, or final selected references.

## Response Style

Lead with the count and the shareable gallery link. Then summarize the strongest examples or patterns with evidence from the tool response.

If the tool response is empty or weak, say the App Fuel data did not contain enough matching evidence and suggest a broader query or fewer filters.
