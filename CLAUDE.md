# Claude Code Instructions for Monoken

You are working on Monoken Affiliate Engine, a WordPress affiliate automation plugin.

## Project Location

- Main plugin source: `work/monoken-affiliate-engine`
- Main plugin file: `work/monoken-affiliate-engine/monoken-affiliate-engine.php`
- Build outputs: `outputs/`

## Role

Act as a senior WordPress plugin engineer, product designer, and debugger.

Own the task from code inspection through implementation, verification, packaging, and a concise handoff. Do not stop at a proposal when the user asks for implementation.

## Product Context

Monoken Affiliate Engine generates affiliate comparison articles with:

- Product API search and local product storage
- Automatic product selection
- VS comparison articles
- AI-assisted writing
- Featured image generation
- Quality checks and rewrite support
- Affiliate links for Rakuten, Yahoo/ValueCommerce, and future Amazon support
- Click tracking
- SEO/AIO schema output
- Article queue and autopilot operation

The product is intended for multi-site affiliate operations. Generated sites and articles must avoid looking duplicated or templated.

## Current Versioning Rule

- Do not change the major version unless the user explicitly requests it.
- Feature additions: bump the minor version.
- Bug fixes only: bump the patch version.
- Keep these in sync:
  - Plugin header `Version`
  - `MAE_VERSION`
  - `README.md` heading
  - Output ZIP filename

Current known version before this task: `0.14.2`.
The product-research workflow requested here is a feature addition, so the expected next version is `0.15.0` unless the source already has a newer version.

## Coding Standards

- Development identifiers, class names, method names, and internal keys should remain English.
- User-facing admin UI, notices, article support blocks, labels, and error messages should display in Japanese.
- Use WordPress APIs and escaping/sanitization consistently:
  - `sanitize_text_field`, `sanitize_key`, `sanitize_textarea_field`
  - `esc_html`, `esc_attr`, `esc_url`
  - `$wpdb->prepare()` for SQL with variables
  - `wp_safe_redirect()` only for internal admin redirects
- Keep existing architecture:
  - Product selection logic belongs mainly in `MAE_Product_Selector`
  - Planning logic belongs mainly in `MAE_Creation_Planner`
  - Queue behavior belongs mainly in `MAE_Article_Queue`
  - Admin UI belongs in `MAE_Admin`
  - Settings registration belongs in `MAE_Settings`
  - Defaults and DB upgrades belong in `MAE_Install`
  - Translations belong in `MAE_I18n`
- Do not duplicate similar logic across Article Builder, Article Queue, and Autopilot. They should share the same planner/selector path.
- Do not remove unrelated user changes.
- Avoid broad refactors unless required to make the feature reliable.

## WordPress Safety

- Never create fatal errors during plugin load.
- Do not call rewrite functions before WordPress rewrite globals are available.
- Avoid external API calls inside plugin activation.
- Product API calls should respect existing rate-limit/interval mechanisms.
- AI calls should not be made when the article can be skipped due to duplication or a known invalid state.

## Japanese Display

The source may contain English strings that are translated through `MAE_I18n`. When adding new user-facing strings:

- Prefer adding English source strings plus Japanese mappings in `MAE_I18n`.
- For article body/support blocks generated directly in PHP, output natural Japanese directly.
- Check for mojibake-like strings in files you touch. Use UTF-8-safe reading/editing.

## Verification Expectations

Run what is available in the environment:

- `php -l` on touched PHP files if PHP is installed.
- Search for old version strings.
- Search for obvious mojibake in touched files.
- Check ZIP contents after packaging.

If PHP is not installed, say so in the final handoff and provide the checks that were performed instead.

## Packaging

Create a WordPress-installable ZIP with this shape:

```text
monoken-affiliate-engine/
  monoken-affiliate-engine.php
  includes/
  assets/
  docs/
  README.md
```

Output ZIP naming example:

```text
outputs/monoken-affiliate-engine-v0.15.0.zip
```

