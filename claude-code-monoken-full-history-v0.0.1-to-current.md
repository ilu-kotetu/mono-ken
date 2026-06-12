# Claude Code Full Handoff Prompt: Monoken v0.0.1 to Current

You are Claude Code working in this workspace:

```text
C:\Users\iluko\Documents\Codex\2026-06-08\chatgpt
```

First read:

```text
CLAUDE.md
```

Then use this prompt as the complete handoff of Monoken Affiliate Engine from the original v0.0.1 concept through the current implementation.

Important: the workspace may contain partial edits from an in-progress product-research workflow. Inspect the current source and local file contents before changing anything. Continue from the current state, do not blindly duplicate logic, and do not revert unrelated changes.

## Current Known State

- Main plugin source: `work/monoken-affiliate-engine`
- Latest packaged version known before the newest product-research work: `0.14.2`
- Latest ZIP known: `outputs/monoken-affiliate-engine-v0.14.2.zip`
- Current main plugin files:
  - `work/monoken-affiliate-engine/monoken-affiliate-engine.php`
  - `work/monoken-affiliate-engine/includes/`
  - `work/monoken-affiliate-engine/assets/`
  - `work/monoken-affiliate-engine/docs/`
  - `work/monoken-affiliate-engine/README.md`

The next requested feature is product-research workflow improvement and stronger Japanese localization. Because it is a feature addition, the expected version after completion is `0.15.0`, unless the source already has a newer version.

## Product Vision From v0.0.1

Build a WordPress affiliate operation system that can become a paid product worth around 50,000 yen or more.

It should not be just an article generator. It should feel like an operations system for running multiple affiliate sites:

- Product research
- Product selection
- Article generation
- Featured image generation
- Affiliate link insertion
- Quality scoring and rewriting
- Diagnostics
- Click analytics
- SEO/AIO output
- Autopilot/queue operation
- Multi-site differentiation
- Backup/restore and support logs
- Commercial packaging readiness

The original site used WordPress with the SWELL theme. The user later requested rebuilding as a new plugin and theme, while keeping visual migration from SWELL as painless as possible. The plugin is the main implementation target for now.

## Versioning Rule

- Major version: only when the user explicitly requests it.
- Minor version: feature additions.
- Patch version: fixes.
- Keep version synchronized in:
  - Plugin header `Version`
  - `MAE_VERSION`
  - `README.md`
  - ZIP filename

## Development Rules

- Development names/classes/functions/internal keys should be English.
- User-facing display should be Japanese.
- Use WordPress APIs and safe escaping/sanitization.
- Avoid plugin-load fatal errors.
- Do not call external APIs during activation.
- Avoid unnecessary AI/API calls, especially when duplicate checks or invalid state can stop earlier.
- Product API calls should respect configured intervals.
- Do not weaken existing affiliate link handling.
- Do not compare unrelated product types, such as hair irons and home projectors.

## Major Requirements Accumulated So Far

### 1. Plugin/Theme Rebuild

Original request:

- Rebuild as a WordPress plugin/theme system.
- Theme should be able to migrate away from SWELL without breaking the appearance too much.
- Start as `v0.0.1`.
- Remove unnecessary functions.
- Make product comparisons mainly in VS format.
- Rework AI usage.
- Support complete automation, SEO, and AIO.
- Provide sitemap and robots.txt support.
- Identify necessary and unnecessary functions.

### 2. Multi-Site Operation and Differentiation

The user plans to deploy to multiple sites and run them automatically.

Must avoid Google seeing all sites as the same:

- Site-specific prompt differences must be enforced.
- Multiple article structure patterns.
- Different heading structures for the same product.
- Site-specific internal link strategies.
- More design presets.
- Generated article similarity checks.
- Guards against creating the same text across multiple sites.
- Site profile, editorial policy, comparison angle, tone, product genre, article structure, and site identity seed should be switchable from settings.

User-facing sites should look different:

- Design presets
- Colors
- Overall width
- Card presentation
- Site profile
- Writing tone
- Product genre
- Article structure

### 3. Product Page and Article Strategy

Product selection should support:

- Same product type
- Same category
- Model variants
- Similar price range
- Highly rated alternatives
- Manual A/B selection
- Auto decision

Product presentation should support:

- VS comparison
- Detailed review
- Explanation plus model-variant listing
- Buying guide
- Pochipp-style multi-shop affiliate buttons
- CTA improvements
- Mobile-friendly comparison UI
- "Recommended pick" display
- Product cards with Rakuten/Yahoo/Amazon-style buttons

Articles should persuade affiliate readers by:

- Clear conclusion near the beginning
- Reasoning before the conclusion
- "Who should choose which product"
- Comparison basis in bullet points
- Normalized specs
- Original evaluation score
- Sources and confirmation notes
- Price confirmation date
- Update date
- FAQ
- CTA
- Evidence and disclaimers

Avoid thin generic articles. Generated content should help readers feel confident enough to purchase.

### 4. AI Router and Provider Requirements

AI calls should be smart and safe:

- Avoid repeated calls in a short time.
- Add intervals after AI calls.
- Product API calls should wait at least 1 second after execution.
- Handle 429/rate limit/billing limit errors.
- Use provider fallback/cooldown/circuit breaker.
- If image generation fails or URL save fails, fall back to another method.
- Do not let a failed provider block the whole system forever.

Text providers discussed/used:

- OpenAI
- Gemini
- OpenRouter
- Pollinations text

Image providers discussed/used:

- Pollinations
- Hugging Face
- Cloudflare Workers AI
- OpenAI image, but OpenAI billing/limit failures should be handled gracefully

Testing mode:

- Allow disabling featured image generation during testing.
- The toggle should be visible on the article generation screen.

### 5. External Product APIs and Affiliate Links

Rakuten:

- Use the current Rakuten Ichiba Item Search API documentation.
- Known target endpoint in implementation: `IchibaItem/Search/20260401`.
- Requires Rakuten App ID and Access Key.
- Rakuten Affiliate ID should be supported.
- Use `affiliateUrl` when Rakuten Affiliate ID is configured.
- Same product's affiliate block should include Rakuten where available.

Yahoo:

- Yahoo Shopping URLs must be wrapped via ValueCommerce when SID/PID are configured.
- Yahoo App ID is not the same as ValueCommerce SID/PID.
- Yahoo ValueCommerce SID/PID settings exist.
- Raw Yahoo links should not be treated as affiliate links when SID/PID are missing.

Amazon:

- Settings exist.
- PA-API SearchItems is legacy/deprecated in the current implementation and should be fallback only.
- Future Amazon support is planned.

Affiliate rendering:

- Use Pochipp-like display style.
- Show Rakuten, Yahoo, and future Amazon shop buttons where available.
- Track clicks by shop and post/product.

### 6. Article Generation Flow

Current desired automatic flow:

1. User sets a field or keyword.
2. System researches products.
3. System gets/stores a product candidate list.
4. If the first candidate was already handled within the last month, slide to the next candidate.
5. Select a flagship/trending base product.
6. Select comparison/opponent products based on product selection settings.
7. Pick products from the same product type/category/model/price/rating group.
8. Generate the article.
9. Generate or skip featured image depending on settings.
10. Add affiliate links.
11. Add internal links, and if needed queue supporting internal-link articles.

Article Builder, Article Queue, and Autopilot should share the same automatic creation planner. Do not keep three separate versions of similar logic.

### 7. Product Research Workflow To Finish Next

Implement this feature cleanly:

- Product research
- Product list acquisition
- If the first picked product was handled within one month, slide to the next product
- Get comparison products
- Create article

Add setting:

```text
recent_product_skip_days
```

Default:

```text
30
```

Behavior:

- `30`: skip products used in Monoken posts within the last 30 days.
- `0`: disable skip.
- Clamp range, e.g. `0` to `365`.
- Check `_mae_product_a_id` and `_mae_product_b_id`.
- Count draft, pending, and published posts.
- Do not count trash/deleted posts.
- Respect manual product selections.
- If all candidates are recent, fall back gracefully to the best available product rather than stopping forever.

Add logs:

- Product research started
- Candidate list prepared
- Recent product skipped
- Comparison candidate selected
- Fallback used

### 8. Queue and Diagnostics

Article generation queue should include:

- Daily generation limit
- Scheduled generation
- Retry/failure states
- Failure reason classification:
  - API limit/billing
  - Product shortage
  - Image generation/save failure
  - Auth/API key issue
  - Network/timeout
  - AI provider issue
  - Duplicate article
  - Unknown
- Failed/low-quality/featured-image-failed article visibility
- Ability to retry/requeue/delete jobs

Diagnostics should show:

- Low-score posts
- Image failure posts
- Missing thumbnail posts
- Products without affiliate URL
- AI failures/provider status
- Click analytics
- Publish readiness checklist

### 9. Setup and Operation Readiness

Must support:

- Initial setup wizard
- API key setup
- AI/image AI setup
- Product API setup
- Cron/WP-Cron check
- Sample generation
- Public preflight checklist
- Click tracking
- PR disclosure/disclaimer
- Settings export and import
- Backup/restore
- Support log bundle
- License/update settings, but license can be off during testing
- User-facing manuals and error guides later

Xserver/WP-Cron:

- WP-Cron may work, but low-traffic sites should support server Cron execution.
- Admin UI should make Cron status visible.

### 10. SEO/AIO Requirements

Support:

- `monoken-sitemap.xml`
- robots.txt integration
- FAQ schema
- Breadcrumb schema
- ItemList schema
- Product schema
- Article schema
- Meta description helper
- Author information
- Organization information
- Updated date
- Price checked date
- Source links
- Entity-strengthening article structure
- AIO-friendly direct-answer/conclusion blocks

Articles should be structured so AI search can cite them:

- Clear answer
- Reasoning
- Evidence
- Sources
- FAQ
- Structured data
- Author/operator credibility

### 11. Japanese Localization

User-facing display should be Japanese.

Important:

