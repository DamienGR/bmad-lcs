---
name: 'step-01-init-discover'
description: 'Detect entry mode, verify prerequisites, load audit artifacts, extract zones (3 levels), map dependencies to zones'

nextStepFile: './step-02-score-prioritize.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
codeAnalysisPath: '{output_folder}/lcs/audit/code-analysis.md'
siteExplorationPath: '{output_folder}/lcs/audit/site-exploration.md'
dependencyScanPath: '{output_folder}/lcs/audit/dependency-scan.md'
auditDataExtraction: '../data/audit-data-extraction.md'
---

# Step 1: Initialize & Discover Zones

## STEP GOAL:

To load all audit findings, systematically extract a 3-level zone hierarchy from the data, and map vulnerable dependencies to zones — preparing all inputs needed for the scoring phase (step 02).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check (unless chained mode)
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — methodical, direct, reassuring
- ✅ You are synthesizing intelligence from 3 audit artifacts into a unified zone model
- ✅ The developer brings their project knowledge — you bring systematic analysis
- ✅ Be factual and concise — this is data extraction with light collaboration

### Step-Specific Rules:

- 🎯 Focus ONLY on loading artifacts, extracting zones, and mapping dependencies
- 🚫 FORBIDDEN to score zones — that's step 02
- 🚫 FORBIDDEN to ask business criticality questions — that's step 03
- 💬 Present zone inventory clearly for developer confirmation
- 🎯 Use subprocess to load audit artifacts (Pattern 3) — return structured data only

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all extracted data in memory: zones_l1[], zones_l2[], zones_l3_candidates[], dep_zone_mapping{}
- 📖 Track progress via sidecar-state task statuses
- 🚫 Do NOT modify any audit artifacts — read-only access

## CONTEXT BOUNDARIES:

- Available: workflow.md initialization, module config loaded, 3 audit artifacts
- Focus: zone extraction and dependency mapping only
- Limits: do not score, do not assess business criticality, do not modify project files
- Dependencies: audit phase (Phase 1) must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Detect Entry Mode

Read `{sidecarStatePath}`.

**Check for chained entry:**
- If sidecar-state contains `triggered_by: audit` or `risk_map_triggered: true` → **Chained mode**
  - "**Mode chaîné détecté** — arrivée directe depuis l'audit. Prérequis validés implicitement."
  - Skip to section 3.
- Otherwise → **Standalone mode**
  - "**Mode autonome** — vérification des prérequis..."
  - Continue to section 2.

### 2. Verify Prerequisites (Standalone Only)

**Check audit completion:**
- Read `{sidecarStatePath}` → verify ALL audit tasks have status `done`
- If audit NOT complete → ERROR: "L'audit (Phase 1) doit être complété avant de générer la risk map. Complétez l'audit d'abord."

**Check 3 audit artifacts exist:**
- Verify `{codeAnalysisPath}` exists with frontmatter `status: complete`
- Verify `{siteExplorationPath}` exists with frontmatter `status: complete`
- Verify `{dependencyScanPath}` exists with frontmatter `status: complete`
- If ANY missing → ERROR: "Artefact d'audit manquant : {missing file}. Relancez l'audit."

"**Prérequis vérifiés :**
- Audit : ✅ Complété
- Code analysis : ✅ Disponible
- Site exploration : ✅ Disponible
- Dependency scan : ✅ Disponible"

### 3. Load Audit Artifacts

Load `{auditDataExtraction}` for extraction methodology reference.

Launch a subprocess that loads all 3 audit artifacts and extracts only structured data:
- From `{codeAnalysisPath}`: file inventory, complexity metrics, import counts, git history, test coverage
- From `{siteExplorationPath}`: page inventory, user journeys, interactive elements, forms
- From `{dependencyScanPath}`: vulnerable dependencies (CVE IDs, severity, affected packages)

If subprocess unavailable: load each artifact in main thread and extract the same data.

"**Données d'audit chargées :**
- **Fichiers analysés :** {N} fichiers dans code analysis
- **Pages explorées :** {N} pages dans site exploration
- **Dépendances scannées :** {N} dépendances ({N_vulnerable} avec CVEs)"

