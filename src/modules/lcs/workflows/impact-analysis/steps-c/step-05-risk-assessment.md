---
name: 'step-05-risk-assessment'
description: 'Score impact severity per zone and generate a prioritized action plan'

nextStepFile: './step-06-report.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
advancedElicitationTask: '{project-root}/_bmad/core/workflows/advanced-elicitation/workflow.xml'
partyModeWorkflow: '{project-root}/_bmad/core/workflows/party-mode/workflow.md'
---

# Step 5: Risk Assessment & Prioritized Action Plan

## STEP GOAL:

To score impact severity per zone based on all findings from steps 02-04, highlight "surprising" impacts prominently, and generate a prioritized checklist of the top 3-5 actions the developer must take BEFORE modifying code.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst synthesizing findings into actionable risk assessment
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ This step transitions from analysis to recommendations — intent-based style
- ✅ You bring expertise in risk assessment and change management
- ✅ The developer brings their judgment on acceptable risk levels

### Step-Specific Rules:

- 🎯 Synthesize ALL findings from steps 02-04 into a coherent risk picture
- 🚫 FORBIDDEN to add new analysis — use only what was found in previous steps
- 💬 Present risk assessment clearly, then WAIT for user review before finalizing
- 📋 The action plan must be concrete: specific files, specific tests, specific checks

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append risk assessment and action plan to {outputFile}
- 📖 Update output frontmatter stepsCompleted when this step is done
- 🚫 Do NOT proceed without user reviewing the assessment

## CONTEXT BOUNDARIES:

- Available: All findings from steps 01-04 in {outputFile}
- Focus: risk scoring and actionable recommendations
- Limits: no new analysis — synthesize only
- Dependencies: steps 01-04 must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load All Findings

Read {outputFile} completely to collect:
- Scope (step 01)
- Direct dependencies (step 02)
- Indirect coupling — code, data, framework (step 03)
- Related tests and coverage gaps (step 04)

### 2. Score Impact Severity

For each impacted zone, calculate a risk score based on:

**Severity factors:**
| Factor | Weight | Scoring |
|---|---|---|
| Coupling depth | HIGH | Direct = 1, Indirect-1 = 2, Indirect-2+ = 3 |
| Coverage gap | HIGH | Well covered = 0, Partial = 1, None = 3 |
| Surprising impact | MEDIUM | Expected = 0, Surprising = 2 |
| Data coupling | HIGH | None = 0, Read-only = 1, Write = 3 |
| Framework coupling | MEDIUM | None = 0, Hook = 1, Filter chain = 2 |
| Cross-dimension | HIGH | Single dimension = 0, Multiple = 2 |

**Risk levels:**
- **CRITICAL (7+):** Must be addressed before any modification
- **HIGH (4-6):** Should be addressed, significant regression risk
- **MEDIUM (2-3):** Monitor, may need attention
- **LOW (0-1):** Minimal risk

### 3. Present Risk Summary

"**Risk Assessment Summary:**

| Risk Level | Zones | Key Concerns |
|---|---|---|
| 🔴 CRITICAL | {count} | {brief} |
| 🟠 HIGH | {count} | {brief} |
| 🟡 MEDIUM | {count} | {brief} |
| 🟢 LOW | {count} | {brief} |

**⚠️ Surprising Impacts (most important):**
{List each surprising impact with its risk score and why it's surprising}

**Highest risk zones:**
1. {zone} — Risk: {score} — {reason}
2. {zone} — Risk: {score} — {reason}
3. {zone} — Risk: {score} — {reason}"

### 4. Generate Prioritized Action Plan

Based on risk assessment, generate top 3-5 actions:

"**Prioritized Action Plan — Do These BEFORE Modifying Code:**

- [ ] **Action 1:** {specific action} — Addresses: {risk zone} — Priority: CRITICAL
  *Example: Write unit test for authenticate() covering OAuth2 path before modifying*

- [ ] **Action 2:** {specific action} — Addresses: {risk zone} — Priority: HIGH
  *Example: Verify DB trigger after_user_update won't fire during auth changes*

- [ ] **Action 3:** {specific action} — Addresses: {risk zone} — Priority: HIGH
  *Example: Run existing integration tests for login flow to establish baseline*

- [ ] **Action 4:** {specific action} — Addresses: {risk zone} — Priority: MEDIUM

- [ ] **Action 5:** {specific action} — Addresses: {risk zone} — Priority: MEDIUM"

**Action plan rules:**
- Each action must reference a specific file, function, or test
- Each action must address a specific risk zone
- Actions ordered by risk priority (CRITICAL first)
- Maximum 5 actions to keep it focused and actionable

### 5. Present for User Review

"**Please review the risk assessment and action plan above.**

Questions to consider:
- Does the risk scoring reflect your understanding of the codebase?
- Are there any zones I've scored too high or too low?
- Is the action plan practical for your timeline?
- Any actions you'd add or remove?"

### 6. Append to Report

Append to {outputFile}:

**## Risk Assessment** section with:
- Severity scoring table per zone
- Risk level summary
- Surprising impacts highlighted

**## Prioritized Action Plan** section with:
- Numbered checklist of 3-5 actions
- Each with priority, target, and rationale

Update frontmatter:
```yaml
stepsCompleted: ['step-01-init', 'step-02-direct-deps', 'step-03-indirect-coupling', 'step-04-related-tests', 'step-05-risk-assessment']
lastStep: 'step-05-risk-assessment'
```

### 7. Present MENU OPTIONS

Display: "**Select an Option:** [A] Advanced Elicitation [P] Party Mode [C] Continue to Report Finalization"

#### Menu Handling Logic:

- IF A: Execute {advancedElicitationTask}, and when finished redisplay the menu
- IF P: Execute {partyModeWorkflow}, and when finished redisplay the menu
- IF C: Save risk assessment and action plan to {outputFile}, update frontmatter, then load, read entire file, then execute {nextStepFile}
- IF Any other comments or queries: help user respond then [Redisplay Menu Options](#7-present-menu-options)

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'
- After other menu items execution, return to this menu
- User can chat or ask questions — always respond and then end with display again of the menu options

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN the user has reviewed the risk assessment and action plan and selects 'C', will you then load and read fully `{nextStepFile}` to execute the Report Consolidation and Finalization.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All impacted zones scored for severity
- Surprising impacts highlighted prominently
- Prioritized action plan with 3-5 specific, concrete actions
- User reviewed and approved the assessment
- Risk Assessment and Action Plan sections populated in report
- Frontmatter updated with stepsCompleted

### ❌ SYSTEM FAILURE:

- Adding new analysis instead of synthesizing existing findings
- Vague actions ("review the code" instead of "write test for X covering Y")
- More than 5 actions (not focused enough)
- Proceeding without user review
- Not highlighting surprising impacts

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
