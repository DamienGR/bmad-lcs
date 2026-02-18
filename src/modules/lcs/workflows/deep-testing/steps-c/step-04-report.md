---
name: 'step-04-report'
description: 'Finalize the deep-testing report with executive summary, global metrics, confidence assessment, and mark LCS pipeline Phase 0-4 as complete'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
deepTestingReportPath: '{output_folder}/lcs/deep-testing-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets-report.md'
refactoringReportPath: '{output_folder}/lcs/refactoring-report.md'
---

# Step 4: Final Report

## STEP GOAL:

To finalize the deep-testing report by synthesizing all zone outcomes into an executive summary, computing global metrics, assessing overall confidence, producing recommendations, and marking the deep-testing phase (and the LCS pipeline Phases 0-4) as complete.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — delivering the final assessment
- ✅ Be factual, concise, and actionable — the developer needs a clear picture of test coverage
- ✅ Highlight wins AND remaining gaps honestly
- ✅ This is the culmination of the entire LCS pipeline — present it with appropriate gravity

### Step-Specific Rules:

- 🎯 Focus ONLY on report finalization and workflow completion
- 🚫 FORBIDDEN to write tests, spawn teammates, or modify code
- 💬 Present the report summary to the user before finalizing
- 📋 This is the FINAL step — mark workflow AND LCS pipeline as complete

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Finalize `{deepTestingReportPath}` with all sections
- 💾 Update `{sidecarStatePath}` with workflow and pipeline completion
- 📖 This is the final step — no next step to load

## CONTEXT BOUNDARIES:

- Available: step-02 completed (all zones processed), step-03 completed (discoveries integrated)
- Focus: synthesis, metrics, recommendations only
- Limits: do not modify code, do not run tests, do not spawn teammates
- Dependencies: all zones processed AND discovery integration done

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Zone Results

Read `{sidecarStatePath}` and `{deepTestingReportPath}`.

Extract from sidecar-state:
- `completedZones` with status flags and test counts
- Count per status: approved, skipped, spawn-failed
- Total zones vs processed
- Discovery integration results

Extract from report:
- Per-zone results already written during step-02
- Discovery findings summary from step-03

### 2. Compute Global Metrics

**Calculate:**
- **Total tests written:** sum across all approved zones (unit + integration + edge)
- **Tests by type:** total unit, total integration, total edge cases
- **Zone completion rate:** approved / total zones (%)
- **Discovery count:** bugs + untestable code + surprising behaviors + fixture gaps
- **Known issues:** count of tests marked as known_issue (existing bugs exposed)

**Update Global Metrics section in `{deepTestingReportPath}`:**

| Métrique | Valeur |
|----------|--------|
| Zones traitées | {approved + skipped}/{total} |
| Zones approuvées | {approved} |
| Zones skippées | {skipped} |
| Tests écrits (total) | {total_tests} |
| — Unit tests | {unit_count} |
| — Integration tests | {integration_count} |
| — Edge case tests | {edge_count} |
| Known issues exposés | {known_count} |
| Découvertes intégrées | {discovery_count} |

### 3. Generate Executive Summary

Write the Executive Summary section in `{deepTestingReportPath}`:

"{N} zones de risque traitées par le Deep Tester.

**Résultats :**
- {approved} zones approuvées avec {total_tests} tests écrits
- {skipped} zones skippées {brief reason if applicable}
- {known_count} bugs existants découverts par les tests (marqués known_issue)

