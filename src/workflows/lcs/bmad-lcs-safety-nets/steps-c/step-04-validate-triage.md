---
name: 'step-04-validate-triage'
description: 'Run all safety nets, validate green baseline, triage any failures with user'

nextStepFile: './step-05-discovery-integration.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
safetyNetBuilderArtifact: '{output_folder}/lcs/safety-nets/safety-net-builder-report.md'
staticGuardArtifact: '{output_folder}/lcs/safety-nets/static-guard-report.md'
triageProtocol: '../data/triage-protocol.md'
---

# Step 4: Validate & Triage

## STEP GOAL:

To run all deployed safety nets (smoke tests, e2e tests, visual snapshots, linting) against the current codebase and establish a green baseline. Triage any failures collaboratively with the user using the failure triage protocol (pass / known_failure / fix / env).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip validation — green baseline is mandatory
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — validating the safety nets your team deployed
- ✅ This is the quality gate — nothing proceeds without a green (or triaged) baseline
- ✅ Be methodical — run tests in order (fast → slow), triage each failure category
- ✅ Collaborate with the user on triage decisions — don't decide alone

### Step-Specific Rules:

- 🎯 Focus on running tests and triaging results
- 🚫 FORBIDDEN to modify test files — if a test needs fixing, note it, don't fix it here
- 🚫 FORBIDDEN to skip failures without triage classification
- 💬 Present failures clearly with enough context for triage decisions
- 📋 Use subprocess Pattern 1 (Grep) for scanning test output if available

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with validation results
- 📖 Track: tests_run, tests_passed, tests_failed, triage_results
- 🚫 Do NOT proceed to step 05 until baseline is green or all failures triaged

## CONTEXT BOUNDARIES:

- Available: both teammate artifacts complete, safety branch active, test files + config files in repo
- Focus: test execution and failure triage only
- Limits: do not write new tests, do not modify existing tests, do not modify project code
- Dependencies: step 03 completed — both teammate artifacts confirmed

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Review Teammate Artifacts

Read `{safetyNetBuilderArtifact}` and `{staticGuardArtifact}` to understand what was deployed.

Extract: test file lists, fixture strategy, linting config, hooks, CI config, and any markers (`NEEDS_MANUAL_VERIFICATION`, `BASELINE_PENDING`, `LINT_BASELINE_PENDING`).

Present summary table with categories (smoke/e2e/snapshot/lint/hooks/CI), file counts, and any markers noted.

### 2. Run Smoke Tests (FIRST — fastest)

**Execute smoke tests:**
- Run: `npx playwright test tests/smoke/ --reporter=list` (or equivalent from teammate artifact)
- Capture output

**If Playwright MCP available:**
- Use MCP to execute tests and capture results

**If Playwright MCP NOT available (fallback):**
- Run via CLI command
- If CLI not available: instruct user to run manually, paste results

**Present results:**

"**Smoke tests : {X}/{Y} passés**

| Test | Statut | Durée |
|------|--------|-------|
| {test_name} | ✅ PASS / ❌ FAIL | {time} |"

**If ALL pass:** "Smoke tests verts. ✅"
**If ANY fail:** Proceed to triage (section 6) for failed tests before continuing.

### 3. Run Linting

**Execute linting:**
- Run the lint command from Static Guard's configuration (e.g., `npx eslint .` or `phpcs`)
- Capture output

**Present results:**

"**Linting : {X} erreurs, {Y} avertissements**

| Catégorie | Compte | Sévérité |
|-----------|--------|----------|
| {rule_category} | {count} | error/warning |"

**If ZERO errors:** "Linting propre. ✅"
**If errors exist:** These are BASELINE errors (existing in code before safety-nets). Document count and categories. Do NOT triage individual lint errors — they're informational.

"**Note :** {X} erreurs de linting existantes documentées comme baseline. Elles ne bloquent pas la validation — elles seront traitées pendant la phase de refactoring."

### 4. Run E2E Tests (SECOND — longer)

**Execute e2e tests:**
- Run: `npx playwright test tests/e2e/ --reporter=list` (or equivalent)
- Capture output

**Present results:**

"**E2E tests : {X}/{Y} passés**

