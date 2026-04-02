---
framework: "wordpress"
version: "5.x, 6.x"
---

# Framework Profile: WordPress 5.x / 6.x

## Architecture

### Standard File Structure

```
project-root/
├── wp-config.php              # Database credentials, salts, debug flags, custom constants
├── wp-settings.php            # Bootstrap — loads everything (DO NOT EDIT)
├── wp-load.php                # Entry point for custom scripts that need WP context
├── wp-cron.php                # Pseudo-cron (triggered on page loads)
├── wp-admin/                  # Admin dashboard (core — never modify)
├── wp-includes/               # Core libraries (core — never modify)
├── wp-content/
│   ├── themes/
│   │   ├── {active-theme}/    # The active theme — primary customization surface
│   │   │   ├── style.css      # Theme metadata header + styles
│   │   │   ├── functions.php  # Theme bootstrapper — hooks, filters, includes
│   │   │   ├── index.php      # Fallback template
│   │   │   ├── header.php     # <head> and opening markup
│   │   │   ├── footer.php     # Closing markup, wp_footer()
│   │   │   ├── single.php     # Single post template
│   │   │   ├── page.php       # Page template
│   │   │   ├── archive.php    # Archive listing
│   │   │   ├── search.php     # Search results
│   │   │   ├── 404.php        # Not found
│   │   │   ├── sidebar.php    # Widget area
│   │   │   ├── template-parts/# Reusable partial templates
│   │   │   ├── inc/           # PHP includes (custom post types, customizer, etc.)
│   │   │   └── assets/        # CSS, JS, images
│   │   └── {parent-theme}/    # Parent theme if child theme is active
│   ├── plugins/
│   │   ├── {plugin-slug}/     # Each plugin is self-contained
│   │   │   ├── {plugin-slug}.php  # Main plugin file (metadata header)
│   │   │   └── ...
│   │   └── ...
│   ├── mu-plugins/            # Must-use plugins — auto-loaded, no activation needed
│   ├── uploads/               # Media library (wp-content/uploads/YYYY/MM/)
│   ├── languages/             # Translation files (.mo, .po)
│   ├── upgrade/               # Core update staging
│   └── cache/                 # Object cache files (if caching plugin active)
├── .htaccess                  # Rewrite rules (Apache) — pretty permalinks
└── composer.json              # Optional — Composer-managed WP (Bedrock, Roots)
```

### Core Design Patterns

- **Hook System (Actions & Filters):** The fundamental extension mechanism. Actions fire at specific points (`do_action()`). Filters modify data in transit (`apply_filters()`). All customization should use hooks — never patch core files.
- **Template Hierarchy:** WordPress selects templates by specificity cascade: `single-{post_type}-{slug}.php` → `single-{post_type}.php` → `single.php` → `singular.php` → `index.php`. Understanding this is essential for tracing which file renders a given URL.
- **The Loop:** `while ( have_posts() ) : the_post();` — the standard pattern for iterating over query results. Custom queries use `WP_Query`.
- **Plugin API:** Plugins register hooks in their main file. Activation/deactivation hooks handle setup/teardown (creating tables, scheduling cron, flushing rewrites).
- **Shortcode API:** `[shortcode attr="val"]content[/shortcode]` — registered via `add_shortcode()`. Commonly used by page builders, forms plugins, and custom functionality.
- **Widget API:** Widgets registered via `register_widget()`, displayed in sidebars defined by `register_sidebar()`.
- **REST API:** Available since WP 4.7 at `/wp-json/wp/v2/`. Custom endpoints via `register_rest_route()`. Block editor (Gutenberg) depends on this.
- **Options API:** Key-value store via `get_option()` / `update_option()`. Used for plugin settings, theme mods, and site configuration. Stored in `wp_options` table.
- **Transients API:** Cached key-value with expiration. `get_transient()` / `set_transient()`. Uses object cache if available, otherwise `wp_options`.

### Template/Theme System

