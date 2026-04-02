# Framework Profile Validation Checklist

**Purpose:** Quality criteria for each of the 6 framework profile sections. A section is COMPLETE only when it meets ALL criteria for that section.

---

## Section Criteria

### 1. Architecture

**Severity:** CRITICAL (impacts all downstream workflows)

**Must contain:**
- [ ] File/directory structure specific to this framework version
- [ ] Core design patterns (hooks system, plugin/module architecture, event system)
- [ ] Template/theme system description (how themes work, inheritance model)
- [ ] Routing and URL structure (permalink/alias system, rewrite rules)

**Minimum:** At least file structure AND design patterns present.

### 2. Common Patterns

**Severity:** CRITICAL (impacts audit and refactoring workflows)

**Must contain:**
- [ ] Theme/module customization approaches (child themes, custom modules)
- [ ] Content types and data modeling (posts/nodes, taxonomies, fields)
- [ ] Page builders / layout systems if applicable (Elementor, DIVI, Layout Builder)
- [ ] Custom code entry points (functions.php, .module files, hooks)

**Minimum:** At least customization approaches present.

### 3. Known Pitfalls

**Severity:** CRITICAL (impacts risk-map and safety-nets workflows)

**Must contain:**
- [ ] Common sources of side effects (global state, hook conflicts, caching issues)
- [ ] Performance traps (N+1 queries, unoptimized images, blocking scripts)
- [ ] Security anti-patterns (SQL injection vectors, XSS patterns, auth bypass)
- [ ] Dependency conflicts (plugin/module incompatibilities, version constraints)

**Minimum:** At least security AND performance items present.

### 4. CLI Tools

**Severity:** WARNING (impacts efficiency but not correctness)

**Must contain:**
- [ ] Primary CLI tool with key commands (WP-CLI / Drush / Artisan)
- [ ] DDEV integration commands (if using DDEV local dev)
- [ ] Database inspection commands (export, import, search-replace)
- [ ] Cache and configuration management commands

**Minimum:** At least primary CLI tool with specific commands listed.

### 5. Test Patterns

**Severity:** CRITICAL (impacts deep-testing and safety-nets workflows)

**Must contain:**
- [ ] Critical pages/routes to cover with E2E tests (home, login, key content pages)
- [ ] Components that benefit from snapshot testing (rendered templates, forms)
- [ ] Integration points to monitor (API endpoints, webhooks, cron jobs)
- [ ] Framework-specific test utilities or helpers (WP_UnitTestCase, BrowserTestBase)

**Minimum:** At least critical routes for E2E present.

### 6. Database

**Severity:** WARNING (impacts audit and refactoring workflows)

**Must contain:**
- [ ] Key tables and their purposes (wp_posts, node, users, etc.)
- [ ] Important relationships to understand (post_meta, field tables, taxonomy)
- [ ] Configuration storage patterns (wp_options, config table, key_value)
- [ ] Content storage patterns (serialized data, JSON fields, EAV pattern)

**Minimum:** At least key tables listed.

---

## Scoring

| Score | Status | Action |
|-------|--------|--------|
| 6/6 complete | FULL | Proceed to enrichment |
| 4-5/6 complete | PARTIAL | Enrich gaps, flag remaining |
| 2-3/6 complete | SPARSE | Prioritize CRITICAL gaps for enrichment |
| 0-1/6 complete | MINIMAL | Profile is placeholder — heavy enrichment needed |

## Gap Classification

| Severity | Meaning | Impact |
|----------|---------|--------|
| CRITICAL | Directly impacts downstream workflow quality | Must attempt enrichment |
| WARNING | Reduces efficiency but workflows can proceed | Enrich if project data available |
| INFO | Nice to have, no direct workflow impact | Optional enrichment |
