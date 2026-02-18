---
name: 'step-02-test-cycle'
description: 'Execute the per-zone testing loop: baseline check, spawn Deep Tester, two-category validation, user approval'

nextStepFile: './step-03-discovery-integration.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
deepTestingReportPath: '{output_folder}/lcs/deep-testing-report.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets-report.md'
spawnPromptDeepTester: '../data/spawn-prompt-deep-tester.md'
testStrategyGuide: '../data/test-strategy-guide.md'
validationProtocol: '../data/validation-protocol.md'
deepTesterWorkflow: '../teammates/deep-tester-workflow.md'
frameworksPath: '{project-root}/src/modules/lcs/data/frameworks/'
capabilityMatrixPath: '{output_folder}/lcs/pre-flight/capability-matrix.md'
---

# Step 2: Test Cycle

## STEP GOAL:

To execute the per-zone testing loop: for each prioritized zone, verify the baseline, spawn the Deep Tester teammate, validate results using the two-category protocol (safety nets vs. new tests), triage failures with the user, and approve — one zone at a time.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the baseline check before any zone cycle
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — orchestrating deep test coverage zone by zone
- ✅ Zone discipline: ONE zone at a time, write tests, validate, get approval
- ✅ The developer makes the final call on each zone — you present results, they decide
- ✅ Be factual and concise — report clearly what the Deep Tester produced and what the validation found

### Step-Specific Rules:

