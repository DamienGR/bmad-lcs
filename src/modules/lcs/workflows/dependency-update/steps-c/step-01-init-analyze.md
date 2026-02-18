---
name: 'step-01-init-analyze'
description: 'Verify prerequisites, run baseline safety nets, load audit data, scan dependencies for vulnerabilities and outdated packages'

nextStepFile: './step-02-plan.md'
continueFile: './step-01b-continue.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
capabilityMatrixPath: '{output_folder}/lcs/pre-flight/capability-matrix.md'
auditReportPath: '{output_folder}/lcs/audit/audit-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
reportOutputPath: '{output_folder}/lcs/dependency-update-report.md'
reportTemplate: '../templates/report-template.md'
---

# Step 1: Initialize & Analyze Dependencies

## STEP GOAL:

To verify that dependency-update prerequisites are met (safety nets green, audit complete), run a baseline safety net validation, load and analyze dependency audit data, scan for vulnerabilities and outdated packages, and present the analysis summary.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are preparing the dependency update phase — analysis and verification are key
- ✅ The developer brings their project context and exclusion preferences
- ✅ Be factual and concise — this is technical orchestration

### Step-Specific Rules:

- 🎯 Focus ONLY on prerequisites, baseline check, and dependency analysis
- 🚫 FORBIDDEN to update any packages — that's step 03
- 🚫 FORBIDDEN to propose an update plan — that's step 02
- 💬 Report each prerequisite check result clearly
- 📋 If dependency-update tasks already show progress → route to step-01b (continuation)
- 🎯 Use subprocess to load and parse audit report (Pattern 3) — return only dependency-related findings

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all collected data in memory for plan generation: dependency_scan_results, vulnerability_data, outdated_packages, package_manager_detected, baseline_status
- 📖 Track progress via sidecar-state task statuses
- 🚫 Do NOT update packages or propose plans in this step

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: prerequisites + baseline + dependency analysis only
- Limits: do not update packages, do not propose plan
- Dependencies: safety-nets phase (Phase 2) must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Check for Continuation

Read `{sidecarStatePath}`.

**Check dependency-update tasks:**
- If ANY dependency-update task has status `in_progress`, `done`, or `failed` → this is a continuation scenario
- Load, read entirely, then execute `{continueFile}`
- If ALL dependency-update tasks are `pending` or dependency-update phase not yet started → this is a fresh run, continue below

### 2. Verify Prerequisites

**Check safety-nets completion:**
- Read `{sidecarStatePath}` → verify `consolidation.current_phase` is `safety-nets` or later
- Verify ALL safety-nets tasks have status `done`
- If safety-nets NOT complete → ERROR: "Les safety nets (Phase 2) doivent être complétées avant de lancer la mise à jour des dépendances. Complétez les safety nets d'abord."

**Check audit report exists:**
- Verify `{auditReportPath}` exists and has frontmatter `status: complete`
- If missing or incomplete → ERROR: "Le rapport d'audit est manquant ou incomplet. Relancez l'audit."

**Check risk map exists:**
- Verify `{riskMapPath}` exists and has frontmatter `status: complete`
- If missing or incomplete → ERROR: "La risk map est manquante. Elle est générée comme dernière étape de l'audit."

"**Prérequis vérifiés :**
- Safety Nets : ✅ Complétées
- Rapport d'audit : ✅ Disponible
- Risk Map : ✅ Disponible"

### 3. Baseline Safety Net Check

**CRITICAL: Verify safety nets are green NOW, not just at Phase 2.**

Run the existing safety net suite to confirm the baseline is still green:

"**Vérification du baseline — les safety nets sont-elles toujours vertes ?**"

Execute the safety net test suite (Playwright MCP if available, otherwise CLI).

- If ALL GREEN → "✅ Baseline confirmé — safety nets vertes. On peut procéder."
- If ANY RED → "🛑 STOP — Les safety nets ne sont plus vertes depuis la Phase 2. Quelqu'un a peut-être modifié du code. Corrigez les tests échoués avant de procéder."
  - Present failed tests
  - HALT — do not proceed until baseline is green

### 4. Read Capability Matrix

Read `{capabilityMatrixPath}` to understand available MCP tools.

**Present relevant MCP tools for dependency update:**

