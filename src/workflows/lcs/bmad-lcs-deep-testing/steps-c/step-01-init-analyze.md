---
name: 'step-01-init-analyze'
description: 'Verify deep-testing prerequisites, load risk map and reports, analyze coverage gaps, prioritize zones, create report from template'

nextStepFile: './step-02-test-cycle.md'
continueFile: './step-01b-continue.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
riskMapPath: '{output_folder}/lcs/risk-map.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets-report.md'
refactoringReportPath: '{output_folder}/lcs/refactoring-report.md'
deepTestingReportPath: '{output_folder}/lcs/deep-testing-report.md'
reportTemplatePath: '../templates/report-template.md'
capabilityMatrixPath: '{output_folder}/lcs/pre-flight/capability-matrix.md'
frameworksPath: '{project-root}/_bmad/lcs/data/frameworks/'
---

# Step 1: Initialize & Analyze Coverage

## STEP GOAL:

To verify that deep-testing prerequisites are met (refactoring complete, safety nets green, risk map available), load and analyze existing test coverage against the risk map, identify coverage gaps by zone, and collaboratively prioritize zones with the user for deep testing.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are preparing the deep testing phase — coverage analysis and zone prioritization are key
- ✅ The developer brings their project context and coverage priority decisions
- ✅ Be factual and concise — this is technical orchestration with targeted collaboration

### Step-Specific Rules:

- 🎯 Focus ONLY on prerequisites, input discovery, coverage analysis, and zone prioritization
- 🚫 FORBIDDEN to write tests, spawn teammates, or run test suites — that's step 02
- 💬 Present coverage gaps clearly with risk data for informed zone prioritization
- 📋 If deep-testing tasks already show progress → route to step-01b (continuation)
- 🎯 Use subprocess to load and parse risk map + safety nets report + refactoring report (Pattern 3) — return only zones and coverage gaps

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all collected data in memory for cycle execution: prioritized_zones, coverage_gaps, framework_profile
- 📖 Track progress via sidecar-state task statuses
- 🚫 Do NOT write tests, spawn teammates, or run test suites in this step

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: prerequisites + coverage analysis + zone prioritization only
- Limits: do not write tests, spawn teammates, or run test suites
- Dependencies: refactoring phase (Phase 3) must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Check for Continuation

Read `{sidecarStatePath}`.

**Check deep-testing tasks:**
- If deep-testing phase has `currentZone` with a non-null name, or `completedZones` is non-empty → this is a continuation scenario
- Load, read entirely, then execute `{continueFile}`
- If deep-testing phase not yet started or no progress → this is a fresh run, continue below

### 2. Verify Prerequisites

**Check refactoring completion:**
- Read `{sidecarStatePath}` → verify refactoring phase has status `done`
- If refactoring NOT complete → ERROR: "Le refactoring (Phase 3) doit être complété avant de lancer le deep testing. Complétez le refactoring d'abord."

**Check safety nets baseline:**
- Verify `{safetyNetsReportPath}` exists and confirms green baseline
- If baseline not green → ERROR: "Les safety nets ne sont pas vertes. Corrigez les échecs avant de lancer le deep testing."

**Check risk map exists:**
- Verify `{riskMapPath}` exists and has frontmatter `status: complete`
- If missing or incomplete → ERROR: "La risk map est manquante ou incomplète. Elle est générée comme dernière étape de l'audit."

**Check refactoring report:**
- Verify `{refactoringReportPath}` exists and has frontmatter with completion data
- If missing → WARN: "⚠️ Le rapport de refactoring n'est pas disponible. L'analyse de couverture sera basée uniquement sur la risk map."

"**Prérequis vérifiés :**
- Refactoring : ✅ Phase 3 complétée
- Safety Nets : ✅ Baseline verte
- Risk Map : ✅ Disponible
- Rapport refactoring : {✅ Disponible / ⚠️ Non disponible (optionnel)}"

### 3. Load Framework Profile

Read the framework profile from `{frameworksPath}` based on framework detected during pre-flight.

Store framework-specific testing patterns in memory for the Deep Tester teammate (test conventions, file organization, assertion libraries).

### 4. Load Capability Matrix

Read `{capabilityMatrixPath}` to determine available MCP tools.