- **Child Themes:** Override parent theme templates by placing same-named files in child theme directory. `functions.php` is additive (both load). `style.css` requires explicit `@import` or enqueue of parent styles.
- **Template Tags:** Functions like `the_title()`, `the_content()`, `the_permalink()` that echo data inside The Loop.
- **Conditional Tags:** `is_home()`, `is_single()`, `is_page()`, `is_archive()`, `is_admin()` — used for conditional logic in templates and `functions.php`.
- **Block Themes (FSE):** WordPress 5.9+ supports `theme.json` and block templates in `templates/` and `parts/` directories. HTML-based templates with block markup.

### Routing and URL Structure

- **Pretty Permalinks:** Configured in Settings → Permalinks. Common structures: `/%postname%/`, `/%category%/%postname%/`. Rewrite rules stored in `wp_options` (option `rewrite_rules`).
- **Rewrite API:** Custom post types and taxonomies register their own rewrite rules. `flush_rewrite_rules()` regenerates `.htaccess` / rewrite rules.
- **Query Vars:** URL parameters parsed by `WP::parse_request()` into `WP_Query` arguments. Custom query vars via `query_vars` filter.
- **Admin Routes:** `wp-admin/admin.php?page={slug}` for custom admin pages. AJAX via `wp-admin/admin-ajax.php` (action parameter dispatches to hooks).

## Common Patterns

### Theme/Module Customization Approaches

- **functions.php as a bootstrapper:** In well-structured themes, `functions.php` includes files from `inc/` directory rather than containing all logic directly. In legacy code, it often becomes a monolithic file with hundreds/thousands of lines.
- **Custom Post Types (CPTs):** Registered via `register_post_type()` in `functions.php` or a plugin. Common CPTs: portfolio, testimonials, events, products. Check for CPT UI or custom code.
- **Custom Taxonomies:** Registered via `register_taxonomy()`. Often paired with CPTs for categorization.
- **Custom Fields / Meta:** Via `add_post_meta()` / `get_post_meta()` or ACF (Advanced Custom Fields) plugin. ACF stores field definitions in `wp_posts` (acf-field-group post type) and values in `wp_postmeta`.
- **Theme Customizer:** Panels/sections/controls registered via `customize_register` action. Settings stored as theme mods (`get_theme_mod()`).

### Content Types and Data Modeling

- **Posts & Pages:** Core content types. Posts have categories and tags. Pages are hierarchical (parent/child).
- **Custom Post Types:** Extend content model. Each CPT has its own archive and single templates.
- **Menus:** Managed via `wp_nav_menu()`. Menu items stored in `wp_posts` (post type `nav_menu_item`), relationships in `wp_term_relationships`.
- **Widgets:** Stored as serialized arrays in `wp_options` (option `widget_{id_base}`). Widget positions stored in `sidebars_widgets` option.

### Page Builders / Layout Systems

- **Elementor:** Stores layout data as serialized post meta (`_elementor_data`). Renders via its own template override. CSS generated per-page in `wp-content/uploads/elementor/css/`. Key indicators: `elementor` directory in plugins, `_elementor_edit_mode` post meta.
- **DIVI (Elegant Themes):** Uses shortcode-based layout stored in `post_content`. Builder data in shortcodes like `[et_pb_section]`, `[et_pb_row]`, `[et_pb_column]`, `[et_pb_text]`. Key indicators: `Divi` theme or `divi-builder` plugin, `et_pb_` prefixed shortcodes in post content.
- **WPBakery (Visual Composer):** Shortcode-based like DIVI. Uses `[vc_row]`, `[vc_column]`, etc. Key indicator: `js_composer` plugin directory.
- **Gutenberg (Block Editor):** Core since WP 5.0. Block markup stored as HTML comments in `post_content`: `<!-- wp:paragraph -->`. Custom blocks registered via PHP and JS.
- **ACF Flexible Content:** Layout builder via ACF's flexible content field type. Data stored as post meta with a specific naming convention.

### Custom Code Entry Points

- **functions.php:** Primary entry point for theme customizations. Hooks, filters, includes, enqueues.
- **mu-plugins/:** Auto-loaded files for site-wide customizations that survive theme changes. Often used for custom post types, security hardening, or agency-specific utilities.
- **Plugin main file:** `{plugin-slug}.php` with plugin header comment. Hooks into `plugins_loaded`, `init`, or `admin_init`.
- **wp-config.php:** Constants for debugging (`WP_DEBUG`), memory limits, custom directories, multisite, and security keys.
- **Custom page templates:** PHP files with `/* Template Name: My Template */` header in the theme directory.