"**Outils MCP disponibles :**

| Outil | Status | Impact |
|-------|--------|--------|
| Snyk MCP | {OK/MISSING} | {CVSS scores + vulnerability data / fallback: manual audit data only} |
| Playwright MCP | {OK/MISSING} | {Safety net execution / fallback: CLI-based test runs} |
| Git MCP | {OK/MISSING} | {Atomic commits + rollbacks / fallback: CLI git commands} |

{If Snyk missing: '⚠️ Sans Snyk MCP, on utilisera uniquement les données de l\'audit Phase 1 pour les vulnérabilités.'}
{If all available: '✅ Tous les outils critiques sont disponibles.'}"

### 5. Load and Analyze Dependency Data

Launch a subprocess that loads `{auditReportPath}`, extracts all dependency-related findings, and returns only:
- Package manager detected (composer/npm/yarn)
- List of vulnerable packages with CVSS scores
- List of outdated packages with current/latest versions
- Dependency count and health summary

If subprocess unavailable: load `{auditReportPath}` in main thread and extract the same data.

**If Snyk MCP is available:**
Also query Snyk MCP for current vulnerability data to complement/update audit findings.

### 6. Detect Package Manager

Based on audit data and project files, confirm the package manager:

"**Package manager détecté : {composer/npm/yarn}**
- Fichier manifest : {composer.json / package.json}
- Fichier lock : {composer.lock / package-lock.json / yarn.lock}
- Version : {package manager version if detectable}"

### 7. Present Analysis Summary

"**Analyse des dépendances :**

**Vulnérabilités ({count}) :**

| Package | Version actuelle | CVSS | Sévérité | Description |
|---------|-----------------|------|----------|-------------|
{table sorted by CVSS descending}

**Packages obsolètes ({count}) :**

| Package | Version actuelle | Dernière version | Retard |
|---------|-----------------|------------------|--------|
{table sorted by version gap descending}

**Résumé :**
- Total dépendances : {N}
- Vulnérabilités : {N_critical} critiques, {N_high} hautes, {N_medium} moyennes, {N_low} basses
- Packages obsolètes : {N}
- Package manager : {detected}"

### 8. Ask for Exclusions

"**Souhaitez-vous exclure des packages de la mise à jour ?**

Raisons possibles d'exclusion :
- Incompatibilité connue avec votre code
- Package abandonné (remplacement planifié)
- Contrainte de version spécifique

Listez les packages à exclure, ou tapez **[OK]** pour n'exclure aucun package."

**Wait for user response.**

- If user excludes packages → store exclusion list in memory, mark as excluded
- If user confirms no exclusions → proceed

### 9. Initialize Report and Update Sidecar State

Create `{reportOutputPath}` from `{reportTemplate}` with:
- `project_name`, `date`, `user_name` filled in
- `packageManager` set to detected manager
- `totalPackages` set to total count
- `stepsCompleted: ['step-01-init-analyze']`

Update `{sidecarStatePath}`:
- Set `consolidation.current_phase` to `dependency-update`
- Set `consolidation.last_session` to current date
- Initialize dependency-update tasks:
  - `analysis`: `done`
  - `plan`: `pending`
  - `approval`: `pending`
  - `execution`: `pending`
  - `report`: `pending`

### 10. Present Summary and Proceed

"**Résumé de l'initialisation :**

- **Prérequis :** ✅ Vérifiés
- **Baseline safety nets :** ✅ Vertes
- **Outils MCP :** {summary}
- **Package manager :** {detected}
- **Vulnérabilités :** {count} ({critical} critiques)
- **Packages obsolètes :** {count}
- **Exclusions :** {count or 'aucune'}

**Prochaine étape :** Élaboration du plan de mise à jour priorisé."

Display: **[C] Continuer vers le plan de mise à jour**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` with analysis complete, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Prerequisites verified, baseline green, audit data extracted, package manager detected
- Analysis presented, exclusions collected, report initialized, sidecar-state updated

### ❌ SYSTEM FAILURE:

- Skipping prerequisites or baseline check
- Updating packages or proposing a plan in this step
- Not initializing report or not updating sidecar-state

**Master Rule:** All prerequisites MUST be verified, baseline MUST be green, analysis MUST be complete before proceeding.
