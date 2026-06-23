# App Fuel Agent Endpoints

Use this reference when you need raw HTTP endpoint details, are configuring an MCP adapter, or are unsure which surface to call.

Production API base:

```text
https://new.theappfuel.com/api/elite/v1/elite
```

Authentication:

- Public documentation endpoints do not require authentication.
- Search, usage, and key-management endpoints require an App Fuel API key.
- Send the key as `Authorization: Bearer <account-api-key>` or `X-Elite-Agent-Key: <account-api-key>`.
- If a protected endpoint returns `{"detail":"Agent authentication credentials were not provided."}`, create/configure an App Fuel API key and retry.

## Public Agent Documentation

- `GET /agent/instructions.md/`
  Agent-readable Markdown instructions for clients that can fetch URLs but do not support this skill.

- `GET /agent/openapi.json/`
  Curated OpenAPI spec for the agent-facing API. Use it to discover current paths, examples, auth, request shapes, and response schemas.

- `GET /agent/schema/`
  Combined agent contract for paid ads and organic reels.

- `GET /agent/schema/ads/`
  Paid Meta ads filters, query rules, gallery behavior, and returned ad fields.

- `GET /agent/schema/reels/`
  Organic Instagram Reels filters, query rules, gallery behavior, and returned reel fields.

- `GET /mcp/install.py/`
  One-command local MCP adapter installer.

## Protected Agent Data

- `GET /agent/usage/`
  Returns account-level monthly request and download usage for the current key.

- `POST /agent/ads/search/`
  Searches enriched Meta ad creatives.

- `POST /agent/reels/search/`
  Searches enriched organic Instagram Reels.

## Paid Ads Search

Use `POST /agent/ads/search/` for paid Meta ad creative, advertiser, and app discovery questions.

Request shape:

```json
{
  "query": "",
  "filters": {
    "category": "HEALTH_AND_FITNESS",
    "active_status": "active"
  },
  "limit": 50,
  "ads_per_app": 8,
  "group_by": "app",
  "return_view": true
}
```

Common filters:

- `category`: App Store category id or alias, such as `HEALTH_AND_FITNESS`, `FINANCE`, `PRODUCTIVITY`, `fitness`, or `photo`.
- `active_status`: `active`, `inactive`, or `all`.
- `media_type`: `video`, `image`, or `all`.
- `people_gender`: AI creative label such as `female`, `male`, `mixed_group`, or `unknown`.
- `people_role`: labels such as `creator`, `presenter`, `customer`, `expert`, `founder`, `actor`, or `user`.
- `creative_format`: labels such as `ugc_style`, `app_demo_style`, `screen_recording_style`, `testimonial_style`, `before_after_style`, `faceless`, `meme_format`, or `skit_format`.
- `language`: language code such as `en`, `de`, or `fr`.
- `min_run_days`, `max_run_days`: ad flight duration filters.
- `min_video_duration_seconds`, `max_video_duration_seconds`: video duration filters.
- `web_funnel`: `yes`, `no`, or `both`.
- `min_app_revenue`, `max_app_revenue`: app revenue filters.
- `min_radar_average`: minimum App Fuel radar score.
- `sort`: `started_desc`, `started_asc`, `duration_desc`, `revenue_desc`, or `radar_desc`.
- `group_by`: use `app` when the user asks for apps; use empty string for individual creative rows.

Query rule:

- Use `query` only for creative content inside the ad: scene, hook, claim, offer, visible UI, pain point, creator format, or product moment.
- Put category, active/running status, media type, revenue, grouping, and sorting in filters.
- If the user asks "find Health & Fitness apps running ads", use `query=""`.

## Organic Reels Search

Use `POST /agent/reels/search/` for organic Instagram/Reels content and app account research.

Request shape:

```json
{
  "query": "",
  "filters": {
    "category": "HEALTH_AND_FITNESS",
    "account_type": "app_account",
    "min_views": 10000
  },
  "limit": 50,
  "reels_per_app": 8,
  "group_by": "app",
  "return_view": true
}
```

Common filters:

- `category`: App Store category id or alias.
- `account_type`: `app_account`, `company_account`, or `influencer_creator`.
- `language`: language code such as `en`, `es`, `de`, or `fr`.
- `hook_type`: hook label such as `question`, `problem_statement`, `curiosity_gap`, `visual_surprise`, `trend_or_meme`, `how_to`, `transformation`, `before_after`, `product_demo_first`, `app_ui_first`, `bold_claim`, or `social_proof`.
- `people_gender`, `people_age_range`: AI creative-content labels.
- `ui_context`: labels such as `app_screen_recording`, `phone_in_hand`, `desktop_or_web_ui`, `animated_ui_mockup`, `static_screenshot`, or `not_shown`.
- `creator_mode`: labels such as `talking_head`, `voiceover_only`, `hands_only`, `faceless`, `screen_recording`, `phone_in_hand`, or `mixed`.
- `dominant_emotion`: detected creative-emotion label.
- `min_views`, `min_likes`, `min_comments`: engagement filters.
- `min_app_revenue`, `max_app_revenue`: app revenue filters.
- `date_start`, `date_end`: reel date filters in `YYYY-MM-DD`.
- `sort`: `explore`, `recent`, `views`, `likes`, `comments`, or `revenue`.
- `search_scope`: `ai`, `hook`, or `app`.
- `group_by`: use `app` when the user asks for apps; use empty string for individual reels.

Query rule:

- Use `query` only for content inside the reel: visible scene, hook, caption, transcript, UI moment, claim, trend, or creator mechanic.
- Put category, account type, dates, engagement, revenue, grouping, and sorting in filters.

## Gallery Links

Set `return_view=true` when examples matter. Return `view_url` to the user so they can inspect the matching gallery in App Fuel.

Video review is optional. For large sets of video ads or reels, suggest opening the gallery and scanning first at `2x` speed, then slowing to `1x` for the few examples that need close study.