## Known Pitfalls

### Common Sources of Side Effects

- **Hook priority collisions:** Multiple plugins/theme hooking the same action at default priority (10). Changing priority of one hook can break others. Audit all hooks on critical actions like `init`, `wp_head`, `wp_footer`, `wp_enqueue_scripts`.
- **Global state pollution:** WordPress relies heavily on globals (`$post`, `$wp_query`, `$wpdb`). Custom queries that don't call `wp_reset_postdata()` corrupt the main loop. Legacy code often modifies globals directly.
- **Serialized data in options:** Plugin settings stored as serialized PHP arrays in `wp_options`. Find-and-replace on database (e.g., domain migration) breaks serialized data. Use WP-CLI `search-replace` which handles serialization.
- **Shortcode rendering order:** Shortcodes execute during `the_content` filter (priority 11). Filters added at lower priority may receive raw shortcode tags instead of rendered HTML.
- **Auto-loading of mu-plugins:** Files in `mu-plugins/` load alphabetically, before regular plugins. No activation/deactivation hooks. Subdirectories require a loader file.
- **Object cache conflicts:** Multiple caching plugins (W3 Total Cache, WP Super Cache, Redis) can conflict. Check for `wp-content/object-cache.php` drop-in and `wp-content/advanced-cache.php`.

### Performance Traps

- **Autoloaded options:** Options with `autoload = 'yes'` are loaded on EVERY page request. Plugins that store large blobs (serialized arrays, CSS) in autoloaded options cause memory and query overhead. Query: `SELECT * FROM wp_options WHERE autoload = 'yes'`.
- **Unindexed meta queries:** `WP_Query` with `meta_query` on large sites is extremely slow without custom indexes. The `wp_postmeta` table only has an index on `post_id` and `meta_key` — not `meta_value`.
- **N+1 queries in loops:** Custom loops calling `get_post_meta()` per iteration instead of using `update_meta_cache()` or `'update_post_meta_cache' => true` in query args.
- **Excessive cron jobs:** Plugins scheduling WP-Cron events that pile up. Check `_transient_doing_cron` and `cron` option for scheduled events. Use WP-CLI `wp cron event list`.
- **Admin-ajax bottleneck:** All frontend AJAX goes through `admin-ajax.php`, which bootstraps the entire WP stack. High-traffic AJAX should use REST API or custom lightweight endpoints.
- **Unoptimized images:** Uploads stored at original resolution. Missing srcset generation. Check `wp_get_attachment_image_srcset()` usage and image size registration.

### Security Anti-Patterns

- **Direct database queries without preparation:** Using `$wpdb->query("SELECT ... WHERE id = $id")` instead of `$wpdb->prepare()`. SQL injection risk.
- **Unescaped output:** Echoing user input or database values without `esc_html()`, `esc_attr()`, `esc_url()`, or `wp_kses()`. XSS risk.
- **Missing nonce verification:** Form handlers and AJAX endpoints without `wp_verify_nonce()` or `check_ajax_referer()`. CSRF risk.
- **Capability checks missing:** Admin functions without `current_user_can()` checks. Privilege escalation risk.
- **File upload without validation:** Custom upload handlers not using `wp_check_filetype()` or allowing dangerous MIME types.
- **Debug mode in production:** `WP_DEBUG = true` and `WP_DEBUG_DISPLAY = true` exposing error details. Check `wp-config.php`.
- **Exposed wp-config.php:** Web-accessible `wp-config.php` backup files (`.bak`, `.old`, `~`).
- **Default table prefix:** Using `wp_` prefix increases vulnerability to automated SQL injection attacks targeting known table names.
- **XML-RPC enabled:** `xmlrpc.php` allows brute-force amplification attacks. Should be disabled if not needed (Jetpack requires it).

### Dependency Conflicts and Plugin Incompatibilities