**Key check:** Is Playwright MCP available?
- If YES → Deep Tester can execute integration/e2e tests during writing
- If NO → Deep Tester writes test files only, user validates manually

Store capability profile in memory for spawn prompt.

### 5. Analyze Coverage Gaps by Zone

Launch a subprocess that loads `{riskMapPath}`, `{safetyNetsReportPath}`, and `{refactoringReportPath}` (if available), cross-references risk zones with existing safety net coverage, and returns only:
- Zone name, risk score, existing coverage level (none / smoke-only / partial / good)
- Refactoring changes applied to this zone (from refactoring report)
- Coverage gap assessment (critical / significant / moderate / minor)
- Recommended test depth (unit + integration + edge / unit + integration / unit only)

If subprocess unavailable: load all files in main thread and extract the same data.

**Present coverage analysis:**

"**Analyse de couverture par zone :**

| # | Zone | Score risque | Couverture existante | Gap | Profondeur recommandée |
|---|------|-------------|---------------------|-----|----------------------|
{table of all zones sorted by risk score × coverage gap descending}

**Total :** {N} zones — {critical} gaps critiques, {significant} significatifs, {moderate} modérés"

### 6. Prioritize Zones

"**Priorisation des zones pour le deep testing**

Basé sur la risk map et l'analyse de couverture, voici l'ordre recommandé (risque × lacune de couverture) :

**Priorité 1 — Gaps critiques** (zones à haut risque avec peu/pas de couverture)
{list critical zones with brief description}

**Priorité 2 — Gaps significatifs**
{list significant zones}

**Priorité 3 — Gaps modérés**
{list moderate zones}

Souhaitez-vous ajuster ces priorités ? Vous pouvez :
- Remonter/descendre une zone
- Exclure une zone temporairement
- Définir des seuils de couverture cibles par zone
- Limiter le nombre de zones pour cette session

Ou tapez **[OK]** pour confirmer l'ordre."

**Wait for user response.**

- If user adjusts priorities → apply changes, re-present
- If user specifies coverage targets → store per-zone thresholds
- If user confirms → store final prioritized zone list in memory

### 7. Create Deep Testing Report

Create `{deepTestingReportPath}` from `{reportTemplatePath}`:
- Fill frontmatter: date, user_name, project_name, stepsCompleted: ['step-01-init-analyze']
- Populate "Zone Results" section header with the full prioritized list as placeholders
- Leave other sections as placeholders for progressive updates

### 8. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `consolidation.current_phase` to `deep-testing`
- Set `consolidation.last_session` to current date
- Initialize deep-testing state:
  - `currentZone`: null
  - `completedZones`: []
  - `remainingZones`: [full prioritized list with recommended test depth]
  - `baselineGreen`: true

### 9. Present Summary and Proceed

"**Résumé de l'initialisation :**

- **Prérequis :** ✅ Vérifiés
- **Framework :** {detected framework}
- **Zones identifiées :** {N} zones avec gaps de couverture
- **Ordre de priorité :** {top 3 zones}
- **Playwright MCP :** {✅ Disponible / ❌ Mode écriture seule}
- **Rapport :** ✅ Initialisé

**Prochaine étape :** Lancement du cycle de test par zone — spawn Deep Tester, écriture des tests, validation."

Display: **[C] Continuer vers le cycle de test**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` with initialization complete, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Refactoring completion verified (Phase 3 done)
- Safety nets baseline verified (green)
- Risk map and reports loaded and analyzed
- Framework profile loaded for Deep Tester context
- Capability matrix checked (Playwright MCP availability)
- Coverage gaps analyzed and presented by zone
- Zones prioritized with user confirmation
- Deep testing report initialized from template
- Sidecar-state updated (current_phase: deep-testing, zones initialized)
- Proceeding to step 02

### ❌ SYSTEM FAILURE:

- Skipping prerequisite checks
- Not loading the risk map or reports completely
- Prioritizing zones without user input
- Not presenting coverage analysis for user confirmation
- Writing tests or spawning teammates (that's for step 02)
- Not updating sidecar-state before proceeding
- Not creating the deep testing report

**Master Rule:** All prerequisites MUST be verified, zones prioritized with user confirmation, and report initialized before proceeding. No shortcuts.
