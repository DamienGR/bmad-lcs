---
framework: "drupal"
version: "7, 9, 10"
---

# Framework Profile: Drupal 7 / 9 / 10

## Architecture

### Standard File Structure

#### Drupal 9/10 (Composer-based)

```
project-root/
├── composer.json                # Project dependencies (drupal/core, contrib modules)
├── composer.lock                # Locked dependency versions
├── web/                         # Docroot (webroot)
│   ├── index.php                # Front controller
│   ├── core/                    # Drupal core (managed by Composer — never modify)
│   │   ├── lib/Drupal.php       # Main Drupal class (version constant)
│   │   ├── modules/             # Core modules (node, user, views, etc.)
│   │   ├── themes/              # Core themes (Olivero, Claro, Stark)
│   │   └── ...
│   ├── modules/
│   │   ├── contrib/             # Community-contributed modules (via Composer)
│   │   └── custom/              # Site-specific custom modules
│   │       └── {module_name}/
│   │           ├── {module_name}.info.yml    # Module metadata
│   │           ├── {module_name}.module      # Hook implementations
│   │           ├── {module_name}.routing.yml # Route definitions
│   │           ├── {module_name}.services.yml # Service definitions (DI)
│   │           ├── {module_name}.permissions.yml
│   │           ├── {module_name}.links.menu.yml
│   │           ├── src/
│   │           │   ├── Controller/           # Route controllers
│   │           │   ├── Form/                 # Form classes
│   │           │   ├── Plugin/               # Plugin types (Block, Field, etc.)
│   │           │   ├── EventSubscriber/      # Event subscribers
│   │           │   └── Service/              # Custom services
│   │           ├── templates/                # Twig templates
│   │           ├── config/
│   │           │   └── install/              # Default config on module install
│   │           └── tests/                    # PHPUnit tests
│   ├── themes/
│   │   ├── contrib/             # Community themes
│   │   └── custom/              # Site-specific custom themes
│   │       └── {theme_name}/
│   │           ├── {theme_name}.info.yml     # Theme metadata, regions, libraries
│   │           ├── {theme_name}.theme        # Preprocess functions (hook_preprocess_*)
│   │           ├── {theme_name}.libraries.yml # CSS/JS asset libraries
│   │           ├── templates/                # Twig template overrides
│   │           ├── css/
│   │           └── js/
│   ├── profiles/                # Installation profiles
│   ├── sites/
│   │   └── default/
│   │       ├── settings.php     # Database credentials, config directory paths
│   │       ├── settings.local.php  # Local overrides (gitignored)
│   │       ├── services.yml     # Service container overrides
│   │       └── files/           # Public file uploads
│   └── .htaccess                # Apache rewrite rules
├── config/
│   └── sync/                    # Exported configuration YAML files
├── drush/                       # Drush configuration
├── vendor/                      # Composer dependencies
└── scripts/                     # Deployment/utility scripts
```

#### Drupal 7 (Legacy)

```
project-root/
├── index.php                    # Front controller
├── includes/                    # Core includes (bootstrap, database, form API)
├── modules/                     # Core modules
├── sites/
│   ├── all/
│   │   ├── modules/
│   │   │   ├── contrib/         # Community modules
│   │   │   └── custom/          # Custom modules
│   │   ├── themes/              # Custom and contrib themes
│   │   └── libraries/           # Third-party PHP/JS libraries
│   └── default/
│       ├── settings.php         # Database credentials, config
│       └── files/               # Public file uploads
├── profiles/                    # Installation profiles
├── misc/                        # Core JS, CSS, images
├── scripts/                     # Drush and utility scripts
└── .htaccess
```

### Core Design Patterns

