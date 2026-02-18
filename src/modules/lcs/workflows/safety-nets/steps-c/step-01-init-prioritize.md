---
name: 'step-01-init-prioritize'
description: 'Verify safety-nets prerequisites, load audit/risk-map, define fixture strategy, prioritize zones, handle UNCERTAIN fallback'

nextStepFile: './step-02-spawn.md'
continueFile: './step-01b-continue.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
capabilityMatrixPath: '{output_folder}/lcs/pre-flight/capability-matrix.md'
auditReportPath: '{output_folder}/lcs/audit/audit-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
fixtureStrategies: '../data/fixture-strategies.md'
---

# Step 1: Initialize & Prioritize Safety Nets

## STEP GOAL:

To verify that safety-nets prerequisites are met (audit complete, risk map available), load and analyze the audit findings and risk map, collaboratively define a fixture strategy and prioritize zones for safety net deployment, and handle UNCERTAIN zones with conservative fallback.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are preparing the safety net deployment — prioritization is key
- ✅ The developer brings their project context and priority decisions
- ✅ Be factual and concise — this is technical orchestration with targeted collaboration

### Step-Specific Rules:

- 🎯 Focus ONLY on prerequisites, input discovery, fixture strategy, and prioritization
- 🚫 FORBIDDEN to write any test files or config — that's what teammates do
- 🚫 FORBIDDEN to spawn teammates — that's step 02
- 💬 Report each prerequisite check result clearly
- 📋 If safety-nets tasks already show progress → route to step-01b (continuation)
- 🎯 Use subprocess to load and parse audit report + risk map (Pattern 3) — return only risk zones and priorities

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all collected data in memory for spawn prompts: prioritized_zones, fixture_strategy, uncertain_zones, mcp_tools_available
- 📖 Track progress via sidecar-state task statuses
- 🚫 Do NOT write test artifacts or config files in this step

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: prerequisites + prioritization + fixture strategy only
- Limits: do not write tests, config, or spawn teammates
- Dependencies: audit phase (Phase 1) must be complete, including risk-map generation

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Check for Continuation

Read `{sidecarStatePath}`.

**Check safety-nets tasks:**
- If ANY safety-nets task has status `in_progress`, `done`, or `failed` → this is a continuation scenario
- Load, read entirely, then execute `{continueFile}`
- If ALL safety-nets tasks are `pending` or safety-nets phase not yet started → this is a fresh deployment, continue below

### 2. Verify Prerequisites

**Check audit completion:**
- Read `{sidecarStatePath}` → verify `consolidation.current_phase` is `audit` or later
- Verify ALL audit tasks have status `done`
- If audit NOT complete → ERROR: "L'audit (Phase 1) doit être complété avant de lancer les safety nets. Complétez l'audit d'abord."

**Check audit report exists:**
- Verify `{auditReportPath}` exists and has frontmatter `status: complete`
- If missing or incomplete → ERROR: "Le rapport d'audit est manquant ou incomplet. Relancez l'audit."

**Check risk map exists:**
- Verify `{riskMapPath}` exists and has frontmatter `status: complete`
- If missing or incomplete → ERROR: "La risk map est manquante. Elle est générée comme dernière étape de l'audit (step-05-risk-map-trigger)."

"**Prérequis vérifiés :**
- Audit : ✅ Complété
- Rapport d'audit : ✅ Disponible
- Risk Map : ✅ Disponible"

### 3. Read Capability Matrix

Read `{capabilityMatrixPath}` to understand available MCP tools.

**Present relevant MCP tools for safety nets:**

"**Outils MCP disponibles pour les safety nets :**

| Outil | Status | Impact sur les safety nets |
|-------|--------|---------------------------|
| Playwright MCP | {OK/MISSING} | {E2E tests + visual snapshots / fallback: write-only, manual validation} |
| ESLint MCP | {OK/MISSING} | {Lint validation / fallback: config files only, CLI validation} |

{If Playwright missing: '⚠️ Sans Playwright MCP, les tests e2e et snapshots seront écrits mais non exécutés automatiquement. Validation manuelle requise.'}
{If both available: '✅ Tous les outils critiques sont disponibles. Déploiement complet possible.'}"

### 4. Load and Analyze Risk Map

