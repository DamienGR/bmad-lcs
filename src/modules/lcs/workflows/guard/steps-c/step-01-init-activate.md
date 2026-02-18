---
name: 'step-01-init-activate'
description: 'Verify guard prerequisites, load risk map and sidecar state, gather change scope, create impact report'

nextStepFile: './step-02-analyze.md'
continueFile: './step-01b-continue.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
riskMapPath: '{output_folder}/lcs/risk-map.md'
guardOutputPath: '{output_folder}/lcs/guard'
outputFile: '{guardOutputPath}/impact-report-{date}.md'
templateFile: '../templates/impact-report-template.md'
---

# Step 1: Initialize & Activate Guard

## STEP GOAL:

To verify that all consolidation phases are complete, load the risk map and sidecar state, gather the developer's planned change scope, and create the impact report document from the template.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are activating Guard mode — the developer's daily protection companion
- ✅ The developer brings their project context and change intentions
- ✅ Be fast and non-intrusive — this is a daily-use tool, not a ceremony

### Step-Specific Rules:

- 🎯 Focus ONLY on prerequisites, input discovery, and scope gathering
- 🚫 FORBIDDEN to start any impact analysis — that's step 02
- 💬 Be concise — the developer wants to get to coding, not fill forms
- 📋 If a guard cycle is already in progress → route to step-01b (continuation)

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Create impact report from template with scope in frontmatter
- 📖 Track progress via impact report stepsCompleted
- 🚫 Do NOT analyze dependencies or run tests in this step

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: prerequisites + scope gathering only
- Limits: do not analyze, do not run tests
- Dependencies: all prior LCS phases must be complete (pre-flight, audit, risk-map, safety-nets)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Check for Continuation

Check if an impact report already exists at `{guardOutputPath}/impact-report-*.md` with `stepsCompleted` containing entries.

- If an in-progress report exists → load, read entirely, then execute `{continueFile}`
- If no in-progress report → this is a fresh guard cycle, continue below

### 2. Verify Prerequisites

**Check sidecar state:**
- Read `{sidecarStatePath}`
- Verify consolidation phases completed: pre-flight, audit, risk-map, safety-nets
- If any phase is missing or incomplete → ERROR with clear message about which phase to complete first

**Check risk map exists:**
- Verify `{riskMapPath}` exists and has frontmatter `status: complete`
- If missing → ERROR: "Risk map is missing. Complete the audit workflow first."

"**Guard Mode — Prerequisites:**
- Consolidation phases: ✅ Complete
- Risk map: ✅ Available
- Safety nets: ✅ Deployed"

### 3. Load Risk Map Summary

Launch a subprocess that loads `{riskMapPath}`, extracts zone names with risk scores, and returns a summary table.

If subprocess unavailable: load `{riskMapPath}` in main thread and extract the data.

Store the risk zones in memory for use in step-02.

"**Risk zones loaded:** {N} zones — {critical} critical, {high} high, {medium} medium, {low} low"

### 4. Gather Change Scope

"**What are you planning to change?**

Describe briefly:
- Which files or folders?
- Which functions or features?
- What kind of change? (bug fix, new feature, refactoring, dependency update)

Be as specific as you can — the more precise, the faster the analysis."

**Wait for user response.** Store the declared scope.

### 5. Create Impact Report

Create `{outputFile}` from `{templateFile}`:
- Set frontmatter: `date`, `user_name`, `project_name`, `changeScope` (from user input)
- Set `stepsCompleted: ['step-01-init-activate']`
- Set `lastStep: 'step-01-init-activate'`
- Fill the "Change Scope > Declared Scope" section with the user's input

### 6. Proceed to Analysis

"**Guard mode activated.** Scope recorded. Proceeding to impact analysis..."

Display: **[C] Continue to impact analysis**

#### Menu Handling Logic:

- IF C: Ensure `{outputFile}` is saved with scope and frontmatter, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All consolidation prerequisites verified (pre-flight, audit, risk-map, safety-nets)
- Risk map loaded and zones extracted
- Change scope gathered from developer
- Impact report created from template with scope recorded
- stepsCompleted updated in report frontmatter
- Proceeding to step 02

### ❌ SYSTEM FAILURE:

- Skipping prerequisite checks
- Not loading the risk map
- Not gathering change scope from the developer
- Starting impact analysis in this step
- Not creating the impact report before proceeding

**Master Rule:** Guard activation must be fast and focused — verify prerequisites, get the scope, create the report, and move on. No ceremony.