- **jQuery version conflicts:** WordPress bundles jQuery in noConflict mode. Plugins that enqueue their own jQuery version or assume `$` is available cause conflicts. Check `wp_deregister_script('jquery')`.
- **PHP version requirements:** WP 6.x requires PHP 7.4+. Legacy plugins may use deprecated PHP functions (`each()`, `create_function()`, `mysql_*`).
- **REST API conflicts:** Plugins that filter REST responses globally can break the block editor. Check filters on `rest_pre_serve_request` and `rest_post_dispatch`.
- **Classic Editor vs. Gutenberg:** Some plugins are incompatible with the block editor and require Classic Editor plugin. Check `classic-editor` plugin or `use_block_editor_for_post_type` filter.
- **WooCommerce conflicts:** WooCommerce overrides template hierarchy and adds its own endpoints. Themes must declare `woocommerce_support`. Custom checkout code is fragile.

## CLI Tools

### WP-CLI — Primary CLI Tool

```bash
# Core information
wp core version                          # Show WordPress version
wp core check-update                     # Check for core updates
wp core verify-checksums                 # Verify core file integrity

# Database
wp db export backup.sql                  # Export database
wp db import backup.sql                  # Import database
wp db query "SELECT ..."                 # Run SQL query
wp db search "string"                    # Search all tables for a string

# Search and replace (serialization-safe)
wp search-replace 'old-domain.com' 'new-domain.com' --dry-run
wp search-replace 'old-domain.com' 'new-domain.com' --all-tables

# Plugins
wp plugin list                           # List all plugins with status
wp plugin list --status=active           # Active plugins only
wp plugin activate {slug}                # Activate a plugin
wp plugin deactivate {slug}              # Deactivate a plugin
wp plugin verify-checksums --all         # Verify plugin file integrity

# Themes
wp theme list                            # List all themes
wp theme activate {slug}                 # Activate a theme

# Options (configuration)
wp option get siteurl                    # Show site URL
wp option list --search="*transient*"    # Find transients
wp option list --autoload=yes --format=csv  # List autoloaded options

# Cron
wp cron event list                       # List scheduled cron events
wp cron event run --all                  # Execute all due cron events

# Cache
wp cache flush                           # Flush object cache
wp transient delete --all                # Delete all transients

# Users
wp user list                             # List all users
wp user create admin admin@example.com --role=administrator  # Create admin

# Posts and content
wp post list --post_type=page            # List all pages
wp post list --post_type={cpt}           # List custom post type entries
wp post meta list {post_id}              # List post meta for a specific post

# Maintenance
wp rewrite flush                         # Regenerate rewrite rules / .htaccess
wp core update-db                        # Update database schema after core update
```

### DDEV Integration Commands

```bash
# Start/stop environment
ddev start                               # Start containers
ddev stop                                # Stop containers
ddev restart                             # Restart containers

# WP-CLI through DDEV
ddev wp plugin list                      # Run WP-CLI inside container
ddev wp db export                        # Export from container database
ddev wp search-replace ...               # Serialization-safe search-replace

# Database access
ddev mysql                               # MySQL CLI inside container
ddev export-db --file=backup.sql.gz      # Export database
ddev import-db --file=backup.sql.gz      # Import database

# Composer (if applicable)
ddev composer install                    # Install dependencies
ddev composer update                     # Update dependencies

# SSH into container
ddev ssh                                 # Enter web container
```

### Database Inspection Commands

```bash
# Table sizes and overview
wp db query "SHOW TABLE STATUS"
wp db query "SELECT table_name, table_rows, data_length FROM information_schema.tables WHERE table_schema = DATABASE() ORDER BY data_length DESC"

# Autoloaded options analysis (performance)
wp db query "SELECT option_name, LENGTH(option_value) AS size FROM wp_options WHERE autoload = 'yes' ORDER BY size DESC LIMIT 20"

# Find large postmeta entries
wp db query "SELECT post_id, meta_key, LENGTH(meta_value) AS size FROM wp_postmeta ORDER BY size DESC LIMIT 20"

# Count posts by type
wp db query "SELECT post_type, post_status, COUNT(*) as count FROM wp_posts GROUP BY post_type, post_status ORDER BY count DESC"

# Find orphaned postmeta
wp db query "SELECT COUNT(*) FROM wp_postmeta pm LEFT JOIN wp_posts p ON pm.post_id = p.ID WHERE p.ID IS NULL"
```