- **Hook System (Drupal 7):** Procedural hook-based extension. Functions named `{module}_{hook}()` in `.module` files are auto-discovered. Key hooks: `hook_menu()`, `hook_form_alter()`, `hook_node_view()`, `hook_schema()`, `hook_update_N()`.
- **Plugin System (Drupal 9/10):** Object-oriented plugin architecture replacing many hooks. Annotated PHP classes in `src/Plugin/` directories discovered via PSR-4 autoloading. Plugin types: Block, Field (Formatter, Widget, Type), Views (Filter, Sort, Field), Condition, Action, etc.
- **Services and Dependency Injection (Drupal 9/10):** Symfony-based service container. Services defined in `*.services.yml`. Core services: `entity_type.manager`, `current_user`, `database`, `config.factory`, `messenger`. Injected via constructors or `\Drupal::service()` (static fallback).
- **Entity API:** Content entities (nodes, users, terms, files) and configuration entities (views, image styles, content types). Field API allows attaching fields to any entity bundle. Entity storage handles CRUD with database abstraction.
- **Configuration Management (Drupal 9/10):** YAML-based config in `config/sync/`. Export: `drush config:export`. Import: `drush config:import`. Active config lives in database (`config` table). Config entities vs. simple config (`*.settings.yml`).
- **Render API:** Render arrays (`#type`, `#theme`, `#markup`) converted to HTML. Caching via `#cache` keys (contexts, tags, max-age). Bubbleable metadata for cache invalidation.
- **Form API:** Structured form definitions as arrays. `buildForm()`, `validateForm()`, `submitForm()` lifecycle. CSRF protection via form tokens. Ajax support via `#ajax` property.
- **Routing (Drupal 9/10):** Symfony-based routing in `*.routing.yml`. Route parameters, requirements, access checks. Controllers in `src/Controller/`.
- **Event System (Drupal 9/10):** Symfony EventDispatcher. Event subscribers in `src/EventSubscriber/`. Coexists with hooks (many hooks have event equivalents).

### Template/Theme System

- **Twig (Drupal 9/10):** All templates are Twig (`.html.twig`). Template suggestions follow naming conventions: `node--{type}--{view_mode}.html.twig`. Preprocess functions in `.theme` file add variables. Debug mode via `services.yml`: `twig.config.debug: true`.
- **PHPTemplate (Drupal 7):** PHP-based templates (`.tpl.php`). Template suggestions via `hook_theme_suggestions_alter()`. Preprocess functions via `{theme}_preprocess_{hook}()`.
- **Theme inheritance:** Subtheme declares `base theme` in `.info.yml`. Inherits templates, CSS, JS from parent. Can override selectively.
- **Libraries:** CSS/JS managed as libraries in `*.libraries.yml`. Attached to render arrays via `#attached`. External dependencies declared explicitly. Libraries can declare dependencies on other libraries.
- **Regions:** Defined in `{theme}.info.yml`. Blocks assigned to regions via admin UI or config. Standard regions: header, content, sidebar_first, sidebar_second, footer.

### Routing and URL Structure

- **Drupal 9/10:** Routes defined in `*.routing.yml` with path patterns, controller references, access requirements. Path aliases stored in `path_alias` entity (replaces D7 `url_alias` table). Patterns set via Pathauto module (`/content/{node-title}`).
- **Drupal 7:** Routes defined in `hook_menu()` with path, callback, access arguments. Paths like `node/{nid}`, `user/{uid}`, `admin/config/*`. URL aliases in `url_alias` table.
- **Clean URLs:** Enabled by default (mod_rewrite on Apache, try_files on Nginx). All paths routed through `index.php`.
- **Admin paths:** `/admin/structure`, `/admin/config`, `/admin/content`, `/admin/people`. Module settings typically at `/admin/config/{category}/{module}`.

## Common Patterns

### Theme/Module Customization Approaches