| Test | Zone | Statut | Durée |
|------|------|--------|-------|
| {test_name} | {zone} | ✅ PASS / ❌ FAIL | {time} |"

**If ALL pass:** "E2E tests verts. ✅"
**If ANY fail:** Proceed to triage (section 6) for failed tests before continuing.

### 5. Run Visual Snapshot Tests (LAST — baseline capture)

**Execute snapshot tests:**
- Run: `npx playwright test tests/snapshots/ --reporter=list --update-snapshots` (first run establishes baselines)
- Capture output

**Present results:**

"**Snapshots : {X} baselines capturées**

| Page | Viewport | Statut |
|------|----------|--------|
| {page_name} | desktop/mobile | ✅ Baseline capturée / ❌ Échec |"

**First run note:** "Première exécution — les baselines visuelles sont maintenant établies. Les prochaines exécutions compareront contre ces références."

**If ANY fail to capture:** Proceed to triage (section 6).

### 6. Failure Triage Protocol

Load `{triageProtocol}` for the triage presentation template and classification codes (P/K/F/E).

**For EACH failed test (from sections 2, 4, 5):**
- Present the failure using the template from `{triageProtocol}` (test name, file, zone, error, context)
- Present the 4 classification options (P: Pass, K: Known failure, F: Fix, E: Environment)
- Wait for user classification

**Record triage results** in a summary table. After all failures triaged, apply actions per classification (see `{triageProtocol}`).

### 7. Determine Baseline Status

**Green baseline** = ALL tests pass OR all failures are triaged as `known_failure` or `pass`

**If GREEN:**

"**✅ Baseline verte confirmée !**

| Catégorie | Total | Passés | Known Failures | À corriger |
|-----------|-------|--------|----------------|------------|
| Smoke | {n} | {n} | {n} | {n} |
| E2E | {n} | {n} | {n} | {n} |
| Snapshots | {n} | {n} | {n} | {n} |
| Linting | — | — | {errors} baseline | — |

Le filet de sécurité est en place. Toute modification future du code sera détectée par ces tests."

**If NOT GREEN (fix tests remain):**

"**⚠️ Baseline partiellement verte.**

{X} tests marqués 'fix' nécessitent une correction. Options :

- **[R] Relancer le teammate** — re-spawn pour corriger les tests défectueux
- **[M] Manuel** — noter les corrections nécessaires et continuer (corrections manuelles plus tard)
- **[C] Continuer** — accepter la baseline partielle et avancer"

Wait for user decision:
- If R: Re-spawn the appropriate teammate with fix list. Return to step 03 for monitoring.
- If M: Document fix list in sidecar-state, continue to step 05.
- If C: Accept partial baseline, continue to step 05.

### 8. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `validation` task status to `done`
- Store `baseline_status`: green / partial
- Store `tests_summary`: counts per category
- Store `known_failures`: list of triaged known failures
- Store `pending_fixes`: list of tests needing fix (if any)

### 9. Present Validation Summary and Proceed

"**Validation terminée.**

- **Baseline :** {verte / partielle}
- **Smoke tests :** {X}/{Y} ✅
- **E2E tests :** {X}/{Y} ✅
- **Snapshots :** {X} baselines capturées
- **Linting :** {X} erreurs baseline documentées
- **Known failures :** {X}
- **Corrections en attente :** {X}

**Passage à l'intégration des découvertes...**"

Display: **[C] Continuer vers l'intégration des découvertes**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}`, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All test categories executed in correct order (smoke → lint → e2e → snapshots)
- Results presented clearly with counts and status tables
- Every failure triaged with user using the protocol (pass/known_failure/fix/env)
- Baseline status determined (green or partial)
- Sidecar-state updated with validation results
- Proceeding to step 05 with clear baseline status

### ❌ SYSTEM FAILURE:

- Skipping any test category
- Not triaging failures (just ignoring them)
- Classifying failures without user input
- Modifying test files during validation
- Proceeding with untriaged failures
- Not recording triage results
- Not updating sidecar-state with validation summary

**Master Rule:** Every failure MUST be triaged through the protocol with user input. Green baseline means all tests pass or all failures have been classified. No shortcuts — this is the quality gate.