**Couverture par type :**
- **Unit :** {unit_count} tests — {qualitative: logique métier, transformations, validateurs}
- **Integration :** {integration_count} tests — {qualitative: interactions composants, flux API}
- **Edge cases :** {edge_count} tests — {qualitative: valeurs limites, chemins d'erreur}

**Découvertes :**
{count} trouvailles intégrées au risk map — {brief categorization}"

### 4. Assess Confidence

For each zone, determine confidence level based on:
- Test count and depth (unit + integration + edge = high, unit + integration = medium, unit only = low)
- Known issues (many known issues = lower confidence in code quality)
- Untestable code (untestable areas = gap in confidence)

**Write Confidence Assessment section in `{deepTestingReportPath}`:**

"**Évaluation de la confiance :**

| Zone | Profondeur | Tests | Known Issues | Confiance |
|------|-----------|-------|--------------|-----------|
| {zone} | {depth} | {count} | {known} | {🟢 Haute / 🟡 Moyenne / 🔴 Basse} |

**Confiance globale : {🟢/🟡/🔴} — {one-line justification}**"

### 5. Document Remaining Gaps

**Identify areas NOT covered by deep testing:**
- Zones that were skipped or spawn-failed
- Code identified as untestable despite refactoring
- Areas where only partial depth was achieved (unit only, no integration)
- Known issues that remain unresolved

**Write Remaining Gaps section in `{deepTestingReportPath}`:**

"**Lacunes restantes :**
- {list of gaps with zone, reason, and recommended action}

**Zones non-testées :** {list of skipped zones with reason}"

### 6. Generate Recommendations

Read `{riskMapPath}`, `{safetyNetsReportPath}`, and `{refactoringReportPath}` for cross-phase context.

**Write Recommendations section in `{deepTestingReportPath}`:**

Recommendations should cover:

1. **Remaining coverage gaps** — what still needs attention (manual testing, targeted follow-up)
2. **Known issues** — which exposed bugs should be prioritized for fixing (based on risk score)
3. **Untestable code** — whether additional refactoring is warranted or risk is acceptable
4. **Test maintenance** — strategy for keeping the new test suite healthy over time
5. **Guard workflow** — mention the LCS Guard workflow is available for ongoing maintenance and regression monitoring

### 7. Finalize Report

Update `{deepTestingReportPath}` frontmatter:
- Append `step-04-report` to `stepsCompleted`
- Set `lastStep` to `step-04-report`
- Set `status` to `complete`
- Set `completedDate` to current date

### 8. Update Sidecar State

Update `{sidecarStatePath}`:
- Set deep-testing phase status to `complete`
- Set `consolidation.last_session` to current date
- Note: do NOT advance `consolidation.current_phase` — that's the lead agent's responsibility at workflow orchestration level

### 9. Present Final Summary

"**Rapport de deep testing finalisé.**

**Résultats globaux :**
| Métrique | Valeur |
|----------|--------|
| Zones traitées | {N}/{total} |
| Tests écrits | {total_tests} |
| Known issues exposés | {known_count} |
| Découvertes intégrées | {discovery_count} |
| Confiance globale | {🟢/🟡/🔴} |

**Rapport sauvegardé :** `{deepTestingReportPath}`

---

**Le pipeline LCS (Phases 0 à 4) est maintenant complet.**

| Phase | Workflow | Statut |
|-------|----------|--------|
| Phase 0 | Pre-flight | ✅ Complété |
| Phase 1 | Audit | ✅ Complété |
| Phase 2 | Safety Nets | ✅ Complété |
| Phase 2b | Dependency Update | ✅ Complété |
| Phase 3 | Refactoring | ✅ Complété |
| Phase 4 | Deep Testing | ✅ Complété |

**Votre codebase legacy est désormais consolidée** — auditée, protégée par des filets de sécurité, refactorisée, et couverte par des tests approfondis.

**Pour la maintenance continue :** le workflow **Guard** est disponible pour le monitoring des régressions et la protection continue de votre codebase.

**Merci pour votre collaboration tout au long de ce processus de consolidation !**"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All zone results loaded from sidecar-state and report
- Global metrics computed accurately from actual zone outcomes
- Executive summary synthesizes coverage achieved and discoveries
- Confidence assessment per zone with overall assessment
- Remaining gaps documented honestly
- Recommendations are actionable and cross-reference prior phases
- Report frontmatter updated with complete status
- Sidecar-state updated with phase completion
- LCS pipeline completion summary presented
- Guard workflow mentioned for ongoing maintenance
- Clear final summary presented to user

### ❌ SYSTEM FAILURE:

- Inaccurate metrics (not matching actual zone outcomes)
- Missing executive summary or confidence assessment
- Not documenting remaining gaps
- Generic recommendations not tied to actual results
- Not updating report frontmatter to complete
- Not updating sidecar-state
- Not presenting the LCS pipeline completion summary
- Writing tests or modifying code in this step

**Master Rule:** The report is the deliverable. It must accurately reflect what was tested, what was discovered, and what confidence level the codebase has reached. No fluff, no padding — honest synthesis. The LCS pipeline completion is a significant milestone — present it clearly.
