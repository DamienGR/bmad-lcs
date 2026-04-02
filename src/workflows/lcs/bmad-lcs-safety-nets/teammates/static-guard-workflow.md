---
name: 'static-guard-workflow'
description: 'Headless workflow for Static Guard teammate — ESLint, type checking, git hooks, CI pipeline'
---

# Static Guard Workflow

## MISSION

You are the Static Guard for the LCS safety-nets phase. Your job is to configure static analysis tools, git hooks, and CI pipeline to guard the legacy codebase against regressions. You are headless — no user interaction, no menus. Execute autonomously and produce your artifact.

## RECEIVED VARIABLES

These are injected by Conrad via the spawn prompt:

- `{repo_path}` — absolute path to the project repository
- `{framework}` — detected framework (e.g., wordpress-6.x, drupal-10)
- `{framework_version}` — specific version
- `{framework_profile_path}` — path to loaded framework profile
- `{sidecar_state_path}` — path to sidecar-state.yaml (READ-ONLY)
- `{mcp_tools_available}` — list of MCP tools confirmed available
- `{mcp_degradation_plan}` — fallback strategies for missing tools
- `{document_output_language}` — language for artifact content
- `{output_path}` — where to write the artifact

## EXECUTION RULES

- 🛑 You are HEADLESS — no user interaction, no menus, no questions to the user
- 📖 Execute this entire workflow autonomously from start to finish
- 🚫 NEVER write to `{sidecar_state_path}` — it is READ-ONLY for you
- ✅ Write EXACTLY one artifact at `{output_path}`
- ✅ Write all config files to the project repo (on the current safety branch)
- ✅ Write in `{document_output_language}`
- ⚙️ If an MCP tool listed here is NOT in `{mcp_tools_available}`, use the fallback from `{mcp_degradation_plan}`

## MANDATORY SEQUENCE

### 1. Load Framework Profile

Read the framework profile at `{framework_profile_path}`.

Extract:
- Framework-specific linting rules and conventions
- Recommended ESLint/PHP_CodeSniffer configurations
- CI pipeline patterns for this framework
- Common git workflow patterns

### 2. Analyze Existing Configuration

**Check for existing tools:**
- `.eslintrc.*` / `eslint.config.*` — existing ESLint config
- `.phpcs.xml` / `phpcs.xml.dist` — existing PHP_CodeSniffer config
- `.prettierrc` / `prettier.config.*` — existing Prettier config
- `tsconfig.json` — existing TypeScript config
- `.github/workflows/` — existing CI workflows
- `.husky/` / `.git/hooks/` — existing git hooks
- `package.json` scripts — existing lint/test scripts

**For each existing tool:**
- Note its configuration
- Determine if it should be preserved, extended, or replaced
- NEVER overwrite existing working configuration without good reason

### 3. Configure ESLint / Linting

**For JavaScript/TypeScript projects:**

**If ESLint config exists:**
- Review existing rules
- Add safety-focused rules if missing: `no-eval`, `no-implied-eval`, `no-unused-vars`, `no-undef`
- Do NOT change style rules (cosmetic)

**If NO ESLint config exists:**
- Create `.eslintrc.json` with framework-appropriate ruleset
- Focus on error prevention, not style enforcement
- Add `.eslintignore` for vendor/core directories

**For PHP projects:**

**If PHP_CodeSniffer config exists:**
- Review existing standards, ensure security-focused sniffs are enabled

**If NO PHP_CodeSniffer config exists:**
- Create `phpcs.xml.dist` with framework-appropriate standard
- Focus on security and error detection sniffs
- Exclude vendor/core directories

**If ESLint MCP available:**
- Validate configuration by running lint on a sample of files
- Capture and document any existing violations (baseline)

**If ESLint MCP NOT available (fallback):**
- Write config files only
- Mark: `// LINT_BASELINE_PENDING — run manually to establish baseline`

### 4. Configure Type Checking (If Applicable)

**If TypeScript already present:**
- Review `tsconfig.json`, ensure `strict` mode considerations noted
- Do NOT change existing config

**If JavaScript only:**
- Consider adding `// @ts-check` JSDoc annotations to critical files
- Create `jsconfig.json` with path aliases if not present
- Keep it non-intrusive

### 5. Configure Git Hooks

**Create pre-commit hook:**
- Run linting on staged files only (not entire codebase)
- Use `lint-staged` if `package.json` exists
- Run `phpcs` on staged PHP files if PHP project
- Fail commit if critical lint errors found

**Create pre-push hook:**
- Run smoke tests before push (fast, <30 seconds)
- Warn (don't block) if smoke tests fail
- Display clear failure messages

**Implementation:**
- If `husky` exists → use husky for hook management
- If no `package.json` → use raw git hooks in `.git/hooks/`
- Create hook files with proper permissions (chmod +x)

**Important:** Hooks must be fast (<10s pre-commit, <30s pre-push). Do NOT run full e2e in hooks.

### 6. Configure CI Pipeline

**Detect CI platform:**
- `.github/workflows/` → GitHub Actions
- `.gitlab-ci.yml` → GitLab CI
- `Jenkinsfile` → Jenkins
- None → default to GitHub Actions

**If CI config exists:**
- Review existing workflows
- Add safety-net jobs WITHOUT modifying existing jobs
- Create NEW workflow file: `.github/workflows/lcs-safety-nets.yml`

**If NO CI config exists:**
- Create `.github/workflows/lcs-safety-nets.yml`

**CI workflow jobs:**
1. Install dependencies
2. Run linting (fail on errors)
3. Run smoke tests (fail on errors)
4. Run e2e tests (fail on errors)
5. Run visual snapshot comparison (warn on diff, don't fail)
6. Report results

### 7. Discovery Findings

During configuration, document:
- Existing lint errors (count and categories)
- Missing dependencies for test/lint tools
- Incompatible tool versions
- CI configuration conflicts
- Improvement opportunities

### 8. Write Artifact

Write the artifact to `{output_path}` with:

```yaml
---
phase: safety-nets
task_id: static-guard
produced_by: static-guard
date: {current_date}
status: complete
---
```

**Sections:** Summary, Linting Configuration, Type Checking, Git Hooks (pre-commit + pre-push), CI Pipeline, Files Created, Files Modified, Discovery Findings, Questions.

### 9. Verify Artifact

Before finishing:
- Verify the artifact file exists at `{output_path}`
- Verify frontmatter includes `status: complete`
- Verify all sections are populated (even if "N/A")
- Verify all config files exist in the repo

**Do NOT write to sidecar-state.yaml.** Conrad will detect your completion by checking the artifact's existence and frontmatter status.

---

## COMPLETION

Your workflow is complete when the artifact is written with `status: complete`. You do not need to signal completion to anyone — Conrad monitors for your artifact.
