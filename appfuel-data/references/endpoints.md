# App Fuel Agent Endpoints

Production API base:

```text
https://new.theappfuel.com/api/elite/v1/elite
```

Authentication:

- Public documentation endpoints do not require authentication.
- Search, app detail, usage, and saved research endpoints require an App Fuel API key.
- Send the key as `Authorization: Bearer <account-api-key>` or `X-Elite-Agent-Key: <account-api-key>`.
- If a protected endpoint returns `{"detail":"Agent authentication credentials were not provided."}`, create/configure an App Fuel API key and retry.

Public endpoints:

- `GET /agent/instructions.md/`: hosted Markdown instructions.
- `GET /agent/openapi.json/`: curated OpenAPI spec.
- `GET /agent/schema/`: combined agent contract.
- `GET /agent/schema/ads/`: paid ads filters, pagination, and response shape.
- `GET /agent/schema/reels/`: organic reels filters, pagination, and response shape.
- `GET /agent/schema/apps/`: app search/detail fields.
- `GET /agent/schema/collections/`: saved research operations.
- `GET /mcp/connection/`: hosted MCP connection snippets.
- `GET|POST /mcp`: hosted Streamable HTTP MCP endpoint. Use this as the default agent connection.

Protected endpoints:

- `GET /agent/usage/`: monthly request and download usage.
- `POST /agent/apps/search/`: app discovery by name, category, audience, job-to-be-done, competitor set, or product concept.
- `POST /agent/apps/detail/`: one app's metadata, intelligence, revenue series, latest rankings, similar apps, and App Fuel gallery entry points.
- `POST /agent/ads/search/`: enriched paid ad search.
- `POST /agent/reels/search/`: enriched organic Instagram Reels search.
- `POST /agent/collections/list/`: saved collections, items, filters, and pagination.
- `POST /agent/collections/create/`: create or reuse a saved collection.
- `POST /agent/collections/update/`: make a saved collection public or private.
- `POST /agent/collections/save-item/`: save an app, paid ad, or organic reel.
- `POST /agent/collections/save-filter/`: save a reusable research filter view.

App search request:

```json
{
  "query": "fasting tracker for women",
  "limit": 30,
  "offset": 0
}
```

Paid ads request:

```json
{
  "query": "",
  "filters": {
    "category": "HEALTH_AND_FITNESS",
    "active_status": "active",
    "min_app_revenue": 15000,
    "max_app_revenue": 30000
  },
  "limit": 30,
  "ads_per_app": 8,
  "group_by": "app",
  "return_view": true
}
```

Organic reels request:

```json
{
  "query": "",
  "filters": {
    "category": "HEALTH_AND_FITNESS",
    "account_type": "app_account",
    "min_views": 10000
  },
  "limit": 30,
  "reels_per_app": 8,
  "group_by": "app",
  "return_view": true
}
```

Save item request:

```json
{
  "item_type": "app",
  "item_key": "1234567890",
  "collection_name": "Fitness apps running ads",
  "title": "Example Fitness App",
  "notes": "Strong onboarding and active paid ads."
}
```

Create public collection request:

```json
{
  "name": "Fitness apps running ads",
  "is_public": true
}
```

Update collection visibility request:

```json
{
  "collection_id": "collection-id",
  "is_public": false
}
```

Query rule:

- Use `query` only for creative content inside ads or reels: scenes, hooks, claims, offers, visible UI, captions, transcripts, pain points, creator mechanics, or product moments.
- Put category, active/running status, app ids, account type, media type, hook type, video duration, dates, revenue, engagement, grouping, sorting, and pagination in filters or typed arguments.
- To search exact hook wording only, set `search_scope="hook"` with `query`. Use the default `search_scope="ai"` for broader creative/content matching.
- For "find Health & Fitness apps running ads", use `query=""`, `filters.category="HEALTH_AND_FITNESS"`, `filters.active_status="active"`, and `group_by="app"`.
- For one or more specific apps, use `include_app_ids` with App Fuel app ids from `search_apps` or `app_detail`. For paid ads, use `exclude_app_ids` to remove known apps from a market scan.

Common ads filters include `category`, `active_status`, `include_app_ids`, `exclude_app_ids`, `media_type`, `people_gender`, `people_role`, `people_age_range`, `creative_format`, `production_quality`, `language`, `cut_speed`, `pain_intensity`, `hook_type`, `search_scope`, `min_run_days`, `max_run_days`, `min_video_duration_seconds`, `max_video_duration_seconds`, `web_funnel`, `min_app_revenue`, `max_app_revenue`, `min_radar_average`, `radar_mins`, `sort`, and `explore_seed`.

Common reels filters include `category`, `account_type`, `include_app_ids`, `language`, `hook_type`, `people_gender`, `people_age_range`, `ui_context`, `creator_mode`, `dominant_emotion`, `min_views`, `min_likes`, `min_comments`, `min_video_duration_seconds`, `max_video_duration_seconds`, `min_app_revenue`, `max_app_revenue`, `date_start`, `date_end`, `app_release_start`, `app_release_end`, `sort`, and `search_scope`.

Pagination:

- Default search pages return about 30 results.
- Use `pagination.next_request` when present.
- For grouped app results, the next page normally uses `app_offset`.
- For flat creative results and app search, the next page normally uses `offset`.

Set `return_view=true` when examples matter and return `view_url` to the user.

Collections are private by default. Public collections returned by `create_collection`, `update_collection`, and `list_collections` include `collection.url` when a `shareId` is available. Return that URL as the direct collection link after creating or filling a public collection. Private collections do not include a public URL.

Collection limit: a user can keep up to 20,000 saved items inside collections across all collections. When the limit is reached, moving or saving another item into a collection returns an error.