- **Custom modules (Drupal 9/10):** OOP-based with PSR-4 autoloading. Controllers, Forms, Plugins, Services. Module scaffolding via Drush generate or Drupal Console.
- **Custom modules (Drupal 7):** Procedural `.module` files implementing hooks. All functions prefixed with module name. `.install` file for schema and updates.
- **Theme overrides:** Copy core/contrib templates into custom theme `templates/` directory. Override preprocess functions in `.theme` file. Theme-specific template suggestions.
- **hook_form_alter() / hook_form_FORM_ID_alter():** The primary way to modify any form in Drupal. Used extensively for customizing admin forms, content forms, and views exposed filters.
- **Custom blocks (Drupal 9/10):** Block plugin classes in `src/Plugin/Block/`. Annotation-based discovery. Configurable blocks with `blockForm()`, `blockSubmit()`, `build()` methods.
- **Preprocess functions:** `{theme}_preprocess_{hook}(&$variables)` in `.theme` file. Add/modify variables available in Twig templates.

### Content Types and Data Modeling

- **Content types (node bundles):** Defined in admin UI or config YAML. Each type has its own fields. Common: Article, Basic Page, custom types for business content.
- **Field API:** Fields attached to entity bundles (content types, taxonomy vocabularies, user profiles). Field types: text, integer, boolean, entity reference, link, image, file, date, list. Field storage config + field instance config.
- **Paragraphs module:** Reusable content components attached to nodes via reference field. Each paragraph type has its own fields. Alternative to page builders. Content stored as separate paragraph entities.
- **Taxonomy:** Vocabularies contain terms. Terms can be hierarchical. Used for categorization, tagging, and structured data. Entity reference fields link content to terms.
- **Views:** Powerful query builder for listing content. Views configuration defines: base table, fields, filters, sorts, pager, display modes (page, block, REST). Stored as config entities.
- **Media entities (Drupal 9/10):** Media types (Image, Document, Video, Remote Video) with source plugins. Replaces direct file field usage. Stored as media entities.

### Page Builders / Layout Systems

- **Layout Builder (Core):** Core module in Drupal 9/10. Per-content-type or per-entity layout customization. Sections with configurable layouts. Blocks placed in layout regions. Stores layout data on content entity field (`layout_builder__layout`).
- **Paragraphs:** Component-based content modeling. Not a visual page builder but serves similar purpose. Each paragraph type is a mini content type. Nested paragraphs possible. Widely used in enterprise Drupal.
- **Layout Paragraphs:** Adds layout capabilities to Paragraphs — grid layouts, columns, nesting.
- **Gutenberg for Drupal:** Drupal module bringing WordPress block editor. Stores content as Gutenberg block markup. Less common.

### Custom Code Entry Points

- **`*.module` file:** Hook implementations. Event subscriber registration (D7 only — D9/10 uses services.yml).
- **`*.theme` file:** Preprocess functions, theme suggestions, template alter hooks.
- **`settings.php`:** Database config, trusted host patterns, config sync directory, file paths, environment-specific overrides.
- **`settings.local.php`:** Local development overrides (gitignored). Debug settings, dev service parameters.
- **`services.yml` (site-level):** Override core service parameters (Twig debug, cache backends, CORS).
- **`*.routing.yml`:** Custom routes/pages.
- **`*.services.yml`:** Custom service definitions and dependency injection.
- **`config/sync/`:** Exported YAML configuration. Import/export manages site configuration changes across environments.

## Known Pitfalls

### Common Sources of Side Effects

- **hook_form_alter() cascading:** Multiple modules altering the same form. Order depends on module weight. A module altering a form can break another module's alterations. Audit with `hook_form_FORM_ID_alter()` specificity.
- **Cache invalidation complexity:** Drupal's render cache is aggressive. Changes not appearing often due to cache. Cache tags, contexts, and max-age must be correctly set. Missing cache metadata causes stale content or cache pollution.
- **Config import/export drift:** Manual changes via admin UI create config drift between environments. Always export config after UI changes. `drush config:import` can override manual changes. Conflicting config changes between branches cause merge failures.
- **Update hooks and schema changes:** `hook_update_N()` functions run once and are tracked by schema version. Skipping a version number breaks the update chain. Database updates must be idempotent.
- **Entity reference integrity:** Deleting referenced entities leaves dangling references. No cascading delete by default. Check for orphaned references after entity cleanup.
- **Module weight and execution order:** Hook execution order determined by module weight in `system` table. Default weight is 0. Modules with dependencies execute after their dependencies.
- **Rebuild registry issues (Drupal 7):** Adding new files requires registry rebuild. Missing class autoloading after file renames. Use `drush registry-rebuild`.

