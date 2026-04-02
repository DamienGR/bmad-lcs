---
name: 'step-03-enrich'
description: 'Scan project repository for framework-specific details and enrich the profile with project-specific knowledge'

outputFile: '{output_folder}/lcs/framework-profile-active.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
---

# Step 3: Enrich Profile with Project Specifics

## STEP GOAL:

To scan the project repository for framework-specific details, enrich the profile sections (prioritizing gaps identified in step 02), write the final enriched profile to `{outputFile}`, and update `{sidecarStatePath}` to mark the profile as loaded.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER fabricate project details — only report what is found in the repo
- 📖 CRITICAL: Read the complete step file before taking any action
- 📋 YOU ARE AN ANALYST, not a content generator
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You bring framework analysis expertise; the project files provide the evidence
- ✅ Report what exists factually — do not speculate about what might be there

### Step-Specific Rules:

- 🎯 Focus on scanning the project and enriching profile sections
- 🎯 Use subprocess optimization (Pattern 1: Grep/Regex) for scanning when available
- 💬 If subprocess unavailable, perform scans in main thread
- 🚫 FORBIDDEN to guess or fabricate — if a scan finds nothing, report "not detected"
- 📋 Prioritize CRITICAL gaps from step 02 validation

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write final enriched profile to `{outputFile}` with updated frontmatter
- 📖 Update `{sidecarStatePath}` with `profile_loaded: true`
- 🚫 Do NOT skip scanning even if profile seems complete — project specifics add value

## CONTEXT BOUNDARIES:

- Available: `{outputFile}` with profile content; `framework`, `repo_path`, `validation_score`, `gaps[]` from previous steps
- Focus: project file scanning and profile enrichment
- Limits: only scan files within `repo_path` — do not access external resources
- Dependencies: `{outputFile}` must exist from step 01; gaps from step 02 in memory

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Prepare Scan Based on Framework

Read `{outputFile}` to get current profile content.

Use `framework` from memory to determine scan targets:

**IF WordPress:**

Launch a subprocess (or perform in main thread) that scans the repo for:

- `wp-content/plugins/` — list all plugin directories (active plugins)
- `wp-content/themes/` — list all theme directories, identify active theme
- Grep `functions.php` for: `add_shortcode\(`, `register_post_type\(`, `add_action\(`, `add_filter\(`
- Grep for page builder indicators: `elementor`, `divi`, `et_pb_`, `acf_`, `advanced-custom-fields`
- Check `composer.json` (if exists) for WordPress-specific dependencies
- Check `wp-config.php` for multisite indicators, custom table prefix

Return structured findings:
```
{
  plugins: [list],
  themes: [list],
  active_theme: 'name',
  page_builders: [detected],
  custom_post_types: [found],
  shortcodes: [found],
  hooks_custom: [count of add_action/add_filter in functions.php],
  multisite: true/false
}
```

**IF Drupal:**

Launch a subprocess (or perform in main thread) that scans the repo for:

- `modules/custom/` or `web/modules/custom/` — list custom modules
- `modules/contrib/` or `web/modules/contrib/` — list contrib modules
- `themes/custom/` or `web/themes/custom/` — list custom themes
- Check `composer.json` for Drupal dependencies and versions
- Check `config/` or `web/sites/default/` for configuration exports
- Grep for `hook_` implementations in custom module `.module` files

Return structured findings:
```
{
  custom_modules: [list],
  contrib_modules: [list],
  custom_themes: [list],
  drupal_version: 'X.x',
  config_management: 'detected/not_detected',
  hook_implementations: [count]
}
```

**IF Other:**

Scan for general indicators:
- `composer.json` dependencies
- `package.json` dependencies (if exists)
- Directory structure overview
- Configuration files detected

### 2. Report Scan Findings

"**Project Scan Results**

**Framework:** {framework} {framework_version}
**Repository:** {repo_path}

**Discoveries:**
[Present structured findings as a clear table or list]

**{count} project-specific details discovered.**"

### 3. Enrich Profile Sections

For each gap identified in step 02 (prioritize CRITICAL gaps):

**Attempt enrichment using scan findings:**

- **Architecture** — Add discovered directory structure, detected patterns
- **Common Patterns** — Add page builders, custom code patterns, hooks usage
- **Known Pitfalls** — Add framework-version-specific issues, detected plugin conflicts
- **CLI Tools** — Add commands relevant to detected tools (DDEV, Composer)
- **Test Patterns** — Add critical routes based on detected content types and plugins
- **Database** — Add table information based on detected plugins and custom post types

**For sections with NO relevant scan data:**
- Mark as "requires manual input" — do not fabricate content

### 4. Write Final Enriched Profile

Update `{outputFile}` with:

**Updated frontmatter:**
```yaml
---
workflowType: 'framework-profile'
stepsCompleted: ['step-01-load-profile', 'step-02-validate', 'step-03-enrich']
framework: '{framework}'
framework_version: '{framework_version}'
profile_source: '{profile_source}'
validation_score: '{final_score}/6'
date: '{date}'
---
```

**Updated body:** Original profile content with enrichment injections in each section.

Each enriched section should clearly separate generic profile content from project-specific additions:

```markdown
## [Section Name]

[Original profile content]

### Project-Specific

[Enrichment from scan — only if data was found]
```

### 5. Update Sidecar State

Read `{sidecarStatePath}` and update:

```yaml
profile_loaded: true
framework_profile_path: '{outputFile}'
```

If `{sidecarStatePath}` does not exist, note it — pre-flight should have created it.

### 6. Final Summary

"**Framework Profile Complete**

**Profile:** `{outputFile}`
**Framework:** {framework} {framework_version}
**Source:** {profile_source}
**Validation Score:** {final_score}/6

**Enrichment Summary:**

| Section | Before | After | Source |
|---------|--------|-------|--------|
| Architecture | {before} | {after} | {scan data or manual} |
| Common Patterns | {before} | {after} | {scan data or manual} |
| Known Pitfalls | {before} | {after} | {scan data or manual} |
| CLI Tools | {before} | {after} | {scan data or manual} |
| Test Patterns | {before} | {after} | {scan data or manual} |
| Database | {before} | {after} | {scan data or manual} |

**Remaining gaps:** {list any sections still incomplete — 'requires manual input'}

**Sidecar state updated:** `profile_loaded: true`

The enriched profile is now available for all downstream LCS workflows (audit, risk-map, deep-testing, safety-nets)."

## CRITICAL STEP COMPLETION NOTE

This is the final step. The workflow is complete when `{outputFile}` has been written with the enriched profile and `{sidecarStatePath}` has been updated. No next step to load.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Project repo scanned for framework-specific details
- CRITICAL gaps from step 02 prioritized for enrichment
- Profile sections enriched with factual project data
- `{outputFile}` written with updated frontmatter and enriched content
- `stepsCompleted` includes all 3 steps
- `{sidecarStatePath}` updated with `profile_loaded: true`
- Final summary presented with before/after comparison
- Remaining gaps clearly communicated

### ❌ SYSTEM FAILURE:

- Fabricating project details not found in the repo
- Not scanning the project (skipping directly to writing)
- Not prioritizing CRITICAL gaps for enrichment
- Not updating `{sidecarStatePath}`
- Not presenting the final summary
- Writing scan findings as if they were validated facts when they are uncertain

**Master Rule:** Evidence-based enrichment only. Report what is found in the project. Flag what requires manual input. Write the final profile and update sidecar state.
