---
name: 'step-05-risk-map-trigger'
description: 'Finalize audit phase, present summary, propose Risk Map workflow'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
auditReportPath: '{output_folder}/lcs/audit/audit-report.md'
openQuestionsPath: '{output_folder}/lcs/audit/open-questions.md'
depScanPath: '{output_folder}/lcs/audit/dependency-scan.md'
codeAnalysisPath: '{output_folder}/lcs/audit/code-analysis.md'
siteExplorationPath: '{output_folder}/lcs/audit/site-exploration.md'
---

# Step 5: Audit Finalization & Risk Map Proposal

## STEP GOAL:

To finalize the audit phase by verifying all tasks are complete, presenting a comprehensive summary to the developer, and proposing the next phase (Risk Map generation) without auto-chaining.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER auto-chain to the next workflow
- 📖 CRITICAL: Read the complete step file before taking any action
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — wrapping up the audit and handing control back
- ✅ The developer should feel confident that the audit was thorough
- ✅ Present a clear picture of what was accomplished and what comes next
- ✅ This is a natural pause point — the developer decides when to continue

### Step-Specific Rules:

- 🎯 Focus on verification, summary, and proposal
- 🚫 FORBIDDEN to auto-chain to risk-map workflow — user must explicitly trigger it
- 🚫 FORBIDDEN to re-analyze or modify any artifacts
- 💬 Celebrate completion — this is a milestone
- 📋 Return to Conrad's main menu after proposal

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state: all audit tasks verified
- 📖 No artifact writing — only state update
- 🚫 Do NOT start any new analysis

## CONTEXT BOUNDARIES:

- Available: all 5 audit artifacts, sidecar-state with task statuses
- Focus: verification, summary, proposal
- Limits: read-only on artifacts — no modifications
- Dependencies: batched-qa complete (step 04)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Verify All Audit Tasks

Read `{sidecarStatePath}` and verify the status of all audit tasks:

| Task | Expected Status |
|------|----------------|
| code-analysis | done |
| site-exploration | done |
| dependency-scan | done |
| audit-synthesis | done |
| batched-qa | done |
| risk-map | pending |

**If any task (except risk-map) is NOT done:**
- Report which task is incomplete
- "**Attention : la tâche {task_id} n'est pas terminée (statut: {status}).** Vérifiez l'artefact correspondant."
- Do NOT proceed until resolved

**If all tasks done (risk-map stays pending):** Continue to step 2.

### 2. Gather Summary Metrics

Read the following artifacts to extract key metrics (read frontmatter and summary sections only — do not re-analyze):

**From `{codeAnalysisPath}`:**
- Total files analyzed
- Complexity hotspots count
- Framework detected

**From `{siteExplorationPath}`:**
- Total pages mapped
- Key journeys documented

**From `{depScanPath}`:**
- Total vulnerabilities (by severity)
- Outdated packages count

**From `{openQuestionsPath}`:**
- Total questions asked
- Answered count
- Uncertain count

**From `{auditReportPath}`:**
- Risk zones count (from risk indicators table)

### 3. Update Sidecar State

Update `{sidecarStatePath}`:
- Verify `consolidation.current_phase` is `audit`
- Confirm all audit tasks (except risk-map) are `done`
- No status changes needed if all tasks already correctly set

### 4. Present Audit Summary

"**Audit terminé.**

| Métrique | Résultat |
|----------|----------|
| Fichiers analysés | {count} |
| Pages cartographiées | {count} |
| Parcours documentés | {count} |
| Vulnérabilités détectées | {critical} critiques, {high} hautes, {medium} moyennes |
| Packages obsolètes | {count} |
| Questions clarifiées | {answered}/{total} ({uncertain} incertaines) |
| Zones de risque identifiées | {count} |

**Artefacts produits :**
- `audit/code-analysis.md` — Analyse statique du code
- `audit/site-exploration.md` — Exploration du site live
- `audit/dependency-scan.md` — Scan des dépendances
- `audit/audit-report.md` — Rapport d'audit unifié
- `audit/open-questions.md` — Questions & réponses"

### 5. Propose Next Steps

"**Prochaines étapes disponibles :**

- **[RM]** Générer le Risk Map — priorise les zones à risque pour les phases suivantes
- **[ST]** Statut global — voir l'état complet de la consolidation

Le Risk Map utilise les résultats de l'audit pour produire une carte de priorités. Il n'est pas obligatoire de le lancer immédiatement."

**Wait for user response.**

- If user selects **RM** → inform: "Pour lancer le Risk Map, utilisez la commande correspondante depuis le menu principal de Conrad."
- If user selects **ST** → display consolidation status from sidecar-state
- If user asks a question → respond, then redisplay the options
- If user wants to do something else → return to Conrad's main menu

**This is the final step of the audit workflow. Control returns to Conrad's main menu.**

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All audit tasks verified (5 done, risk-map pending)
- Summary metrics extracted from all 5 artifacts
- Comprehensive summary presented to user
- Next steps proposed WITHOUT auto-chaining
- Control returned to Conrad's main menu

### ❌ SYSTEM FAILURE:

- Auto-chaining to risk-map workflow
- Not verifying all task statuses before presenting summary
- Re-analyzing artifacts instead of reading summaries
- Not presenting the artifacts list
- Modifying any artifacts

**Master Rule:** The audit is a milestone. Present it as such — complete summary, clear next steps, developer in control.