### Performance Traps

- **Views without caching:** Views default to no cache or time-based cache. Complex views without tag-based caching cause repeated expensive queries. Enable Views cache and set appropriate cache tags.
- **Uncached render arrays:** Missing `#cache` metadata on render arrays. Every render should declare its cache contexts (user, route, query args), tags (entity types), and max-age.
- **N+1 entity loading:** Loading entities one at a time in loops instead of batch loading with `EntityType::loadMultiple()`. Field API compounds this — each field value requires additional queries without proper entity loading.
- **Excessive hook implementations:** Modules implementing `hook_node_view()` or `hook_entity_view()` for every entity view add overhead. Use entity type and bundle checks early to bail out.
- **Unoptimized custom queries:** Direct database queries bypassing entity API lose caching benefits. Use entity queries (`\Drupal::entityQuery()`) when possible for proper access checking and caching.
- **BigPipe and lazy builder misuse (Drupal 9/10):** Not using lazy builders for personalized content forces full page cache bypass. Personalized blocks should use `#lazy_builder` callback.
- **Translation overhead:** Multilingual sites with many languages add significant database and cache overhead. Each translated entity creates additional database rows.

### Security Anti-Patterns

- **Direct database queries without placeholders:** Using `db_query("SELECT ... WHERE id = $id")` instead of `db_query("SELECT ... WHERE id = :id", [':id' => $id])`. SQL injection risk. Drupal 9/10: use `$database->select()` or `$database->query()` with placeholders.
- **Unfiltered user input in markup:** Using `#markup` with user input instead of `#plain_text` or `Xss::filter()`. XSS risk. Twig auto-escapes by default, but `|raw` filter bypasses this.
- **Missing access checks on routes:** Routes in `*.routing.yml` without `_permission`, `_role`, or `_access` requirements. Custom controllers without `AccessResult` checks.
- **Exposed PHP filter (Drupal 7):** PHP filter module allowing PHP code execution in content. Must be disabled. Critical security risk.
- **SA-CORE advisories ignored:** Drupal Security Advisories (SA-CORE, SA-CONTRIB) must be tracked. Drupal has a public disclosure policy — exploits often appear within hours of advisory.
- **Trusted host patterns not set:** `settings.php` must define `$settings['trusted_host_patterns']` to prevent HTTP Host header attacks.
- **File upload permissions:** Public files accessible at `/sites/default/files/`. Private file system must be configured for sensitive uploads. Check `$settings['file_private_path']`.

### Dependency Conflicts and Plugin Incompatibilities

- **Composer dependency conflicts:** Contrib modules requiring incompatible versions of the same library. Use `composer why-not` to diagnose. Drupal Lenient Composer plugin helps with version constraints.
- **Drupal 7 to 9/10 migration paths:** Modules may not have D9/10 versions. Check drupal.org project pages for compatibility. Upgrade Status module identifies blockers.
- **jQuery UI removal (Drupal 10):** Drupal 10 removed jQuery UI. Contrib modules depending on it need updates or the `jquery_ui` compatibility module. Check for `jquery.ui` library dependencies.
- **CKEditor 4 to 5 migration (Drupal 10):** CKEditor 5 has different plugin architecture. Custom CKEditor plugins need rewriting. Config migration may not be fully automatic.
- **PHP version requirements:** Drupal 10 requires PHP 8.1+. Drupal 9 requires PHP 7.4+. Drupal 7 works with PHP 5.6+ but many modules need 7.x+. Check `composer.json` platform requirements.

