---
name: 'step-03-report'
description: 'Generate the final refactoring report with executive summary, metrics, and recommendations for deep-testing'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
refactoringReportPath: '{output_folder}/lcs/refactoring-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
---

# Step 3: Final Report

## STEP GOAL:

To generate the final refactoring report by synthesizing all cycle outcomes into an executive summary, computing metrics, updating remaining targets, and producing recommendations for the next phase (deep-testing).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — synthesizing refactoring results
- ✅ Be factual, concise, and actionable — the developer needs a clear picture
- ✅ Highlight wins AND remaining risks honestly

### Step-Specific Rules:

- 🎯 Focus ONLY on report generation and workflow completion
- 🚫 FORBIDDEN to start any refactoring or code modification
- 💬 Present the report summary to the user before finalizing
- 📋 This is the FINAL step — mark workflow as complete

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write the final report to `{refactoringReportPath}`
- 💾 Update `{sidecarStatePath}` with workflow completion
- 📖 This is the final step — no next step to load

## CONTEXT BOUNDARIES:

- Available: step-02 completed — all cycle outcomes in sidecar-state and report
- Focus: synthesis, metrics, recommendations only
- Limits: do not modify code, do not start new refactoring cycles
- Dependencies: all targets processed (or explicitly paused)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Cycle Results

Read `{sidecarStatePath}` and `{refactoringReportPath}`.

Extract:
- `completedTargets` with status flags
- Count per status: merged, failed, rejected, obsolete, spawn-failed
- Total targets vs processed

### 2. Compute Metrics

**Calculate:**
- **Success rate:** merged / total processed (%)
- **Testability improvement:** synthesize from individual Refactorer summaries — what was untestable before, what is testable now
- **Complexity reduction:** aggregate from Refactorer summaries — functions extracted, nesting reduced, coupling removed
- **Coverage readiness:** estimate of code now ready for deep testing

**Update Metrics section in `{refactoringReportPath}`:**
- Targets Processed: {N}
- Merged: {N}
- Failed: {N}
- Rejected: {N}
- Obsolete: {N}
- Spawn Failed: {N}
- Testability Improvement: {qualitative assessment}
- Complexity Reduction: {qualitative assessment}

### 3. Generate Executive Summary

Write the Executive Summary section in `{refactoringReportPath}`:

"**Résumé exécutif :**

{N} cibles de refactoring traitées sur {total} identifiées.

**Résultats :**
- {merged} cibles refactorisées et intégrées avec succès
- {failed} cibles en échec technique (safety nets non passées après retry)
- {rejected} cibles rejetées par l'utilisateur
- {obsolete} cibles devenues obsolètes après un refactoring précédent
- {spawn-failed} cibles avec échec d'infrastructure

**Impact testabilité :**
{Synthesize the key testability gains — what categories of code are now testable that weren't before}

**Impact complexité :**
{Synthesize complexity reductions — patterns applied, coupling removed}"

### 4. Update Remaining Targets

If any targets were NOT processed (workflow paused before completion or targets added after initial selection):

- List them in the Remaining Targets section of `{refactoringReportPath}`
- Note why they remain (session limit, deferred, etc.)

If all targets were processed:
- Clear the Remaining Targets section: "Toutes les cibles priorisées ont été traitées."

### 5. Generate Recommendations

Read `{riskMapPath}` for context on remaining risk areas.

Write the Recommendations section in `{refactoringReportPath}`:

**Recommendations should cover:**

1. **Deep-testing priorities** — which refactored modules should get tests first (based on risk score + testability gain)
2. **Failed/rejected targets** — what to do about targets that couldn't be refactored (alternative approaches, manual refactoring, acceptance of risk)
3. **Additional refactoring** — any new targets discovered during the process that weren't in the original risk map
4. **Risk map update** — suggest whether the risk map should be recalculated post-refactoring

### 6. Finalize Report

Update `{refactoringReportPath}` frontmatter:
- Append `step-03-report` to `stepsCompleted`
- Set `lastStep` to `step-03-report`
- Set `status` to `complete`
- Set `nextWorkflow` to `deep-testing`

### 7. Update Sidecar State

Update `{sidecarStatePath}`:
- Clear `currentCycle`
- Set refactoring phase status to `complete`
- Set `consolidation.last_session` to current date
- Note: do NOT advance `consolidation.current_phase` — that's the lead agent's responsibility at workflow orchestration level

### 8. Present Final Summary

"**Rapport de refactoring finalisé.**

**Résultats globaux :**
| Métrique | Valeur |
|----------|--------|
| Cibles traitées | {N}/{total} |
| Succès (merged) | {N} ({%}) |
| Échecs | {N} |
| Rejetées | {N} |
| Obsolètes | {N} |

**Testabilité :** {one-line summary of improvement}

**Rapport sauvegardé :** `{refactoringReportPath}`

---

**Prochaine étape recommandée :** Workflow **deep-testing** (Phase 4) — construire une couverture de tests complète sur le code refactorisé.

**Merci pour votre collaboration sur ce cycle de refactoring !**"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All cycle results loaded from sidecar-state and report
- Metrics computed accurately from actual outcomes
- Executive summary synthesizes testability and complexity impact
- Remaining targets documented (if any)
- Recommendations are actionable and tied to risk data
- Report frontmatter updated with complete status
- Sidecar-state updated with phase completion
- Next workflow (deep-testing) recommended
- Clear final summary presented to user

### ❌ SYSTEM FAILURE:

- Inaccurate metrics (not matching actual cycle outcomes)
- Missing executive summary
- Not documenting remaining targets
- Generic recommendations not tied to actual results
- Not updating report frontmatter to complete
- Not updating sidecar-state
- Not recommending next workflow

**Master Rule:** The report is the deliverable. It must accurately reflect what happened, what improved, and what comes next. No fluff, no padding — honest synthesis.
