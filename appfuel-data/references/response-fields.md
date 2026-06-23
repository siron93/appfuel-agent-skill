# App Fuel Response Fields

Use this reference when you need to know what fields an App Fuel search can return. The API intentionally returns compact agent-facing objects, not raw database rows.

For the latest contract, read:

- `GET /agent/schema/ads/`
- `GET /agent/schema/reels/`
- `GET /agent/openapi.json/`

## Search Response

Paid ads and organic reels return the same top-level shape:

- `schema_version`: response contract version.
- `generated_at`: server timestamp.
- `summary`: human-readable result summary.
- `view_url`: shareable App Fuel gallery URL when `return_view=true`; otherwise `null`.
- `query.input`: the content-search query received by the API.
- `query.normalized_filters`: filters after validation and alias normalization.
- `query.search_mode`: backend search mode when available.
- `kpis`: aggregate counts and metrics for the matched set.
- `results`: compact result objects. See ad and reel field lists below.
- `facets.apps`: app facet/group data from the search index.
- `pagination`: pagination metadata for either app grouping or individual results.

## Paid Ad Result Fields

Each `results[]` item from `POST /agent/ads/search/` can include:

- `ad_id`: representative Meta Ads Library archive id.
- `creative_key`: App Fuel dedupe key for the creative group.

`app`:

- `id`: App Store app id.
- `name`: app name.
- `category`: App Store category id.
- `icon_url`: app icon URL.
- `latest_revenue`: latest known app revenue metric.

`ad`:

- `is_active`: whether the representative ad is currently active.
- `media_type`: canonical media type or display format.
- `first_seen_at`: first seen/start timestamp.
- `last_seen_at`: last seen/end timestamp.
- `flight_days`: observed run duration.
- `variants_count`: number of known variants in the creative group.
- `ad_library_url`: Meta Ads Library URL when available.

`creative`:

- `title`: ad title/headline from the snapshot.
- `body_text`: truncated body copy.
- `cta_text`: call-to-action text.
- `thumbnail_url`: thumbnail or preview image URL.
- `media_url`: primary media URL.

`evidence.people`:

- `roles`: AI creative labels such as creator, presenter, customer, founder, actor, or user.
- `genders`: AI creative labels such as female, male, mixed group, or unknown.
- `age_ranges`: AI creative age-range labels.
- `main_people`: compact people observations from the creative analysis.
- `label_note`: reminder that people fields are creative-content labels, not identity recognition.

`evidence.creative_ai`:

- `hook_type`: detected hook type.
- `creative_formats`: detected creative formats.
- `production_quality`: detected production-quality label.
- `cut_speed`: pacing label.
- `pain_intensity`: pain-point intensity label.
- `radar_average`: App Fuel radar score.

`evidence.text`:

- `main_claim`: extracted main claim.
- `strategy_summary`: strategy summary.
- `visual_description`: video/image description.
- `ocr_text`: visible text detected in the creative.

## Organic Reel Result Fields

Each `results[]` item from `POST /agent/reels/search/` can include:

- `reel_id`: Instagram reel id.

`app`:

- `id`: App Store app id.
- `name`: app name.
- `category`: App Store category id.
- `icon_url`: app icon URL.
- `latest_revenue`: latest known app revenue metric.

`organic`:

- `instagram_username`: source Instagram username.
- `account_type`: account category, such as app, company, or influencer/creator.
- `permalink`: Instagram reel URL.
- `posted_at`: posting timestamp.
- `views`: reel views.
- `likes`: like count.
- `comments`: comment count.

`creative`:

- `title`: compact title.
- `caption_text`: truncated caption text.
- `music_title`: music title.
- `music_artist`: music artist.
- `video_duration`: video duration.
- `thumbnail_url`: thumbnail URL.
- `video_url`: video URL when available.

`evidence.people`:

- `genders`: AI creative people labels.
- `age_ranges`: AI creative age-range labels.
- `label_note`: reminder that people fields are creative-content labels, not identity recognition.

`evidence.creative_ai`:

- `hook_text`: extracted hook text.
- `hook_type`: detected hook type.
- `hook_source`: source of the hook signal.
- `creator_mode`: creator format, such as talking head, voiceover, screen recording, or faceless.
- `ui_context`: detected UI context.
- `ui_clarity`: UI clarity label.
- `cut_speed`: pacing label.
- `ending_type`: ending pattern.
- `mechanics.trend_audio`: whether trend audio is detected.
- `mechanics.tutorial`: whether a tutorial mechanic is detected.
- `mechanics.screen_recording`: whether screen recording is detected.
- `mechanics.loopable`: whether the reel appears loopable.

`evidence.text`:

- `organic_strategy_summary`: strategy summary.
- `reel_description`: visual/video description.
- `caption_summary`: caption summary.
- `spoken_transcript`: truncated transcript.
- `ocr_text`: visible text detected in the reel.
- `search_tags`: AI/search tags.

## Field Caveats

- Missing values can be `null`, empty arrays, or absent when the source data does not contain that signal.
- Do not promise unsupported raw database fields. If the user needs data not listed here, say the current agent surface does not return it and use `view_url` for human inspection.
- People labels describe creative content only. Do not describe them as biometric recognition or identity inference.