## CLI Tools

### Drush — Primary CLI Tool

```bash
# Core information
drush status                              # Site status overview (version, DB, PHP, etc.)
drush core:requirements                   # Check system requirements
drush updatedb:status                     # Check pending database updates

# Cache management
drush cache:rebuild                       # Rebuild all caches (cr)
drush cache:clear                         # Clear specific cache bins
drush cache:rebuild --no-cache-clear      # Rebuild container without clearing caches

# Configuration management (Drupal 9/10)
drush config:export                       # Export active config to sync directory
drush config:import                       # Import config from sync directory
drush config:get system.site              # Read specific config
drush config:set system.site name "Site"  # Set config value
drush config:status                       # Show config differences

# Database
drush sql:dump > backup.sql               # Export database
drush sql:cli                             # MySQL CLI
drush sql:query "SELECT ..."              # Run SQL query
drush sql:sanitize                        # Remove user data (emails, passwords)

# Modules
drush pm:list                             # List all modules with status
drush pm:list --status=enabled            # Enabled modules only
drush pm:enable {module}                  # Enable a module
drush pm:uninstall {module}               # Uninstall a module

# Users
drush user:login                          # Generate one-time login link
drush user:create admin --mail=a@b.com    # Create user
drush user:role:add administrator admin   # Add role to user
drush user:password admin "newpass"       # Reset password

# Entity and content
drush entity:updates                      # Apply pending entity schema updates
drush views:dev                           # Disable Views caching for development

# Updates and maintenance
drush updatedb                            # Run pending database updates
drush state:set system.maintenance_mode 1 # Enable maintenance mode
drush state:set system.maintenance_mode 0 # Disable maintenance mode

# Watchdog (logs)
drush watchdog:show                       # Show recent log entries
drush watchdog:show --severity=error      # Show errors only
drush watchdog:tail                       # Tail the log

# Code generation
drush generate:module                     # Generate module scaffolding
drush generate:controller                 # Generate controller
drush generate:form                       # Generate form class
```

### DDEV Integration Commands

```bash
# Start/stop environment
ddev start                                # Start containers
ddev stop                                 # Stop containers
ddev restart                              # Restart containers

# Drush through DDEV
ddev drush status                         # Run Drush inside container
ddev drush cr                             # Cache rebuild
ddev drush config:export                  # Export config
ddev drush sql:dump > backup.sql          # Export database

# Database access
ddev mysql                                # MySQL CLI inside container
ddev export-db --file=backup.sql.gz       # Export database
ddev import-db --file=backup.sql.gz       # Import database

# Composer through DDEV
ddev composer install                     # Install dependencies
ddev composer require drupal/{module}     # Add a module
ddev composer update                      # Update dependencies

# SSH into container
ddev ssh                                  # Enter web container
```

### Database Inspection Commands

```bash
# Table sizes and overview
drush sql:query "SHOW TABLE STATUS"
drush sql:query "SELECT table_name, table_rows, data_length FROM information_schema.tables WHERE table_schema = DATABASE() ORDER BY data_length DESC"

# Node count by type
drush sql:query "SELECT type, status, COUNT(*) as count FROM node_field_data GROUP BY type, status ORDER BY count DESC"

# Module status and weight
drush sql:query "SELECT name, status, weight FROM system WHERE type = 'module' ORDER BY weight, name"  # Drupal 7
drush pm:list --format=table              # Drupal 9/10

# Config in database
drush sql:query "SELECT name FROM config ORDER BY name"  # List all config names
drush config:get {config_name}            # Read specific config object

# Field storage info
drush sql:query "SELECT id, field_name, entity_type, type FROM config WHERE name LIKE 'field.storage%'" # Drupal 9/10
```

### Cache and Configuration Management

