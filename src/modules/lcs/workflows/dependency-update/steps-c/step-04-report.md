---
name: 'step-04-report'
description: 'Finalize the dependency update report, update sidecar state, and recommend next workflow'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
reportOutputPath: '{output_folder}/lcs/dependency-update-report.md'
---

# Step 4: Generate Report

## STEP GOAL:

To finalize the dependency update report with remaining packages, recommendations, and next steps. Update the sidecar state to mark the dependency-update phase as complete and recommend the next workflow (Refactoring — Phase 3).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are closing out the dependency update phase
- ✅ Be clear about what was accomplished and what remains
- ✅ Provide actionable recommendations for next steps

### Step-Specific Rules:

- 🎯 Focus ONLY on finalizing the report and closing the phase
- 🚫 FORBIDDEN to execute any additional updates
- 💬 Present a clear, complete summary
- 📋 This is the FINAL step — no nextStepFile

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Finalize `{reportOutputPath}` with all remaining sections
- 📖 Update `{sidecarStatePath}` to mark phase complete

## CONTEXT BOUNDARIES:

- Available: execution results from step 03, report with Updates Applied and Failures tables populated
- Focus: finalize report, close phase, recommend next
- Limits: do not execute updates
- Dependencies: step-03 execution must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. Load Current Report

Read `{reportOutputPath}` to see what's been populated by step 03.

### 2. Populate Remaining Packages Section

For all packages that were NOT updated (excluded by user, deferred, or skipped due to circuit breaker):

Update the "Remaining Packages" section in `{reportOutputPath}`:

| # | Package | Current | Latest | CVSS | Reason Not Updated |
|---|---------|---------|--------|------|--------------------|
{table of all remaining packages with reasons}

**Reason categories:**
- "Exclu par le développeur" — user-excluded in step 01
- "Échec — rollback effectué" — failed in step 03 (already in Failures table, reference here)
- "Circuit breaker — non tenté" — skipped due to circuit breaker
- "Batch non approuvé" — developer chose not to include this batch
- "Différé — maintenance" — low priority, deferred

### 3. Generate Recommendations

Update the "Recommendations" section in `{reportOutputPath}`:

"## Recommendations

### Immediate Actions
{If any critical/high vulnerabilities remain:}
- ⚠️ **{N} vulnérabilités critiques/hautes non résolues** — Nécessitent une attention manuelle
  {list each with reason it wasn't updated}

### Failed Packages — Manual Review Required
{If any packages failed:}
- Les packages suivants ont échoué à la validation safety nets. Investigation manuelle recommandée :
  {list each with failure details}

### Deferred Updates
{If maintenance batch was skipped:}
- {N} packages de maintenance peuvent être mis à jour lors de la prochaine itération

### Phase 3 Readiness
{If majority of updates succeeded:}
- ✅ Le projet est prêt pour la Phase 3 — Refactoring
- Les safety nets sont vertes avec les dépendances mises à jour
- La couverture de sécurité a été améliorée

{If significant failures:}
- ⚠️ Résoudre les {N} packages échoués avant de passer au Refactoring
- Certains échecs peuvent indiquer des incompatibilités nécessitant du refactoring ciblé

### Next Workflow
- **Recommandé :** Refactoring (Phase 3) — Refactoring ciblé pour testabilité
- **Alternatif :** Re-exécuter dependency-update pour les packages restants après investigation"

### 4. Finalize Report Frontmatter

Update `{reportOutputPath}` frontmatter:
- Append `'step-04-report'` to `stepsCompleted`
- Set `lastStep` to `'step-04-report'`
- Set `date` to current date
- Verify `updatedCount`, `failedCount`, `skippedCount` are accurate

### 5. Update Sidecar State — Phase Complete

Update `{sidecarStatePath}`:
- Set dependency-update task `report` to `done`
- Set `consolidation.current_phase` to `dependency-update` (completed)
- Set `consolidation.last_session` to current date
- Record summary metrics:
  - `dependency_update.total_updated`: {count}
  - `dependency_update.total_failed`: {count}
  - `dependency_update.total_skipped`: {count}
  - `dependency_update.critical_remaining`: {count}

### 6. Present Final Summary

"**Mise à jour des dépendances — Phase 2b terminée.**

---

**Résultats finaux :**

| Métrique | Valeur |
|----------|--------|
| Packages analysés | {total} |
| ✅ Mis à jour | {updated} |
| ❌ Échoués (rollback) | {failed} |
| ⏭️ Ignorés/Différés | {skipped} |
| Safety nets | {GREEN/YELLOW} |

**Vulnérabilités résolues :**
- Critiques : {resolved}/{total}
- Hautes : {resolved}/{total}
- Moyennes : {resolved}/{total}

**Rapport complet :** `{reportOutputPath}`

---

**Prochaine étape recommandée :**

🔧 **Refactoring (Phase 3)** — Refactoring ciblé pour améliorer la testabilité du code legacy, maintenant que les dépendances sont à jour et les safety nets en place.

{If failures exist: '⚠️ Avant le refactoring, investiguer les ' + failed_count + ' packages échoués listés dans le rapport.'}

---

**Merci d'avoir suivi le processus de mise à jour. Le rapport est sauvegardé et le sidecar-state est mis à jour.**"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Remaining packages section populated with reasons
- Recommendations generated based on actual results
- Report frontmatter finalized (stepsCompleted complete)
- Sidecar-state updated (phase complete, metrics recorded)
- Clear final summary presented
- Next workflow recommended (Refactoring — Phase 3)
- No additional updates executed

### ❌ SYSTEM FAILURE:

- Not populating remaining packages section
- Not generating recommendations
- Not updating sidecar-state to mark phase complete
- Executing additional updates in this step
- Not presenting final summary
- Not recommending next workflow

**Master Rule:** This is the CLOSING step. Finalize everything, leave no loose ends, and hand off cleanly to the next phase.