- 🎯 Focus ONLY on the test cycle — do not generate the final report (that's step 04)
- 🚫 FORBIDDEN to approve a zone without explicit user approval
- 🚫 FORBIDDEN to skip baseline check
- 💬 Present validation results using the two-category format from `{validationProtocol}`
- 📋 Update sidecar state and report after EVERY zone outcome
- 🎯 Use subprocess for baseline check (Pattern 1) — run safety net suite, return pass/fail

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly — it is a loop
- 💾 Update `{sidecarStatePath}` after every zone outcome
- 💾 Append zone result to `{deepTestingReportPath}` after every zone outcome
- 📖 Track: currentZone, completedZones, remainingZones, baselineGreen
- 🚫 NEVER write tests yourself — that's the Deep Tester's job

## CONTEXT BOUNDARIES:

- Available: step-01 completed — prioritized zones in sidecar-state, framework profile loaded, report initialized
- Focus: per-zone test cycle only
- Limits: do not generate final report, do not re-prioritize zones
- Dependencies: sidecar-state has remainingZones, safety nets are green

## MANDATORY SEQUENCE

**CRITICAL:** This is a LOOP. Execute sections 1-8 for each zone. After all zones processed, proceed to section 9.

---

### 0. Read Current State

Read `{sidecarStatePath}` and load: `remainingZones`, `completedZones`, `baselineGreen`.

**If `remainingZones` is empty AND `currentZone` is null:** "**Toutes les zones ont été traitées.**" → Load, read entirely, then execute `{nextStepFile}`

**If `currentZone` is not null with status `validating` or `awaiting-approval`:** Resume validation for the interrupted zone — skip to section 4 or 5 as appropriate.

**Otherwise:** "**Cycle de test — {N} zone(s) restante(s)**" — take the FIRST zone as current.

---

### ── BEGIN LOOP (per zone) ──

### 1. Baseline Check

Load `{validationProtocol}` for the baseline check procedure.

**Run ONLY the existing safety nets** (Category 1 from the validation protocol) to verify green baseline.

Launch a subprocess that executes the safety net suite and returns pass/fail. If subprocess unavailable: execute in main thread.

- **GREEN:** "✅ **Baseline verte.** Cycle pour la zone `{current_zone}` autorisé." → Update `baselineGreen: true` in `{sidecarStatePath}`
- **RED:** "🛑 **STOP — Baseline rouge.** Les safety nets ont échoué AVANT de commencer cette zone. Ceci indique un problème de régression." → Present failure details. **HALT** — user must investigate and fix.

### 2. Spawn Deep Tester

Read `{spawnPromptDeepTester}` and inject zone context:
- Zone name, zone files, risk score, coverage gap, recommended depth
- Refactoring changes from the refactoring report
- Framework profile from `{frameworksPath}`
- Fixture strategy from safety-nets
- Playwright availability from `{capabilityMatrixPath}`
- Path to `{testStrategyGuide}` for the Deep Tester to load
- Path to `{deepTesterWorkflow}` for execution instructions

Update `{sidecarStatePath}`: set `currentZone` with name, status: `spawning`, retryCount: 0.

**If sub-agent available:** Spawn Deep Tester as headless sub-agent. If spawn fails → retry once. If retry fails → flag `spawn-failed`, **go to section 7**.

**If sub-agent NOT available:** "Exécution séquentielle." → Load and execute `{deepTesterWorkflow}` with injected context.

Update sidecar: `status: "testing"`. "**Deep Tester lancé** sur la zone `{current_zone}`."

### 3. Wait for Completion

**Sub-agent:** Monitor for Deep Tester's artifact file (`{output_folder}/lcs/deep-testing-artifacts/zone-{zone_slug}.md`). Detect completion via frontmatter `status: complete`.

**Sequential:** Completion immediate after section 2.

Update sidecar: `status: "validating"`

### 4. Two-Category Validation

Load `{validationProtocol}` for the post-zone validation procedure.

**Run the two-category validation:**

**Category 1 — Safety Nets (existing tests):**
Run the full safety net suite. ALL must pass.
- **PASS:** "✅ **Safety nets — baseline intacte.**"
- **FAIL:** "🛑 **Régression détectée !** Les safety nets échouent après l'ajout des nouveaux tests." → This indicates test isolation problems. Present failures. Options:
  - [F] Fix — Deep Tester investigates and fixes the interference
  - [S] Skip zone — revert all new test files for this zone
  - If F: re-spawn Deep Tester with interference context, retry validation once. If still failing → force skip.

**Category 2 — Deep Tests (new tests):**
Run the new tests written by the Deep Tester. Results are informational.
- Record: passing count, failing count
- For each failure: prepare triage context (test name, assertion, expected vs actual)

### 5. Triage & Present Results

Load the Deep Tester's artifact for this zone.

**Present two-category results:**

"**Validation — Zone : `{current_zone}`**

**Category 1 — Safety Nets (baseline) :** {✅ ALL PASS / 🛑 FAILURES}

**Category 2 — Deep Tests (nouveaux) :**
- Passing : {N} tests
- En échec : {N} tests

**Tests écrits :** {unit count} unit, {integration count} integration, {edge count} edge cases
**Découvertes :** {N} bugs, {N} code non-testable, {N} comportements surprenants"

**If Category 2 has failures, triage each one:**

For each failing new test, present:
"**Test :** `{test_name}`
**Attendu :** {expected} **Obtenu :** {actual}
Classification : [K] Known issue / [F] Fix (test incorrect) / [E] Environnement"

Wait for user to classify each failure. Update the artifact accordingly.

### 6. Zone Approval

After triage is complete:

"**Résumé zone `{current_zone}` :**
- Tests ajoutés : {total count}
- Passing : {N} / Known issues : {N} / Fixed : {N}
- Baseline : ✅ Intacte
- Découvertes : {brief summary}

**Approuver cette zone ?**"

Display: **[A] Approuver / [S] Skip zone / [R] Retry (re-spawn Deep Tester)**

#### Menu Handling Logic:

- IF A: Proceed to section 6a (Approve)
- IF S: Proceed to section 6b (Skip)
- IF R: Proceed to section 6c (Retry)
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input
- NEVER approve without explicit 'A' from user

### 6a. Approve

- Flag zone as `approved` in `{sidecarStatePath}`
- Append zone results to `{deepTestingReportPath}` "Zone Results" section:
  - Zone name, tests written (by type), passing/failing, known issues, discoveries
- Move zone from `remainingZones` to `completedZones` with status and test counts
- Clear `currentZone`

"✅ **Zone `{current_zone}` approuvée.** Tests intégrés." → **Go to section 8**

### 6b. Skip

- Flag zone as `skipped` in `{sidecarStatePath}`
- Append brief skip note to `{deepTestingReportPath}`
- Move zone to `completedZones` with status `skipped`
- Clear `currentZone`
- Optionally: remove test files written for this zone (ask user)

"⏭️ **Zone `{current_zone}` — skip.**" → **Go to section 8**

### 6c. Retry

- Clear the Deep Tester's artifact for this zone
- Optionally: collect user feedback on what to change
- Reset `currentZone.retryCount += 1`
- If retryCount > 1 → "Maximum de retries atteint. [A] Approuver en l'état / [S] Skip"
- Otherwise → **Return to section 2** (re-spawn with feedback context)

### 7. Update State (non-approval outcomes)

For spawn-failed or forced-skip scenarios:
- Move zone to `completedZones` with appropriate status flag
- Clear `currentZone`
- Append note to `{deepTestingReportPath}`
→ **Go to section 8**

### 8. Next Zone

"**Progression : {completed}/{total} zones**

| Zone | Statut | Tests |
|------|--------|-------|
{table of completedZones}

**Zones restantes :** {N}"

**If more zones in `remainingZones`:**

Display: **[C] Continuer vers la prochaine zone / [X] Arrêter et reprendre plus tard**

- IF C: **Return to section 1** (Baseline Check) with next zone
- IF X: Update `{sidecarStatePath}` + `{deepTestingReportPath}` frontmatter (stepsCompleted, lastStep, lastContinued). "**Session sauvegardée. Reprenez avec step-01b.**" → **HALT**

**If `remainingZones` is empty:** "**Toutes les zones ont été traitées !**" → **Go to section 9**

### ── END LOOP ──

---

### 9. Proceed to Discovery Integration

Update `{deepTestingReportPath}` frontmatter: append `step-02-test-cycle` to `stepsCompleted`, set `lastStep`.

"**Tous les cycles terminés. Passage à l'intégration des découvertes.**"

Load, read entirely, then execute `{nextStepFile}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Baseline verified GREEN before every zone cycle
- Deep Tester spawned with full injected context (zone, strategy guide, framework profile)
- Two-category validation executed correctly (safety nets MUST pass, new tests informational)
- Triage completed with user classification for every failing new test
- User approval obtained before accepting each zone
- Sidecar state updated after every zone outcome
- Report updated progressively after every zone outcome
- Session pause/resume supported via [X] option

### ❌ SYSTEM FAILURE:

- Skipping baseline check
- Approving a zone without user input
- Not distinguishing between safety net failures (critical) and new test failures (informational)
- Writing tests directly (that's the Deep Tester's job)
- Not updating sidecar state after a zone
- Not appending to report after a zone
- Allowing unlimited retries (max 1 retry)
- Not loading the validation protocol or test strategy guide

**Master Rule:** ZONE discipline. One zone, one Deep Tester spawn, two-category validation, one approval. Update state on EVERY exit path. No exceptions.
