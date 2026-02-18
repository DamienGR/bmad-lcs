# Environment Checks Registry

Each check is executed sequentially by step-01-env-check.

## Check Format

| Field | Description |
|-------|-------------|
| **Name** | Human-readable check name |
| **Command** | Shell command to execute |
| **Expected** | What a passing result looks like |
| **Category** | BLOCKER / WARNING / INFO |
| **Fix** | Actionable instruction if check fails |
| **Impact** | What happens if this tool is missing |

---

## BLOCKER Checks

These MUST pass for consolidation to proceed.

### Git Repository

- **Command:** `git -C {repo_path} rev-parse --is-inside-work-tree`
- **Expected:** Output `true`, exit code 0
- **Category:** BLOCKER
- **Fix:** "This path is not a git repository. Initialize with `git init` or verify the correct path."
- **Impact:** All LCS phases require git for version control, worktrees, and rollback safety.

### Repository Access

- **Command:** `ls -la {repo_path}`
- **Expected:** Directory listing, exit code 0
- **Category:** BLOCKER
- **Fix:** "Cannot access repository path. Verify the path exists and you have read permissions."
- **Impact:** Cannot analyze code without repository access.

### Site Reachable

- **Command:** `curl -sL -o /dev/null -w "%{http_code}" {site_url}`
- **Expected:** HTTP status 200, 301, or 302
- **Category:** BLOCKER
- **Fix:** "Site is not reachable at {site_url}. Verify the site is running locally. If using DDEV, run `ddev start`. Check the URL is correct."
- **Impact:** Site Explorer cannot navigate the site during Audit phase. E2E tests cannot run.

---

## WARNING Checks

Missing tools degrade specific phases but don't block consolidation.

### DDEV

- **Command:** `command -v ddev && ddev describe --json-output 2>/dev/null`
- **Expected:** ddev command found; if in a ddev project, JSON output with project info
- **Category:** WARNING
- **Fix:** "DDEV not found. Install from https://ddev.com/get-started/ — DDEV provides consistent local development environments for WordPress and Drupal."
- **Impact:** Without DDEV, you'll need to manage the local environment manually. Commands like `ddev wp` and `ddev drush` won't be available.

### Node.js

- **Command:** `node --version`
- **Expected:** Version string (v18+ recommended)
- **Category:** WARNING
- **Fix:** "Node.js not found. Install from https://nodejs.org/ (LTS recommended) or via nvm: `nvm install --lts`"
- **Impact:** Required for test frameworks (Playwright, Jest), linting (ESLint), and CI configuration. Safety Nets and Deep Testing phases will be blocked.

### Composer

- **Command:** `composer --version`
- **Expected:** Version string (2.x recommended)
- **Category:** WARNING
- **Fix:** "Composer not found. Install from https://getcomposer.org/download/ or via: `curl -sS https://getcomposer.org/installer | php`"
- **Impact:** Required for PHP dependency management. Dependency Update phase will be blocked for PHP projects.

---

## INFO Checks

Optional tools that enhance the consolidation experience.

### PHP_CodeSniffer

- **Command:** `command -v phpcs && phpcs --version`
- **Expected:** phpcs command found with version output
- **Category:** INFO
- **Fix:** "PHP_CodeSniffer not found. Install via: `composer global require squizlabs/php_codesniffer`"
- **Impact:** Provides PHP coding standards analysis. Static Guard can still use ESLint for JS/TS. PHP-specific linting will be limited.
