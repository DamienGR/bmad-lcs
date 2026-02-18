---
name: 'step-01b-continue'
description: 'Resume interrupted guard cycle by reading impact report state and routing to the correct step'

guardOutputPath: '{output_folder}/lcs/guard'
step02File: './step-02-analyze.md'
step03File: './step-03-monitor.md'
step04File: './step-04-validate.md'
step05File: './step-05-report.md'
---

# Step 1b: Resume Guard Cycle

## STEP GOAL:

To resume an interrupted guard cycle by reading the impact report's stepsCompleted, presenting the current state, and routing to the correct next step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the status assessment
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — resuming a guard cycle
- ✅ Be clear about what was completed and what comes next
- ✅ The developer needs to understand the state quickly — be concise

### Step-Specific Rules:

- 🎯 Focus ONLY on status assessment and routing
- 🚫 FORBIDDEN to start any analysis, validation, or reporting
- 💬 Present a clear status summary before routing
- 📋 The primary continuation scenario is: step-02 completed → route to step-03 (dev finished coding)

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 No state changes — just read and route
- 📖 Find the most recent impact report in guardOutputPath

## CONTEXT BOUNDARIES:

- Previous guard session was interrupted
- Impact report exists with stepsCompleted array
- Need to determine exactly where to resume
- Most common scenario: dev left after step-02 (went to code), now returns for step-03

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Find and Read Impact Report

Search `{guardOutputPath}` for the most recent `impact-report-*.md` file.

Read its frontmatter:
- `stepsCompleted`: array of completed steps
- `lastStep`: last completed step name
- `changeScope`: declared change scope
- `verdict`: (empty if not yet computed)

### 2. Welcome Back & Present Status

"**Welcome back! Resuming guard cycle.**

**Change scope:** {changeScope}
**Progress:**

| Step | Status |
|------|--------|
| 1. Init & Activate | {✅ if in stepsCompleted, ⬜ otherwise} |
| 2. Impact Analysis | {✅ / ⬜} |
| 3. Change Monitoring | {✅ / ⬜} |
| 4. Validation | {✅ / ⬜} |
| 5. Report | {✅ / ⬜} |

**Last completed:** {lastStep}"

### 3. Route to Correct Step

**Routing based on last completed step:**

**If lastStep = 'step-01-init-activate':**
- "Impact analysis not yet done. Resuming analysis."
- Load, read entirely, then execute `{step02File}`

**If lastStep = 'step-02-analyze':**
- "Impact analysis complete. Have you finished making your changes?"
- Wait for user confirmation
- If yes → load, read entirely, then execute `{step03File}`
- If not yet → "Take your time. Come back when your changes are done."

**If lastStep = 'step-03-monitor':**
- "Changes tracked. Resuming validation."
- Load, read entirely, then execute `{step04File}`

**If lastStep = 'step-04-validate':**
- "Validation complete. Resuming final report."
- Load, read entirely, then execute `{step05File}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Most recent impact report found and read
- Clear status presented to developer
- Correct routing to the appropriate next step
- For step-02 → step-03 transition: confirmed developer has finished coding

### ❌ SYSTEM FAILURE:

- Not finding the impact report
- Routing to the wrong step
- Skipping the status presentation
- Not confirming with developer before step-03 (they may not have finished coding)

**Master Rule:** Read the state, present it clearly, route correctly. The most common scenario is step-02 → step-03 (dev finished coding and returns). Handle this path smoothly.