```bash
# Cache operations
drush cr                                  # Full cache rebuild (most common)
drush cache:clear render                  # Clear render cache only
drush cache:clear dynamic_page_cache      # Clear dynamic page cache
drush cache:clear page                    # Clear page cache

# Configuration workflow
drush config:export -y                    # Export active → sync
drush config:import -y                    # Import sync → active
drush config:status                       # Compare active vs sync
drush config:import --partial --source=/path  # Partial import from directory

# State API (runtime values, not exported)
drush state:get system.maintenance_mode
drush state:set system.cron_last 0        # Force next cron run
```

## Test Patterns

### Critical Pages/Routes to Cover with E2E Tests

- **Homepage** — Often a Views-based listing or custom landing page. Test content rendering, pager.
- **Node pages** — `/node/{nid}` or aliased path. Test each content type's full view mode (fields, layout, related content).
- **Taxonomy term pages** — `/taxonomy/term/{tid}` or aliased. Test term listing, filters.
- **User pages** — `/user/{uid}`, `/user/login`, `/user/register`, `/user/password`. Authentication flow.
- **Search** — `/search/node?keys={query}`. Search results rendering and pager.
- **Views pages** — Custom views with exposed filters. Test filter combinations, pager, AJAX refresh.
- **Admin content listing** — `/admin/content`. Test filters, bulk operations.
- **Content creation/editing** — `/node/add/{type}`, `/node/{nid}/edit`. Test form rendering, validation, save.
- **Webform submissions** — If Webform module is used. Test form rendering, submission, confirmation.
- **404/403 pages** — Custom error pages if configured.
- **Multilingual (if applicable)** — Test language switching, translated content, URL prefixes (`/fr/`, `/en/`).

### Components That Benefit from Snapshot Testing

- **Block output** — Rendered HTML of each custom and configured block.
- **Views output** — Rendered HTML of Views (page, block, REST displays). Detects query/template changes.
- **Menu trees** — Rendered navigation menus by menu name. Detects menu structure changes.
- **Field formatter output** — Rendered field output per formatter type. Catches formatter plugin changes.
- **Config YAML files** — Snapshot exported configuration files. Detects unintended config changes (the config system itself serves as snapshot — supplement with CI checks).
- **REST/JSON:API responses** — `/jsonapi/node/{type}` response structure. Schema validation for API consumers.
- **Twig template output** — Rendered templates with known input variables.

### Integration Points to Monitor

- **Module enable/disable** — Schema changes, service container rebuild, config entity creation/deletion.
- **Config import** — Can change any aspect of the site. Monitor for destructive config deletes.
- **Core/module updates** — Database schema updates (`drush updatedb`). Hook_update_N execution. Post-update hooks.
- **Cron execution** — Modules schedule cron tasks. Monitor `hook_cron()` implementations and queue workers.
- **External API integrations** — Modules calling external services (search indexing, payment, CRM sync). Monitor with event subscribers or Guzzle middleware.
- **Entity CRUD hooks** — `hook_entity_presave()`, `hook_entity_insert()`, `hook_entity_update()`, `hook_entity_delete()`. Side effects from custom logic on entity operations.

### Framework-Specific Test Utilities

- **Drupal Test Traits:** `BrowserTestBase` for functional tests with full Drupal bootstrap. `KernelTestBase` for integration tests with minimal bootstrap. `UnitTestCase` for pure unit tests.
- **Nightwatch.js (Core):** Drupal core includes Nightwatch for JavaScript testing. Configuration in `core/.env` and `core/tests/Drupal/Nightwatch/`.
- **Existing Site Tests:** `ExistingSiteBase` (from `weitzman/drupal-test-traits`) for testing against a real site without install. Fast, no database reset.
- **Drupal Test API:** `$this->drupalLogin()`, `$this->drupalGet()`, `$this->submitForm()`, `$this->assertSession()` for browser-like test interactions.
- **Config testing:** `drush config:status` in CI to detect config drift. Assert no config differences between codebase and active config.
- **Playwright/Cypress for E2E:** Use Drush to seed test data before runs. `drush sql:dump`/`drush sql:cli < dump.sql` for test isolation.

