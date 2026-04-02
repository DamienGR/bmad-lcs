---
name: 'step-06-report'
description: 'Consolidate, review for gaps, and finalize the impact analysis report'

outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
advancedElicitationTask: '{project-root}/_bmad/core/workflows/advanced-elicitation/workflow.xml'
partyModeWorkflow: '{project-root}/_bmad/core/workflows/party-mode/workflow.md'
---

# Step 6: Report Consolidation, Review & Finalization

## STEP GOAL:

To perform a consolidation review pass on the complete impact report, check if later steps revealed couplings missed in earlier steps, enrich the report if needed, add final recommendations, and deliver the completed impact analysis.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst delivering the final analysis
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ This is the deliverable — ensure it's complete, coherent, and actionable
- ✅ You bring expertise in technical communication and report quality

### Step-Specific Rules:

- 🎯 Focus on consolidation, gap detection, and finalization — no new analysis
- 🚫 FORBIDDEN to rerun any analysis steps — use existing findings only
- 💬 Consolidation review may ENRICH earlier sections if gaps are found
- 📋 Final report must stand on its own — a developer should understand the full picture

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update and finalize {outputFile}
- 📖 Mark workflow as complete in frontmatter
- 🚫 Do NOT start new analysis

## CONTEXT BOUNDARIES:

- Available: Complete report from steps 01-05 in {outputFile}
- Focus: consolidation, enrichment, final recommendations
- Limits: no new analysis — only cross-reference and enrich
- Dependencies: all previous steps must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Complete Report

Read {outputFile} completely. Review all sections:
- Scope
- Direct Dependencies
- Indirect Coupling (Code, Data, Framework)
- Related Tests
- Risk Assessment
- Prioritized Action Plan

### 2. Consolidation Review Pass

Cross-reference findings between steps to detect gaps:

**Check 1: Did risk assessment reveal missed dependencies?**
- Review Risk Assessment for zones scored HIGH/CRITICAL
- Verify each high-risk zone appears in Direct Dependencies or Indirect Coupling sections
- If a risk zone was identified in step 05 but not traced in steps 02-03, flag and enrich

**Check 2: Did test mapping reveal uncovered coupling?**
- Review coverage gaps from step 04
- Cross-reference with indirect coupling from step 03
- If a coverage gap exists on a surprising coupling point, escalate its prominence

**Check 3: Consistency between sections**
- Verify the Scope matches what was actually analyzed
- Ensure all direct dependencies are accounted for in the test mapping
- Check that the action plan addresses the highest-risk zones

**If gaps found:**
"**Consolidation Review found {count} enrichment(s):**
- {description of what was enriched and where}

These have been incorporated into the relevant sections."

**If no gaps:**
"**Consolidation Review: All sections are consistent. No enrichments needed.**"

### 3. Add Final Recommendations

Append to the **## Recommendations** section:

**Recommended Test Scope:**
- List specific test suites that should be run before and after the change
- Identify which tests to run first (highest value for catching regressions)
- Suggest new tests needed for uncovered zones (reference action plan)

**Change Strategy Recommendations:**
- Suggested order of modifications (least-risk first, or most-critical first)
- Rollback indicators — what to watch for that signals a regression
- Monitoring recommendations post-change

**Next Workflow Recommendations:**
- If changes are high-risk: "Consider running the **safety-nets** workflow before proceeding"
- If test gaps are significant: "Consider running the **deep-testing** workflow to establish baseline coverage"
- For any change: "After modification, re-run **impact-analysis** to verify no unexpected cascading effects"

### 4. Finalize Report

**Polish the report for readability:**
- Ensure all sections have consistent formatting
- Verify ## Level 2 headers are used for main sections
- Check that surprising impacts are prominently marked with ⚠️ throughout
- Ensure the action plan checklist is properly formatted
- Add a brief executive summary at the top (after frontmatter, before Scope):

```markdown
## Executive Summary

**Change:** {brief description}
**Overall Risk:** {CRITICAL/HIGH/MEDIUM/LOW}
**Impacted zones:** {total count}
**Surprising impacts:** {count}
**Action items before modifying:** {count}
**Test coverage of impacted zones:** {percentage}
```

**Update frontmatter to mark complete:**
```yaml
stepsCompleted: ['step-01-init', 'step-02-direct-deps', 'step-03-indirect-coupling', 'step-04-related-tests', 'step-05-risk-assessment', 'step-06-report']
lastStep: 'step-06-report'
status: 'complete'
completedDate: '{date}'
nextWorkflow: 'safety-nets'
nextWorkflowRecommended: true
```

### 5. Present Final Report Summary

"**Impact Analysis Complete.**

**Report saved to:** {outputFile}

**Summary:**
- Overall Risk: {level}
- {X} impacted zones identified
- {Y} surprising impacts flagged
- {Z} actions to take before modifying
- {N}% of impacted zones have existing test coverage

**Recommended next steps:**
1. Complete the {count} actions in the Prioritized Action Plan
2. Run the listed test suites to establish a baseline
3. Proceed with the modification
4. Re-run impact analysis after modification to verify no cascading effects

**Would you like to review or adjust anything in the report?**"

### 6. Present MENU OPTIONS

Display: "**Select an Option:** [A] Advanced Elicitation [P] Party Mode [C] Finalize and Complete"

#### Menu Handling Logic:

- IF A: Execute {advancedElicitationTask}, and when finished redisplay the menu
- IF P: Execute {partyModeWorkflow}, and when finished redisplay the menu
- IF C: Save finalized report to {outputFile}, mark workflow complete
- IF Any other comments or queries: help user respond then [Redisplay Menu Options](#6-present-menu-options)

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY finalize when user selects 'C'
- After other menu items execution, return to this menu
- User can chat or ask questions — always respond and then end with display again of the menu options

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN the user selects 'C' and the report is finalized with all sections complete, executive summary added, and frontmatter marked as complete, is this workflow finished. There is no next step — this is the final step.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Consolidation review pass completed — gaps detected and enriched
- Final recommendations added (test scope, change strategy, next workflows)
- Executive summary added at top of report
- Report polished for readability and consistency
- Frontmatter marked as complete
- User approved the final report
- Workflow completed successfully

### ❌ SYSTEM FAILURE:

- Skipping the consolidation review pass
- Adding new analysis instead of cross-referencing existing
- Not adding executive summary
- Not recommending next workflows
- Not marking the workflow as complete in frontmatter
- Finalizing without user approval

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