Launch a subprocess that loads `{riskMapPath}`, extracts all risk zones with their scores, and returns only:
- Zone name, file paths, risk score, risk category (critical/high/medium/low)
- Business impact rating
- UNCERTAIN markers (ADR-005)

If subprocess unavailable: load `{riskMapPath}` in main thread and extract the same data.

**Present risk zones summary:**

"**Zones à risque identifiées :**

| Zone | Score | Catégorie | Impact business | UNCERTAIN |
|------|-------|-----------|-----------------|-----------|
{table of all zones sorted by risk score descending}

**Total :** {N} zones — {critical} critiques, {high} hautes, {medium} moyennes, {low} basses
**Zones UNCERTAIN :** {N} zones marquées comme incertaines (ADR-005)"

### 5. Handle UNCERTAIN Zones

For zones marked UNCERTAIN (ADR-005), apply the two-tier approach:

"**Zones UNCERTAIN ({N} zones) — Approche deux tiers :**
- **Tier 1 (maintenant) :** Smoke tests + snapshots visuels uniquement (pas d'assertions métier)
- **Tier 2 (après Q&A) :** E2E tests complets avec assertions business logic
Les zones UNCERTAIN recevront le Tier 1 automatiquement."

### 6. Define Fixture Strategy

Load `{fixtureStrategies}` for reference. Present the 4 options (A: Database Snapshot, B: Seed Data, C: Mock External Services, D: Hybrid — recommended) with their pros/cons from the data file.

"**Stratégie de données de test**

Avant de déployer les tests, nous devons décider comment gérer les données de test.

{Present 4 options from fixture-strategies.md with brief pros/cons for each}

Quelle stratégie préférez-vous ?"

**Wait for user response.** Store the selected fixture strategy in memory.

### 7. Prioritize Zones for Deployment

"**Priorisation des safety nets**

Basé sur la risk map, voici l'ordre de déploiement recommandé :

**Priorité 1 — Zones critiques** (safety nets en premier)
{list critical zones with brief description}

**Priorité 2 — Zones haute priorité**
{list high zones}

**Priorité 3 — Zones moyennes**
{list medium zones}

**Priorité 4 — Zones basses + UNCERTAIN (Tier 1)**
{list low zones and UNCERTAIN zones with Tier 1 treatment}

Souhaitez-vous ajuster ces priorités ? Vous pouvez :
- Remonter/descendre une zone
- Exclure une zone temporairement
- Ajouter une zone non listée

Ou tapez **[OK]** pour confirmer l'ordre."

**Wait for user response.**

- If user adjusts priorities → apply changes, re-present
- If user confirms → store final prioritized list in memory

### 8. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `consolidation.current_phase` to `safety-nets`
- Set `consolidation.last_session` to current date
- Initialize safety-nets tasks:
  - `safety-net-builder`: `pending`
  - `static-guard`: `pending`
  - `validation`: `pending`
  - `discovery-integration`: `pending`
  - `merge-report`: `pending`
- Store `fixture_strategy` and `prioritized_zones` in sidecar-state

### 9. Present Summary and Proceed

"**Résumé de l'initialisation :**

- **Prérequis :** ✅ Vérifiés
- **Outils MCP :** {summary}
- **Zones à couvrir :** {N} zones ({N_critical} critiques)
- **Zones UNCERTAIN :** {N} zones → Tier 1 (conservateur)
- **Stratégie fixtures :** {selected strategy}
- **Priorité :** {brief priority order}

**Prochaine étape :** Création de la branche de sécurité et spawn des teammates."

Display: **[C] Continuer vers le spawn des teammates**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` with initialization complete, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Audit completion verified (all tasks done, report + risk map exist)
- Capability matrix read and MCP adaptation noted
- Risk map loaded and all zones extracted with scores
- UNCERTAIN zones identified and Tier 1 fallback applied
- Fixture strategy selected collaboratively with user
- Zones prioritized with user confirmation
- Sidecar-state updated (current_phase: safety-nets, tasks initialized)
- Proceeding to step 02

### ❌ SYSTEM FAILURE:

- Skipping prerequisite checks
- Not loading the risk map completely
- Not handling UNCERTAIN zones
- Selecting fixture strategy without user input
- Not presenting priorities for user confirmation
- Writing test files or config (that's for teammates)
- Not updating sidecar-state before proceeding

**Master Rule:** All prerequisites MUST be verified, fixture strategy selected, and priorities confirmed before proceeding. No shortcuts.