## Database

### Key Tables and Their Purposes

#### Drupal 9/10

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| `node` | Node entity base table | `nid`, `vid`, `type`, `uuid`, `langcode` |
| `node_field_data` | Node field data (title, status, dates) | `nid`, `vid`, `type`, `langcode`, `title`, `status`, `created`, `changed`, `uid` |
| `node_field_revision` | Node revision data | `nid`, `vid`, `langcode`, `title`, `status`, `created`, `changed` |
| `node__{field_name}` | Dedicated field storage table per field | `entity_id`, `revision_id`, `bundle`, `delta`, `{field_name}_value` (+ format, target_id, etc.) |
| `node_revision__{field_name}` | Field revision storage | Same as above, keyed by `revision_id` |
| `users` | User entity base | `uid`, `uuid`, `langcode` |
| `users_field_data` | User field data | `uid`, `langcode`, `name`, `mail`, `pass`, `status`, `created`, `changed` |
| `taxonomy_term_data` | Taxonomy term base | `tid`, `vid` (vocabulary ID), `uuid`, `langcode` |
| `taxonomy_term_field_data` | Term field data | `tid`, `vid`, `langcode`, `name`, `description__value`, `weight`, `changed` |
| `taxonomy_term__parent` | Term hierarchy | `entity_id`, `parent_target_id` |
| `file_managed` | File entity records | `fid`, `uuid`, `uid`, `filename`, `uri`, `filemime`, `filesize`, `status`, `created`, `changed` |
| `media` | Media entity base | `mid`, `vid`, `bundle`, `uuid`, `langcode` |
| `media_field_data` | Media field data | `mid`, `vid`, `bundle`, `langcode`, `name`, `status`, `created`, `changed`, `uid` |
| `config` | Active site configuration | `name`, `collection`, `data` (serialized BLOB) |
| `key_value` | Key-value store (state API, entity definitions) | `collection`, `name`, `value` |
| `key_value_expire` | Expiring key-value (temp store, form state) | `collection`, `name`, `value`, `expire` |
| `cache_*` | Cache bins (cache_default, cache_render, cache_page, etc.) | `cid`, `data`, `expire`, `tags` |
| `watchdog` | System log entries | `wid`, `uid`, `type`, `message`, `severity`, `timestamp` |
| `path_alias` | URL path aliases | `id`, `path`, `alias`, `langcode` |
| `block_content` | Custom block entity base | `id`, `revision_id`, `type`, `uuid`, `langcode` |
| `block_content_field_data` | Custom block field data | `id`, `revision_id`, `type`, `langcode`, `info`, `status`, `changed` |
| `paragraphs_item` | Paragraph entity base (if Paragraphs module) | `id`, `revision_id`, `type`, `uuid`, `langcode` |
| `paragraphs_item_field_data` | Paragraph field data | `id`, `revision_id`, `type`, `langcode`, `status`, `created`, `changed`, `parent_id`, `parent_type`, `parent_field_name` |

#### Drupal 7 (Legacy)

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| `node` | Node base table | `nid`, `vid`, `type`, `language`, `title`, `uid`, `status`, `created`, `changed` |
| `node_revision` | Node revisions | `nid`, `vid`, `uid`, `title`, `log`, `timestamp` |
| `field_data_{field_name}` | Field value storage | `entity_type`, `bundle`, `entity_id`, `revision_id`, `delta`, `{field_name}_value` |
| `field_revision_{field_name}` | Field revision storage | Same as above |
| `users` | User accounts | `uid`, `name`, `mail`, `pass`, `status`, `created` |
| `taxonomy_term_data` | Taxonomy terms | `tid`, `vid`, `name`, `description` |
| `taxonomy_term_hierarchy` | Term parent relationships | `tid`, `parent` |
| `variable` | System variables (config) | `name`, `value` (serialized) |
| `system` | Module/theme registry | `name`, `type`, `status`, `weight`, `schema_version` |
| `cache` / `cache_*` | Cache tables | `cid`, `data`, `expire` |
| `watchdog` | System log | `wid`, `uid`, `type`, `message`, `severity`, `timestamp` |
| `url_alias` | Path aliases | `pid`, `source`, `alias`, `language` |
| `menu_links` | Menu items | `mlid`, `menu_name`, `link_path`, `link_title`, `weight`, `depth` |
| `block` | Block placement config | `bid`, `module`, `delta`, `theme`, `status`, `weight`, `region` |

