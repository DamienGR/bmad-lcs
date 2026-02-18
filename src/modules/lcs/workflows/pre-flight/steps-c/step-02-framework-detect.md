---
name: 'step-02-framework-detect'
description: 'Detect project framework using multi-signal cascade with confidence scoring'

nextStepFile: './step-03-mcp-check.md'
frameworksPath: '{project-root}/src/modules/lcs/data/frameworks/'
---

# Step 2: Framework Detection

## STEP GOAL:

To identify the project's framework (WordPress/Drupal/Other) using a multi-signal cascade detection with confidence scoring, load the corresponding framework profile, and fall back to user override when confidence is insufficient.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER guess the framework — use evidence from signals only
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 🎯 Execute detection signals sequentially, aggregate results
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You bring framework detection expertise; the developer knows their project
- ✅ When unsure, ASK — the user override path is first-class, not a fallback afterthought

### Step-Specific Rules:

- 🎯 Focus ONLY on framework detection and profile loading
- 🚫 FORBIDDEN to check MCP tools — that's step 03
- 🚫 FORBIDDEN to write any files — that's step 04
- 💬 Present detection evidence transparently — show what signals were found
- 📋 The user override path is expected ~30% of the time — treat it as normal

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store: framework, framework_version, detection_confidence, profile contents in memory
- 📖 Track all signal results for the capability matrix
- 🚫 Do NOT write any files — data persists in memory for step 04

## CONTEXT BOUNDARIES:

- Available: repo_path, site_url from step 01; environment check results
- Focus: framework identification and profile loading only
- Limits: do not analyze code quality, business logic, or dependencies
- Dependencies: repo_path must be valid (verified in step 01)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Signal 1 — Signature Files

Search for framework-specific signature files in the repo:

**WordPress signals:**
- `wp-config.php` (root or one level up)
- `wp-settings.php`
- `wp-load.php`

**Drupal signals:**
- `sites/default/settings.php`
- `web/sites/default/settings.php`
- `core/lib/Drupal.php`
- `web/core/lib/Drupal.php`

Record: which files found, which absent.

### 2. Signal 2 — Composer Dependencies

If `composer.json` exists in the repo root (or `web/` subfolder):

- Read `composer.json`
- Check `require` and `require-dev` for:
  - `drupal/core` or `drupal/core-recommended` → Drupal
  - `johnpbloch/wordpress-core` or `roots/wordpress` → WordPress (Composer-managed)
  - `wpackagist-plugin/*` → WordPress (via wpackagist)

Record: dependencies found with versions.

### 3. Signal 3 — Directory Structure Patterns

Check for characteristic directory structures:

**WordPress patterns:**
- `wp-content/` (themes, plugins, uploads)
- `wp-admin/`
- `wp-includes/`

**Drupal patterns:**
- `sites/all/modules/`
- `modules/custom/`
- `themes/custom/`
- `web/modules/`
- `web/themes/`

Record: which patterns found.

### 4. Signal 4 — Config File Contents

If signature config files were found in Signal 1, inspect their contents:

**WordPress (`wp-config.php`):**
- Look for `$table_prefix` — extract value (identifies WP version conventions)
- Look for `DB_NAME`, `DB_HOST` definitions
- Check for multisite indicators (`MULTISITE`, `WP_ALLOW_MULTISITE`)

**Drupal (`settings.php`):**
- Look for `$databases` array structure
- Check for `$settings['hash_salt']`
- Look for `$config_directories` (Drupal 8+) or `$drupal_hash_salt` (Drupal 7)

**Version detection:**
- WordPress: check `wp-includes/version.php` for `$wp_version`
- Drupal: check `core/lib/Drupal.php` for `const VERSION` or `DRUPAL_ROOT/CHANGELOG.txt`

Record: version indicators found.

### 5. Aggregate Signals — Confidence Score

Count signals per framework:

| Score | Confidence | Action |
|-------|-----------|--------|
| 3-4 signals for same framework | **HIGH** | Auto-detect, confirm with user |
| 2 signals for same framework | **MEDIUM** | Present evidence, ask user to confirm |
| 0-1 signals or conflicting | **LOW** | Present findings, ask user to choose |

### 6. Present Detection Results

**IF HIGH confidence:**

"**Framework detected: {framework} {version}** (confidence: high)

Evidence:
- [list signals found]

Does this look correct?"

Wait for user confirmation. If confirmed, proceed. If corrected, use user's answer.

**IF MEDIUM confidence:**

"**Probable framework: {framework}** (confidence: medium)

Evidence found:
- [list signals found]

Missing signals:
- [list expected but not found]

Is this correct, or would you like to specify the framework?"

Wait for user response.

**IF LOW confidence:**

"**I couldn't reliably detect the framework.**

Here's what I found:
- [list all signals, even conflicting ones]

**What framework is this project using?**
- **[W]** WordPress (specify version if known)
- **[D]** Drupal (specify version if known)
- **[O]** Other (please describe)"

Wait for user selection.

### 7. Load Framework Profile

Once framework is confirmed (by detection or override):

- Construct profile path: `{frameworksPath}{framework}.md`
- Attempt to load the profile file
- IF profile exists: read and store in memory
- IF profile does NOT exist (e.g., "Other" framework or missing profile):
  - Note that no profile is available
  - "No framework profile found for {framework}. LCS will proceed with generic analysis. You can create a profile later at `{frameworksPath}{framework}.md` using the template."

Store in memory: `framework`, `framework_version`, `detection_confidence`, `profile_loaded` (true/false)

### 8. Proceed to MCP Check

Display: "**Framework: {framework} {version} — Proceeding to MCP tools check...**"

#### Menu Handling Logic:

- After framework is confirmed and profile loaded, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step once framework is confirmed
- IF user wants to discuss detection results: respond, then proceed

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All 4 detection signals executed
- Confidence score calculated correctly
- User confirmed or overrode the detection
- Framework profile loaded (or absence noted)
- Framework, version, confidence, and profile status stored in memory

### ❌ SYSTEM FAILURE:

- Guessing framework without running signals
- Not presenting evidence to the user
- Treating user override as an error case
- Attempting to write files in this step
- Skipping signals because "it's obviously WordPress"

**Master Rule:** Evidence-based detection only. The user's confirmation or override is always the final authority.
