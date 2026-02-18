# MCP Tools Registry

Recommended MCP tools for LCS consolidation. Each tool is checked during pre-flight step 03.

## Tool Format

| Field | Description |
|-------|-------------|
| **Name** | MCP tool display name |
| **MCP Server** | Server identifier to check |
| **Criticality** | essential / recommended / optional |
| **Smoke Test** | Whether to perform a functional check (only for essential tools) |
| **Phase(s)** | Which LCS phase(s) depend on this tool |
| **Impact if Missing** | What capability is lost |

---

## Essential Tools

These significantly impact the consolidation journey when missing.

### Chrome DevTools MCP

- **MCP Server:** chrome-devtools / browser-tools
- **Criticality:** essential
- **Smoke Test:** YES — attempt to list browser targets or get version
- **Phase(s):** Audit (Site Explorer)
- **Impact if Missing:** Site Explorer cannot navigate the live site. Audit will rely solely on code analysis without functional site exploration. Page journeys, visual layout, and runtime behavior won't be captured.

### Playwright MCP

- **MCP Server:** playwright
- **Criticality:** essential
- **Smoke Test:** YES — attempt to list available browsers or get accessibility snapshot
- **Phase(s):** Safety Nets (E2E tests), Deep Testing
- **Impact if Missing:** Cannot write E2E tests or visual regression snapshots. Safety Nets phase will be limited to static analysis only. Deep Testing will lack integration test capabilities.

---

## Recommended Tools

These enhance specific phases but consolidation can proceed without them.

### MySQL MCP

- **MCP Server:** mysql
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Audit (Code Analyst — DB inspection)
- **Impact if Missing:** Cannot inspect database structure (wp_options, wp_posts, Drupal tables). Code Analyst must infer data architecture from code alone.

### Semgrep MCP

- **MCP Server:** semgrep
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Audit (security analysis)
- **Impact if Missing:** No automated security vulnerability detection (SQLi, XSS, CSRF). Security audit will rely on manual code review patterns.

### ESLint MCP

- **MCP Server:** eslint
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Safety Nets (Static Guard)
- **Impact if Missing:** Static Guard will configure linting via CLI instead of structured MCP results. Functionality preserved but less integrated.

### Git MCP

- **MCP Server:** git / cyanheads-git
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Refactoring (worktree management)
- **Impact if Missing:** Worktree creation and management via CLI fallback. Refactorer can still operate but with less structured git operations.

### Snyk MCP

- **MCP Server:** snyk
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Dependency Update (vulnerability audit)
- **Impact if Missing:** No automated vulnerability scoring (CVSS). Dependency audit will use `npm audit` / `composer audit` CLI fallback with less detailed results.

### Lighthouse MCP

- **MCP Server:** lighthouse
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** Audit (performance/accessibility baselines)
- **Impact if Missing:** No automated performance and accessibility baselines. Post-refactoring regression detection for performance will not be available.

### Context7

- **MCP Server:** context7 / upstash-context7
- **Criticality:** recommended
- **Smoke Test:** no
- **Phase(s):** All (accurate API documentation)
- **Impact if Missing:** All teammates lose access to up-to-date API docs for WordPress, Drupal, Playwright, and plugins. Increased risk of API hallucinations.

---

## Optional Tools (Framework-Specific)

These are relevant only for specific frameworks.

### WordPress MCP Adapter

- **MCP Server:** wordpress
- **Criticality:** optional (WordPress projects only)
- **Smoke Test:** no
- **Phase(s):** Audit (WP-specific — plugin list, theme settings, content types)
- **Impact if Missing:** Code Analyst relies on file analysis and WP-CLI instead of programmatic API. Most WP information still accessible via `ddev wp` commands.

### Drupal Tools MCP

- **MCP Server:** drupal-tools
- **Criticality:** optional (Drupal projects only)
- **Smoke Test:** no
- **Phase(s):** Audit (Drupal-specific — module queries, Drush integration)
- **Impact if Missing:** Code Analyst relies on file analysis and Drush CLI. Most Drupal information still accessible via `ddev drush` commands.