### 4. Extract Level 1 Zones (Functional)

From site exploration data, identify user journeys and functional areas:
1. List all user journeys
2. Group pages by journey/functional area
3. Each journey = one Level 1 zone (kebab-case naming)

If no user journeys defined → derive from URL structure (e.g., `/admin/*` → `admin`, `/shop/*` → `shop`).

### 5. Extract Level 2 Zones (Component)

From code analysis data, identify logical component groups:
1. Group files by directory structure, shared prefix, or related functionality
2. Cross-reference each L2 zone to its L1 parent
3. Apply rules: minimum 2 files per zone, maximum ~15 L2 zones total
4. Orphan files → `misc-utilities` zone

### 6. Tag Level 3 Candidates

From code analysis complexity metrics, identify extreme files:
- Lines > 500, cyclomatic complexity > 20, or import count > 10
- Tag as L3 candidates (actual drill-down applied only after scoring in step 02)

### 7. Map Dependencies to Zones

From dependency scan, for each vulnerable dependency (critical CVEs):
1. Find files that import/require/use that dependency (from code analysis file inventory)
2. Map those files to their L2 zones
3. Build the mapping table:

| Dependency | CVE(s) | Severity | L2 Zones Affected |
|-----------|--------|----------|-------------------|
| {package} | {CVE-ID} | {severity} | {zone1, zone2} |

If no vulnerable dependencies → "Aucune dépendance vulnérable. Modificateur × 1.0 pour toutes les zones."

### 8. Identify UNCERTAIN Zones

Mark zones as UNCERTAIN (ADR-005) when:
- File purpose unclear from code analysis
- No matching user journey in site exploration
- Insufficient data to categorize

"**Zones UNCERTAIN :** {N} zones marquées — seront présentées au développeur pour classification en étape 03."

### 9. Present Zone Inventory

"**Inventaire des zones extraites :**

**Niveau 1 — Fonctionnel ({N} zones) :**
{list L1 zones with page count}

**Niveau 2 — Composant ({N} zones) :**

| Zone L2 | Parent L1 | Fichiers | Dépendances vulnérables | L3 candidats |
|---------|-----------|----------|------------------------|--------------|
{table of all L2 zones}

**Zones UNCERTAIN ({N}) :**
{list with reason}

**Mapping dépendances → zones :**
{summary table}

Confirmez-vous cet inventaire ? Vous pouvez :
- Fusionner/séparer des zones
- Renommer une zone
- Reclasser un fichier

Ou **[C]** pour confirmer et passer au scoring."

### 10. Update Sidecar State and Proceed

Update `{sidecarStatePath}`:
- Set `risk_map.zone_discovery` task to `done`
- Store `zone_count_l1`, `zone_count_l2`, `uncertain_count`

Display: **[C] Continuer vers le scoring des zones**

#### Menu Handling Logic:

- IF C: Update sidecar-state, then load, read entire file, then execute `{nextStepFile}`
- IF user adjusts zones: apply changes, re-present inventory, redisplay menu
- IF any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting zone inventory
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Entry mode detected correctly (chained vs standalone)
- Prerequisites verified (standalone) or skipped (chained)
- All 3 audit artifacts loaded and parsed
- 3-level zone hierarchy extracted (L1 functional, L2 component, L3 candidates)
- Vulnerable dependencies mapped to L2 zones
- UNCERTAIN zones identified (ADR-005)
- Zone inventory presented and confirmed by developer
- Sidecar-state updated with zone discovery results
- Proceeding to step 02 (scoring)

### ❌ SYSTEM FAILURE:

- Skipping prerequisite checks in standalone mode
- Not loading all 3 audit artifacts
- Scoring zones (forbidden in this step)
- Not identifying UNCERTAIN zones
- Not presenting zone inventory for developer confirmation
- Modifying audit artifacts
- Not updating sidecar-state

**Master Rule:** All 3 audit artifacts MUST be loaded, zones MUST be extracted at 3 levels, and the zone inventory MUST be confirmed by the developer before proceeding. No shortcuts.
