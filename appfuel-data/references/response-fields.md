# App Fuel Response Fields

The API returns compact agent-facing objects, not raw database rows. Read `/agent/schema/`, `/agent/schema/ads/`, `/agent/schema/reels/`, `/agent/schema/apps/`, `/agent/schema/collections/`, or `/agent/openapi.json/` for the latest contract.

Top-level search response fields:

- `schema_version`
- `generated_at`
- `summary`
- `view_url`: shareable App Fuel gallery URL when `return_view=true`; otherwise `null`.
- `query.input`
- `query.normalized_filters`
- `query.search_mode`
- `kpis`
- `results`
- `facets.apps`
- `pagination`: includes page metadata and usually `next_request` when more results are available.

Paid ad result fields:

- `ad_id`: representative paid ad archive id.
- `creative_key`: App Fuel creative dedupe key.
- `app.id`, `app.name`, `app.category`, `app.icon_url`, `app.latest_revenue`
- `ad.is_active`, `ad.media_type`, `ad.first_seen_at`, `ad.last_seen_at`, `ad.flight_days`, `ad.variants_count`
- `creative.title`, `creative.body_text`, `creative.cta_text`, `creative.thumbnail_url`, `creative.media_url`
- `evidence.people.roles`, `evidence.people.genders`, `evidence.people.age_ranges`, `evidence.people.main_people`, `evidence.people.label_note`
- `evidence.creative_ai.hook_type`, `evidence.creative_ai.creative_formats`, `evidence.creative_ai.production_quality`, `evidence.creative_ai.cut_speed`, `evidence.creative_ai.pain_intensity`, `evidence.creative_ai.radar_average`
- `evidence.text.main_claim`, `evidence.text.strategy_summary`, `evidence.text.visual_description`, `evidence.text.ocr_text`

Organic reel result fields:

- `reel_id`
- `app.id`, `app.name`, `app.category`, `app.icon_url`, `app.latest_revenue`
- `organic.instagram_username`, `organic.account_type`, `organic.permalink`, `organic.posted_at`, `organic.views`, `organic.likes`, `organic.comments`
- `creative.title`, `creative.caption_text`, `creative.music_title`, `creative.music_artist`, `creative.video_duration`, `creative.thumbnail_url`, `creative.video_url`
- `evidence.people.genders`, `evidence.people.age_ranges`, `evidence.people.label_note`
- `evidence.creative_ai.hook_text`, `evidence.creative_ai.hook_type`, `evidence.creative_ai.hook_source`, `evidence.creative_ai.creator_mode`, `evidence.creative_ai.ui_context`, `evidence.creative_ai.ui_clarity`, `evidence.creative_ai.cut_speed`, `evidence.creative_ai.ending_type`, `evidence.creative_ai.mechanics`
- `evidence.text.organic_strategy_summary`, `evidence.text.reel_description`, `evidence.text.caption_summary`, `evidence.text.spoken_transcript`, `evidence.text.ocr_text`, `evidence.text.search_tags`

App search and app detail fields:

- `app.id`, `app.name`, `app.subtitle`, `app.seller`, `app.categories`
- `app.icon_url`, `app.rating_average`, `app.rating_count`, `app.release_date`
- `app.latest_revenue`, `app.latest_revenue_month`
- `app.store_links.ios`, `app.store_links.android`
- `app.social.instagram_username`, `app.social.has_instagram_reels`
- `app.intelligence.oneLiner`
- `app.intelligence.primaryUseCases`
- `app.intelligence.targetAudiences`
- `app.intelligence.nicheStatus`
- `app.intelligence.jobToBeDone`
- `app.intelligence.opportunityLens`
- `app.intelligence.whyInteresting`
- `app.intelligence.whyUnique`
- `app.intelligence.buildability`
- `app.intelligence.networkEffects`
- `app.intelligence.noveltyScore`, `app.intelligence.commodityScore`, `app.intelligence.incumbentScore`
- `revenue_series`: recent monthly app revenue rows when requested.
- `latest_rankings`: latest category/country ranking rows when requested.
- `similar_apps`: compact app objects.
- `view_urls.ads`, `view_urls.organic_reels`: App Fuel gallery entry points for that app.

Saved research fields:

- `collections`: collection id, name, `isPublic`, item count, created/updated timestamps, and `url` for public collections.
- `items`: saved app/ad/reel records with title, subtitle, app id, tags, notes, thumbnail/media URLs, metadata, source payload, collection id, and timestamps.
- `filters`: saved filter views with surface, name, description, filters, summary, and timestamps.
- `kpis`: saved item/filter counts, collected-item count, and collection item limit.
- `pagination`: item pagination and `next_request` when more saved items are available.

Missing values can be `null`, empty arrays, or absent. People labels are creative-content labels, not identity recognition.

Do not promise unsupported raw database fields. If the user needs data not listed here, say the current agent surface does not return it and use schema tools plus `view_url` for inspection.
