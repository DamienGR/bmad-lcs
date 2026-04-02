---
name: 'step-05-report'
description: 'Finalize impact report, update sidecar state, present verdict, terminate guard cycle'

outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
---

# Step 5: Final Report

## STEP GOAL:

To finalize the impact report by reviewing all sections for completeness, adding recommendations based on the verdict, updating the sidecar state with the guard cycle result, and presenting the final report to the developer. This is the last step — the guard cycle ends here.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the report finalization
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — delivering the final guard report
- ✅ Be clear and actionable — the developer needs to know what to do next
- ✅ The report is the permanent record of this guard cycle

### Step-Specific Rules:

- 🎯 Focus ONLY on report finalization, sidecar update, and presentation
- 🚫 FORBIDDEN to re-run tests or re-analyze — just finalize what's there
- 💬 Present a concise executive summary, then the full report
- 📋 This is the FINAL step — no nextStepFile

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Finalize `{outputFile}` with all sections complete
- 📖 Update `stepsCompleted` and mark workflow complete
- 🚫 Do NOT re-run any analysis or tests

## CONTEXT BOUNDARIES:

- Available: complete impact report with all sections from steps 01-04
- Focus: polish, recommendations, sidecar update, presentation
- Limits: finalization only — no new analysis, no re-runs
- Dependencies: step-04 completed with verdict computed

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load and Review Complete Report

Read `{outputFile}` completely.

Verify all sections are filled:
- Change Scope: ✅ / ❌
- Impact Analysis (Direct Dependencies, Indirect Coupling, Impacted Zones, Test Coverage): ✅ / ❌
- Actual Changes (Git Diff, Scope Drift): ✅ / ❌
- Validation Results (Safety Net table, Verdict): ✅ / ❌

If any section is empty or incomplete, note it in the report as "Data not available — {reason}".

### 2. Generate Recommendations

Based on the verdict and findings, write actionable recommendations:

**If BLOCKED:**
- List each failing test on critical zones
- Recommend specific fixes or investigation areas
- Suggest re-running guard after fixes

**If WARNING:**
- List failing tests on non-critical zones
- Assess whether failures are related to the change or pre-existing
- Recommend review before proceeding to production

**If CLEAR:**
- Confirm changes are safe
- Note any UNCOVERED zones for future safety net deployment
- Suggest committing changes

Append recommendations to "Recommendations" section of `{outputFile}`.

### 3. Polish Report

Review the complete report for:
1. Consistency between sections
2. All tables properly formatted
3. No duplicate information
4. Clear, concise language
5. Proper ## Level 2 headers for all sections

Minor optimization only — maintain all data and findings.

### 4. Update Sidecar State

Update `{sidecarStatePath}`:
- Record guard cycle completion:
  - `guard.last_cycle_date`: current date
  - `guard.last_verdict`: {BLOCKED/WARNING/CLEAR}
  - `guard.last_scope`: {change scope summary}
  - `guard.cycles_completed`: increment by 1

### 5. Finalize Report Frontmatter

Update `{outputFile}` frontmatter:
- `stepsCompleted`: append `'step-05-report'`
- `lastStep`: `'step-05-report'`
- `status`: `'complete'`

### 6. Present Final Report

"**Guard Cycle Complete**

---

**Verdict: {BLOCKED / WARNING / CLEAR}**

**Executive Summary:**
- **Scope:** {brief change scope}
- **Zones impacted:** {N} ({critical} critical, {high} high)
- **Scope drift:** {yes/no — N files}
- **Tests executed:** {N} across {M} zones
- **Failures:** {N} ({on which zones})

**Recommendations:**
{brief recommendations}

---

Full report saved at: `{outputFile}`

**Guard cycle terminated.** To analyze another change, relaunch the guard workflow."

Display: **[X] Exit guard mode**

#### Menu Handling Logic:

- IF X: Workflow complete. No further steps.
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- This is the final step — no next step to load

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All report sections verified for completeness
- Actionable recommendations generated based on verdict
- Report polished for consistency and clarity
- Sidecar state updated with cycle results
- Report frontmatter marked complete
- Executive summary presented to developer
- Guard cycle terminated cleanly

### ❌ SYSTEM FAILURE:

- Not checking all sections for completeness
- Generic recommendations (not based on actual findings)
- Not updating sidecar state
- Not marking report as complete
- Attempting to loop or start a new cycle

**Master Rule:** Finalize, recommend, update state, present, and terminate. One cycle, one clean exit.