### Cache and Configuration Management

```bash
# Object cache
wp cache flush
wp cache type                            # Show active cache backend

# Transients (temporary cached data)
wp transient delete --all
wp transient list

# Config management
wp config list                           # Show wp-config.php constants
wp config get WP_DEBUG                   # Check specific constant
wp config set WP_DEBUG true --raw        # Set constant

# Rewrite rules
wp rewrite list                          # Show all rewrite rules
wp rewrite flush                         # Regenerate rules
```

## Test Patterns

### Critical Pages/Routes to Cover with E2E Tests

- **Homepage** — Often the most customized. Test layout, hero section, recent posts/products rendering.
- **Single post** — `/{post-slug}/` — Verify content, sidebar, comments, related posts.
- **Single page** — `/about/`, `/contact/` — Static pages, often with forms or page builder layouts.
- **Archive pages** — `/category/{slug}/`, `/tag/{slug}/`, `/author/{slug}/` — Pagination, filtering.
- **Search results** — `/?s={query}` — Search form and results rendering.
- **Custom post type archives** — `/{cpt-slug}/` — If registered with `has_archive`.
- **Custom post type singles** — `/{cpt-slug}/{slug}/` — Template-specific rendering.
- **Login/registration** — `/wp-login.php`, `/wp-login.php?action=register` — Authentication flow.
- **Admin dashboard** — `/wp-admin/` — Post listing, plugin pages, settings pages (if customized).
- **WooCommerce (if present):** Shop (`/shop/`), single product, cart (`/cart/`), checkout (`/checkout/`), my account (`/my-account/`).
- **Contact forms** — Test submission flow (Contact Form 7, Gravity Forms, WPForms).
- **404 page** — `/nonexistent-url/` — Custom 404 template rendering.

### Components That Benefit from Snapshot Testing

- **Header/footer output** — `wp_head()` and `wp_footer()` output (scripts, styles, meta tags). Detects unintended additions/removals by plugins.
- **Navigation menus** — `wp_nav_menu()` HTML output per menu location.
- **Widget areas/sidebars** — `dynamic_sidebar()` output for each registered sidebar.
- **Shortcode output** — Render each registered shortcode and snapshot the HTML.
- **REST API responses** — `/wp-json/wp/v2/posts`, `/wp-json/wp/v2/pages` — Schema and field presence.
- **Page builder rendered content** — Full rendered output of Elementor/DIVI pages (captures layout regressions).
- **wp-config.php constants** — Snapshot all defined WP constants for configuration drift detection.

### Integration Points to Monitor

- **Plugin activation/deactivation** — Side effects: database table creation/deletion, option changes, cron scheduling.
- **Theme switching** — Widget assignments, menu locations, theme mods are theme-specific and may be lost.
- **Core updates** — Database schema changes (`wp core update-db`). Check for deprecated function usage.
- **Plugin updates** — Settings migration, database schema changes, API changes.
- **External API calls** — Plugins calling external services (analytics, email, payment). Monitor with `pre_http_request` filter.
- **Cron execution** — Monitor `wp_schedule_event()` registrations and `wp_cron` execution timing.

### Framework-Specific Test Utilities

- **WP_UnitTestCase:** WordPress PHPUnit test base class. Provides factory methods for creating posts, users, terms in tests.
- **WP Test Utils:** `wp_set_current_user()`, `set_current_screen()`, `go_to()` for simulating request context.
- **WP-CLI test helpers:** `wp scaffold plugin-tests {slug}` generates PHPUnit bootstrap and config for plugin testing.
- **WP Browser (Codeception):** WordPress-specific Codeception module for acceptance, functional, and integration testing. `WPWebDriver`, `WPDb` modules.
- **Playwright/Cypress for E2E:** No WP-specific framework, but use WP-CLI to seed test data before test runs and reset after.

## Database