### Important Relationships to Understand

#### Drupal 9/10

```
node ←——(nid)——→ node_field_data           # 1:1 per language — base to field data
node ←——(nid,vid)——→ node_field_revision    # 1:N — revisions per node
node ←——(entity_id)——→ node__{field_name}   # 1:N per delta — field storage
node_field_data ←——(uid)——→ users           # N:1 — node author
taxonomy_term_data ←——(entity_id)——→ taxonomy_term__parent  # Self-referential hierarchy
node__{field_ref} ←——(target_id)——→ node    # M:N — entity references between nodes
node__{field_ref} ←——(target_id)——→ taxonomy_term_data  # M:N — node to terms
file_managed ←——(fid=target_id)——→ node__{file_field}   # M:N — files to nodes
media ←——(mid=target_id)——→ node__{media_field}          # M:N — media to nodes
paragraphs_item ←——(parent_id)——→ node      # N:1 — paragraphs to host node
```

#### Drupal 7

```
node ←——(nid)——→ field_data_{field_name}    # 1:N — node to field values
node ←——(vid)——→ node_revision              # 1:N — node revisions
node ←——(uid)——→ users                      # N:1 — node author
taxonomy_term_data ←——(tid)——→ taxonomy_term_hierarchy  # Self-referential
taxonomy_term_data ←——(tid)——→ taxonomy_index ——→ node  # M:N — terms to nodes
```

### Configuration Storage Patterns

- **Drupal 9/10 — Config API:** Active config stored in `config` table as serialized data. Exported to YAML files in `config/sync/`. Config split module allows environment-specific config. Simple config (`*.settings`) vs config entities (`views.view.*`, `node.type.*`).
- **Drupal 9/10 — State API:** Runtime values in `key_value` table (collection `state`). Not exportable. Used for: `system.cron_last`, `system.maintenance_mode`, module-specific runtime data.
- **Drupal 7 — Variable API:** `variable` table with serialized values. `variable_get()` / `variable_set()`. All variables loaded on every request (performance concern with many/large variables).
- **Drupal 7 — System table:** Module/theme status, schema versions, weights stored in `system` table.

### Content Storage Patterns

- **Drupal 9/10 — Entity field storage:** Base fields in entity base/data tables. Configurable fields in dedicated `{entity_type}__{field_name}` tables. Shared field storage possible across bundles. Revision tables mirror data tables for revisioned entities.
- **Drupal 9/10 — Paragraphs storage:** Paragraph entities stored in `paragraphs_item` and `paragraphs_item_field_data`. Parent reference (node ID, field name) stored on paragraph. Paragraph fields stored in `paragraphs_item__{field_name}` tables. Deeply nested paragraphs create complex join chains.
- **Drupal 7 — Field storage:** All field data in `field_data_{field_name}` and `field_revision_{field_name}` tables. Entity type and bundle columns allow shared fields across entity types. Delta column handles multi-value fields.
- **File storage:** Files tracked in `file_managed` table. Physical files in `sites/default/files/` (public) or private file path. File usage tracked in `file_usage` table (Drupal 7) or entity reference (Drupal 9/10).
- **Media storage:** Media entities wrap file entities with additional metadata. Media type determines source plugin (file, image, oembed). Media library provides asset selection UI.
