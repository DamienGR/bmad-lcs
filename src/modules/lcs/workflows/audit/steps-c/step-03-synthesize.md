---
name: 'step-03-synthesize'
description: 'Verify all 3 input artifacts are ready, then synthesize into unified audit report'

nextStepFile: './step-04-batched-qa.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
codeAnalysisPath: '{output_folder}/lcs/audit/code-analysis.md'
siteExplorationPath: '{output_folder}/lcs/audit/site-exploration.md'
depScanPath: '{output_folder}/lcs/audit/dependency-scan.md'
auditReportPath: '{output_folder}/lcs/audit/audit-report.md'
advancedElicitationTask: '{project-root}/_bmad/core/workflows/advanced-elicitation/workflow.xml'
partyModeWorkflow: '{project-root}/_bmad/core/workflows/party-mode/workflow.md'
---

# Step 3: Synthesize Audit Report

## STEP GOAL:

To verify all 3 input artifacts are complete, then synthesize them into a unified audit report that presents the full picture of the legacy project to the developer.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER synthesize with incomplete inputs
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — synthesizing your team's findings
- ✅ Your value here is CROSS-CUTTING INSIGHTS — things no single teammate could see
- ✅ The developer needs a clear, actionable picture of their legacy project
- ✅ Be thorough but concise — respect the developer's time

### Step-Specific Rules:

- 🎯 Focus on synthesis — connecting findings across the 3 artifacts
- 🚫 FORBIDDEN to re-do analysis that teammates already completed
- 💬 Highlight cross-cutting concerns and risk indicators
- 📋 Use subprocess Pattern 2 (per-artifact deep analysis) if available
- ⚙️ If subprocess unavailable, read and analyze each artifact sequentially in main thread

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write audit-report.md artifact
- 📖 Update sidecar-state: audit-synthesis → done
- 🚫 Do NOT extract questions here — that's step 04

## CONTEXT BOUNDARIES:

- Available: 3 completed artifacts (code-analysis, site-exploration, dependency-scan)
- Focus: cross-cutting synthesis, not re-analysis
- Limits: do not ask the user questions about business logic — that's step 04
- Dependencies: all 3 input artifacts must be status: complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Verify Input Artifacts

Check each artifact exists and has `status: complete` in frontmatter:

- `{codeAnalysisPath}` — Code Analyst findings
- `{siteExplorationPath}` — Site Explorer findings
- `{depScanPath}` — Dependency scan results

**If any artifact missing or status ≠ complete:**
- Report which artifact is missing/incomplete
- Offer: "Souhaitez-vous attendre, ou continuer plus tard ?"
- Do NOT proceed with synthesis

### 2. Read and Analyze Artifacts

**Using subprocess Pattern 2 (if available):**
DO NOT BE LAZY — For EACH artifact, launch a subprocess that:
1. Loads the artifact completely
2. Extracts key findings, metrics, and risk indicators
3. Returns structured findings to parent

**If subprocess unavailable:**
Read each artifact sequentially and extract key findings.

**Extract from code-analysis.md:**
- Project structure overview
- Complexity hotspots (highest cyclomatic complexity)
- Framework patterns identified
- Business logic locations
- Security findings (if Semgrep was used)
- DB architecture (if MySQL MCP was used)

**Extract from site-exploration.md:**
- Pages mapped (count and structure)
- Key user journeys documented
- Interactive elements inventory
- Performance baselines (if Lighthouse was used)
- Functional specifications inferred

**Extract from dependency-scan.md:**
- Vulnerability summary by severity
- Outdated packages with major gaps
- Priority updates recommended

### 3. Synthesize Cross-Cutting Insights

Identify connections that span multiple artifacts:

- **Code ↔ Site:** Which code files power which pages? Where does business logic map to user journeys?
- **Code ↔ Dependencies:** Which vulnerable dependencies are used in critical code paths?
- **Site ↔ Dependencies:** Which outdated packages affect user-facing functionality?
- **Risk indicators:** Areas with HIGH complexity + HIGH business criticality + LOW test coverage (if available) = highest risk zones
- **Framework-specific concerns:** Patterns specific to WordPress/Drupal that cross code and site boundaries

### 4. Write Audit Report

Write `{auditReportPath}` with standard frontmatter:

```yaml
---
phase: audit
task_id: audit-synthesis
produced_by: conrad-lead
date: {current_date}
status: complete
---
```

**Content structure:**

```markdown
# Audit Report — {project_name}

## Executive Summary
[2-3 paragraphs: project overview, key findings, overall risk assessment]

## Project Architecture
### Code Structure
[From code-analysis: directory layout, framework usage, key files]

### Site Structure
[From site-exploration: page hierarchy, navigation, key journeys]

### Data Architecture
[From code-analysis: DB schema insights, data flow]

## Key Findings

### Code Quality
[Complexity hotspots, code patterns, maintainability concerns]

### Business Logic
[Critical functions, application workflows, integration points]

### Security
[Vulnerabilities from code analysis + dependency scan]

### Performance
[Baselines from site exploration, bottleneck indicators]

## Dependencies
[Vulnerability summary, outdated packages, priority updates]

## Cross-Cutting Risk Indicators
[Zones with multiple risk factors overlapping — feed into Risk Map]

| Risk Zone | Complexity | Business Impact | Vulnerabilities | Priority |
|-----------|-----------|-----------------|-----------------|----------|
| [zone 1]  | [score]   | [high/med/low]  | [count]         | [1-5]    |
| ...       | ...       | ...             | ...             | ...      |

## Recommendations
[Prioritized action items for Safety Nets phase]
```

### 5. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `tasks.audit.audit-synthesis.status` to `done`

### 6. Present Report Summary

Present the executive summary and risk indicators table to the user:

"**Rapport d'audit terminé.**

[Executive summary — 2-3 key takeaways]

**Zones de risque identifiées : {count}**

[Risk indicators table]

Rapport complet dans `audit/audit-report.md`."

### 7. Present MENU OPTIONS

Display: **Sélectionnez :** [A] Advanced Elicitation [P] Party Mode [C] Continue

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'
- After other menu items execution, return to this menu

#### Menu Handling Logic:

- IF A: Execute {advancedElicitationTask}, and when finished redisplay the menu
- IF P: Execute {partyModeWorkflow}, and when finished redisplay the menu
- IF C: Update frontmatter, then load, read entire file, then execute {nextStepFile}
- IF Any other: help user respond, then [Redisplay Menu Options](#7-present-menu-options)

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All 3 input artifacts verified complete before synthesis
- Cross-cutting insights identified (not just concatenation)
- Audit report written with proper frontmatter and structure
- Risk indicators table populated
- Sidecar-state updated (audit-synthesis: done)
- Executive summary presented to user

### ❌ SYSTEM FAILURE:

- Synthesizing with incomplete inputs
- Simply concatenating artifacts without cross-cutting analysis
- Missing risk indicators table
- Not verifying artifact frontmatter status
- Extracting questions here (that's step 04)

**Master Rule:** Synthesis adds value BEYOND what individual artifacts contain. Cross-cutting insights are the primary deliverable.