### Key Tables and Their Purposes

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| `wp_posts` | All content: posts, pages, CPTs, attachments, menu items, revisions | `ID`, `post_type`, `post_status`, `post_content`, `post_title`, `post_parent` |
| `wp_postmeta` | Post metadata (custom fields, ACF, Elementor data, SEO) | `meta_id`, `post_id`, `meta_key`, `meta_value` |
| `wp_options` | Site configuration, plugin settings, transients, widget assignments | `option_id`, `option_name`, `option_value`, `autoload` |
| `wp_users` | User accounts | `ID`, `user_login`, `user_email`, `user_pass` |
| `wp_usermeta` | User metadata (capabilities, preferences) | `umeta_id`, `user_id`, `meta_key`, `meta_value` |
| `wp_terms` | Taxonomy terms (categories, tags, custom taxonomies) | `term_id`, `name`, `slug` |
| `wp_term_taxonomy` | Term-taxonomy relationships (which taxonomy a term belongs to) | `term_taxonomy_id`, `term_id`, `taxonomy`, `parent`, `count` |
| `wp_term_relationships` | Object-term associations (which posts have which terms) | `object_id`, `term_taxonomy_id` |
| `wp_comments` | Comments on posts | `comment_ID`, `comment_post_ID`, `comment_content`, `comment_approved` |
| `wp_commentmeta` | Comment metadata | `meta_id`, `comment_id`, `meta_key`, `meta_value` |
| `wp_links` | Blogroll links (deprecated but table remains) | `link_id`, `link_url`, `link_name` |

### Important Relationships to Understand

```
wp_posts ←——(post_id)——→ wp_postmeta          # 1:N — post to its custom fields
wp_posts ←——(object_id)——→ wp_term_relationships ——→ wp_term_taxonomy ——→ wp_terms
                                                # M:N — posts to taxonomy terms
wp_posts ←——(post_parent)——→ wp_posts          # Self-referential — page hierarchy, revisions, attachments
wp_users ←——(user_id)——→ wp_usermeta           # 1:N — user to capabilities, preferences
wp_posts ←——(comment_post_ID)——→ wp_comments   # 1:N — post to its comments
wp_comments ←——(comment_id)——→ wp_commentmeta  # 1:N — comment to its metadata
```

- **Revisions** are `wp_posts` rows with `post_type = 'revision'` and `post_parent` pointing to the original post.
- **Attachments** are `wp_posts` rows with `post_type = 'attachment'` and `post_parent` pointing to the post they were uploaded to.
- **Menu items** are `wp_posts` rows with `post_type = 'nav_menu_item'`, linked to menu terms via `wp_term_relationships`.

### Configuration Storage Patterns

- **Site-wide settings:** `wp_options` with known option names (`siteurl`, `blogname`, `permalink_structure`, `active_plugins`, `template`, `stylesheet`).
- **Plugin settings:** `wp_options` with plugin-specific prefix (`wpseo_*` for Yoast, `elementor_*` for Elementor, `woocommerce_*` for WooCommerce). Often serialized arrays.
- **Theme settings:** `wp_options` with `theme_mods_{theme_slug}` key. Serialized array of Customizer settings.
- **Widget settings:** `wp_options` with `widget_{widget_id_base}` key. Serialized array per widget type. Sidebar assignments in `sidebars_widgets` option.
- **Transients (cached data):** `wp_options` with `_transient_{name}` and `_transient_timeout_{name}` keys. Or in object cache if available.
- **Cron schedule:** `wp_options` with `cron` key. Serialized array of all scheduled events with timestamps.

### Content Storage Patterns

- **Post content:** Stored as HTML (or block markup for Gutenberg) in `wp_posts.post_content`. Page builders store additional data in `wp_postmeta` (Elementor: `_elementor_data` as JSON; DIVI: shortcodes inline in `post_content`).
- **Custom fields:** `wp_postmeta` rows with descriptive `meta_key` values. ACF stores field definitions as `acf-field-group` and `acf-field` post types, values in `wp_postmeta` with field name as `meta_key` and an additional `_` prefixed key for the field reference.
- **Media/attachments:** File path stored in `wp_posts.guid` and `_wp_attached_file` meta. Image sizes registered in `_wp_attachment_metadata` meta (serialized array with dimensions, file paths).
- **User content:** User-specific data (display name, bio) in `wp_users` and `wp_usermeta`. Capabilities stored as serialized array in `wp_usermeta` with key `wp_{prefix}_capabilities`.
