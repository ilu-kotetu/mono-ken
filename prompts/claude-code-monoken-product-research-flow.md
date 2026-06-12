# Claude Code Prompt: Monoken Product Research Flow

You are Claude Code working in this workspace:

```text
C:\Users\iluko\Documents\Codex\2026-06-08\chatgpt
```

Read `CLAUDE.md` first and follow it.

Important: this workspace may already contain partial edits from the current discussion. Inspect the current source and local diffs first. Complete, clean up, or adjust the existing work instead of blindly duplicating logic or reverting unrelated changes.

## Goal

Implement and package the next Monoken Affiliate Engine feature:

- Product research
- Product list acquisition
- If the first picked product was already used within the last month, slide to the next candidate
- Get comparison/opponent products
- Create the article
- Continue Japanese localization of admin UI, notices, and generated article support text

This should make Article Builder, Article Queue, and Autopilot use the same automatic creation logic.

## Expected Version

This is a feature addition. Unless the source already has a newer version, bump from `0.14.2` to `0.15.0`.

Update:

- `work/monoken-affiliate-engine/monoken-affiliate-engine.php`
- `work/monoken-affiliate-engine/README.md`
- output ZIP name

## Functional Requirements

### 1. Unified Automatic Article Flow

When the user enters a field or keyword, or when Autopilot runs from configured settings, the system should follow this flow:

1. Research products from the configured field/keyword.
2. Get/store a product candidate list from available product APIs and local products.
3. Sort candidates by affiliate usefulness and buyer relevance:
   - review count
   - rating
   - price availability
   - image availability
   - affiliate URL availability
   - Rakuten trend/review/update signals where available
4. Check whether the candidate product has already been used in a Monoken article within the last month.
5. If it was used within the last month, skip it and slide to the next candidate.
6. After choosing the base product, get comparison products using the selected mode:
   - same product type
   - model variants
   - same category
   - similar price range
   - top rated
7. Avoid comparison products that were also used within the recent skip window when possible.
8. If all good candidates were recently used, fall back to the best available product rather than stopping forever.
9. Generate the article only after a valid base/comparison plan is decided.

### 2. Shared Logic

Do not implement separate product selection behavior in each screen.

Use one shared path:

- Article Builder
- Article Queue
- Autopilot

The planner/selector should be the single source of truth.

Likely files:

- `includes/class-mae-creation-planner.php`
- `includes/class-mae-product-selector.php`
- `includes/class-mae-article-queue.php`
- `includes/class-mae-admin.php`
- `includes/class-mae-settings.php`
- `includes/class-mae-install.php`

### 3. Recent Product Skip Setting

Add a setting:

```text
recent_product_skip_days
```

Default:

```text
30
```

Behavior:

- `30` means skip products used in Monoken posts within the last 30 days.
- `0` disables this skip.
- Clamp to a sensible range, for example `0` to `365`.
- Use posts with `_mae_product_a_id` and `_mae_product_b_id`.
- Count draft, pending, and published Monoken posts.
- Do not count deleted/trash posts.

Expose this setting in the Autopilot/settings screen with a Japanese label.

### 4. Product Research and Candidate Logging

Add lightweight logs so the user can understand what happened:

- Product research started
- Candidate list prepared
- Recent product skipped
- Comparison candidate selected
- Fallback used because all candidates were recent or no fresh candidates had a comparison product

Use existing Monoken logging style.

Do not log sensitive API keys.

### 5. Japanese Admin UI

Continue Japanese localization.

At minimum, ensure these concepts display in Japanese:

- Product research
- Product candidate list
- Recently used product skip
- Recent product skip days
- Slide to next candidate
- Comparison product acquisition
- Article creation
- Automatic creation flow
- No comparison products were found
- No flagship product was found
- Queue messages related to skipped/reused/failed jobs

If the project uses `MAE_I18n`, add English source strings and Japanese mappings there.

### 6. Article Text Quality and Japanese Support Blocks

Check generated/fallback article support blocks in `MAE_Article_Generator`.

Make sure direct PHP support blocks are natural Japanese, especially:

- Conclusion
- Comparison reasoning flow
- Evidence used for comparison
- Original evaluation score
- Source and confirmation note
- FAQ
- Related articles
- Generated title/default keyword

Remove or fix mojibake-like text in files you touch.

Do not make generic English headings appear in Japanese articles.

### 7. Duplicate/AI Usage Safety

Keep or improve the existing duplicate guard:

- If the same product/keyword combination already exists, do not call AI again.
- Show a Japanese notice and link to the existing article when possible.
- Queue/autopilot should not endlessly retry a duplicate generation.

### 8. Affiliate and Product-Type Safety

Do not weaken existing affiliate link behavior:

- Rakuten affiliate links should use `affiliateUrl` when configured.
- Yahoo links should wrap through ValueCommerce when SID/PID are configured.
- Product comparison should avoid mixing unrelated product types, such as a hair iron versus a projector.

## Acceptance Criteria

After implementation:

- Article Builder with only a field/keyword can create a planned article.
- Article Queue with only a field/keyword can enqueue/process through the same plan.
- Autopilot uses the same plan.
- If the highest-scored product was used in the last 30 days, the next valid product is selected.
- If no fresh products are valid, the system falls back gracefully rather than failing forever.
- Product comparison candidates remain the same product type where possible.
- Admin UI shows the automatic creation flow in Japanese.
- User-facing notices and major labels are Japanese.
- Article support blocks do not contain mojibake or English headings.
- Version is bumped correctly.
- A ZIP package is created under `outputs/`.

## Suggested Implementation Notes

Prefer these shapes, but adapt to the current source:

- Add `MAE_Product_Selector::recently_used_product_ids($days = null)`.
- Add `MAE_Product_Selector::recent_skip_days()`.
- Let `best_pair_for_field()` build candidates, filter fresh candidates first, then fall back to all candidates.
- Let `select_comparison()` and `candidates()` accept optional excluded product IDs.
- Add setting registration/default/UI for `recent_product_skip_days`.
- Add a small admin note showing the flow:
  - 商品リサーチ
  - 商品候補一覧の取得
  - 直近使用商品のスキップ
  - 対抗商品の取得
  - 記事作成
- Keep manual product selections respected. Recent-product skipping should primarily affect automatic selection.

## Verification

Run available checks:

```bash
php -l work/monoken-affiliate-engine/monoken-affiliate-engine.php
php -l work/monoken-affiliate-engine/includes/class-mae-product-selector.php
php -l work/monoken-affiliate-engine/includes/class-mae-creation-planner.php
php -l work/monoken-affiliate-engine/includes/class-mae-admin.php
php -l work/monoken-affiliate-engine/includes/class-mae-article-generator.php
php -l work/monoken-affiliate-engine/includes/class-mae-settings.php
php -l work/monoken-affiliate-engine/includes/class-mae-install.php
```

If PHP is unavailable, perform text-level checks and mention that PHP lint could not be run.

Also run searches:

```bash
rg -n "0\\.14\\.2|0\\.15\\.0" work/monoken-affiliate-engine
rg -n "Evidence used for this comparison|Original evaluation score|Source and confirmation note|No comparison products were found|No flagship product was found" work/monoken-affiliate-engine
```

Finally, create:

```text
outputs/monoken-affiliate-engine-v0.15.0.zip
```

Make sure the ZIP contains the `monoken-affiliate-engine/` folder at the root.

## Final Handoff

In Japanese, summarize:

- What changed
- Which ZIP was created
- What was verified
- Any checks that could not be run