- Development can remain English.
- Admin menus, labels, notices, error messages, article support blocks, and generated fallback text should be Japanese.
- Fix mojibake-like text in touched files.
- English headings like "Evidence used for this comparison", "Original evaluation score", and "Source and confirmation note" should not appear in Japanese articles.
- Generated output should not contain Markdown fences such as ```html or full HTML wrappers.

Known important Japanese article support labels:

- 比較検討の流れ
- 結論
- 比較に使った根拠
- 独自評価スコア
- 出典・確認メモ
- FAQ
- 関連記事
- 購入前チェック

### 12. Duplicate and Similar Article Guard

Existing behavior should be preserved/improved:

- Same product/keyword/article type should not generate duplicate articles.
- Duplicate guard should run before AI calls to avoid wasting usage.
- Admin notice should be Japanese and link to the existing article where possible.
- Queue/autopilot should not endlessly retry duplicates.
- Similarity/fingerprint checks should reduce repeated content.

### 13. Important Bugs Already Found and Addressed

Be aware of these prior issues:

- WordPress plugin ZIP activation failed with "plugin file does not exist" due to packaging shape.
- `/monoken-go/` returned 404 until rewrite rules/flush behavior was addressed.
- Calling `add_rewrite_rule()` too early caused fatal error because rewrite globals were not ready.
- `wp_safe_redirect()` could block external affiliate redirects, so affiliate redirect handling needed safe target logic.
- AI output included unwanted code fences, full HTML, generic model A/B text, and self-evaluation text.
- Comparison products could be unrelated, such as hair iron versus projector.
- Yahoo links were sometimes not affiliate links.
- Rakuten link enrichment was missing from product cards.
- Settings save on one settings page could wipe another settings group.
- Japanese localization was incomplete.
- Some terminal display may show mojibake even when file UTF-8 is valid; inspect file bytes/content carefully before assuming corruption.

### 14. Current Implemented Feature Areas

Expected existing implementation includes many of these:

- Product repository
- Product selector
- Creation planner
- Rakuten/Yahoo/Amazon API integration
- AI router
- Article generator
- Article queue
- VS renderer
- Click tracker
- SEO/schema/sitemap
- Disclosure
- Autopilot
- Ops/checks/backup/support logs
- Update client settings
- Admin UI
- Localization layer

Inspect source to verify exact current behavior.

## Files Likely To Touch For The Next Work

- `includes/class-mae-product-selector.php`
- `includes/class-mae-creation-planner.php`
- `includes/class-mae-article-generator.php`
- `includes/class-mae-article-queue.php`
- `includes/class-mae-admin.php`
- `includes/class-mae-i18n.php`
- `includes/class-mae-settings.php`
- `includes/class-mae-install.php`
- `includes/class-mae-affiliate-apis.php`
- `monoken-affiliate-engine.php`
- `README.md`
- `assets/css/admin.css`
- `assets/css/front.css`

## Acceptance Criteria For The Next Completed Package

- Version bumped to `0.15.0` if starting from `0.14.2`.
- ZIP package created in `outputs/`.
- Product research flow works from field/keyword.
- Article Builder, Article Queue, and Autopilot share the same planner/selector.
- Recent-product skip works for the configured day window.
- Manual selection remains respected.
- Comparison product remains the same product type when possible.
- Fallback behavior prevents endless failure when all candidates are recent.
- Admin UI explains the automatic creation flow in Japanese.
- Major admin labels/notices are Japanese.
- Article support blocks are natural Japanese.
- No obvious mojibake in touched files.
- Duplicate checks happen before AI calls.
- Rakuten/Yahoo affiliate behavior is preserved.
- PHP lint is run if PHP is available.
- If PHP is unavailable, mention it clearly and perform text-level checks.

## Verification Commands

Run what is available:

```bash
php -l work/monoken-affiliate-engine/monoken-affiliate-engine.php
php -l work/monoken-affiliate-engine/includes/class-mae-product-selector.php
php -l work/monoken-affiliate-engine/includes/class-mae-creation-planner.php
php -l work/monoken-affiliate-engine/includes/class-mae-admin.php
php -l work/monoken-affiliate-engine/includes/class-mae-article-generator.php
php -l work/monoken-affiliate-engine/includes/class-mae-article-queue.php
php -l work/monoken-affiliate-engine/includes/class-mae-i18n.php
php -l work/monoken-affiliate-engine/includes/class-mae-settings.php
php -l work/monoken-affiliate-engine/includes/class-mae-install.php
```

Search checks:

```bash
rg -n "0\\.14\\.2|0\\.15\\.0" work/monoken-affiliate-engine
rg -n "Evidence used for this comparison|Original evaluation score|Source and confirmation note|```html|```" work/monoken-affiliate-engine
rg -n "No comparison products were found|No flagship product was found|Automatic creation flow|Recent product skip days" work/monoken-affiliate-engine
```

ZIP check:

```text
outputs/monoken-affiliate-engine-v0.15.0.zip
```

It must contain:

```text
monoken-affiliate-engine/
  monoken-affiliate-engine.php
  includes/
  assets/
  docs/
  README.md
```

## Final Handoff Format

Reply in Japanese with:

- What changed
- ZIP path
- What was verified
- What could not be verified
- Any remaining risks or next recommended tests

