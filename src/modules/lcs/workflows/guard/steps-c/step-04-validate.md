---
name: 'step-04-validate'
description: 'Select and execute relevant safety nets on impacted zones, collect results, compute verdict'

nextStepFile: './step-05-report.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
riskMapPath: '{output_folder}/lcs/risk-map.md'
selectionProtocol: '../data/safety-net-selection-protocol.md'
---

# Step 4: Post-Change Validation

## STEP GOAL:

To select relevant safety nets for impacted zones based on risk scores, execute them in the correct order (lint → smoke → e2e → visual), collect results, and compute the overall verdict (BLOCKED / WARNING / CLEAR).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the selection protocol
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — executing validation with precision
- ✅ Be systematic — follow the selection protocol exactly
- ✅ Report results clearly — the developer needs to know pass/fail immediately

### Step-Specific Rules:

- 🎯 Focus ONLY on selecting safety nets, executing them, and collecting results
- 🚫 FORBIDDEN to modify code or attempt fixes — just report
- 💬 Present results progressively as each safety net completes
- 📋 Load and follow `{selectionProtocol}` for safety net selection and prioritization
- 🎯 Use subprocess optimization (Pattern 4) for parallel execution of independent safety nets by zone
- ⚙️ If subprocess/parallel unavailable, execute sequentially

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append all results to the "Validation Results" section of `{outputFile}`
- 📖 Update `stepsCompleted` in output frontmatter when complete
- 🚫 Do NOT modify code, suggest fixes, or refactor anything

## CONTEXT BOUNDARIES:

- Available: impact report with impacted zones and risk scores, sidecar state with safety nets registry
- Focus: safety net selection, execution, result collection, verdict
- Limits: validation only — no code changes, no fix suggestions
- Dependencies: step-03 completed with actual changes recorded

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Selection Protocol

Load and read `{selectionProtocol}` completely.

Load `{outputFile}` to get the impacted zones table (from step-02 and step-03).

Load `{sidecarStatePath}` to get the safety nets registry (deployed tests per zone).

Load `{riskMapPath}` for risk scores.

### 2. Select Safety Nets

Apply the selection protocol:

For each impacted zone:
1. Look up risk score from risk map
2. Find available safety nets from sidecar state
3. Apply risk-based selection:
   - **CRITICAL (>= 8):** lint + smoke + e2e + visual
   - **HIGH (5-7):** lint + smoke + e2e
   - **MEDIUM (3-4):** lint + smoke
   - **LOW (1-2):** skip (mention in report)

"**Safety nets selected:**

| Zone | Risk Score | Level | Tests Selected |
|------|-----------|-------|---------------|
{table of zones with their selected tests}

**Total:** {N} tests to execute across {M} zones
**Skipped:** {K} low-risk zones (mentioned in report)"

### 3. Execute Safety Nets

Follow execution order from the protocol: **lint → smoke → e2e → visual**

**Lint (sequential — fast, may block early):**
- Run lint checks on all impacted zones
- If lint fails on a CRITICAL zone → report BLOCKED, skip remaining tests for that zone

**Smoke tests (parallel by zone if subprocess available):**
Launch subprocesses in parallel — one per zone — each running the zone's smoke tests and returning results. If subprocess unavailable, run sequentially.

**E2E tests (parallel by zone if subprocess available):**
Same parallel pattern as smoke tests.

**Visual snapshots (parallel by zone if subprocess available):**
Same parallel pattern as smoke tests.

Present results progressively:

"**Lint:** {PASS/FAIL} ({details})
**Smoke tests:** {N}/{M} passed ({details})
**E2E tests:** {N}/{M} passed ({details})
**Visual snapshots:** {N}/{M} passed ({details})"

### 4. Collect Results

For each safety net executed, record:
- Safety net name
- Zone
- Risk level
- Result: PASS / FAIL / SKIP / ERROR
- Details (failure message or skip reason)

### 5. Compute Verdict

Apply verdict rules from the protocol:

- **BLOCKED:** At least 1 FAIL on a CRITICAL zone (risk >= 8)
- **WARNING:** FAILs only on HIGH or lower zones
- **CLEAR:** All PASS (or only SKIPs on LOW zones)

Handle edge cases:
- Zones with no safety nets → report as UNCOVERED
- Safety net errors (not failures) → report as ERROR, exclude from verdict

"**Verdict: {BLOCKED / WARNING / CLEAR}**

{BLOCKED: '🛑 Changes impact critical zones with failing tests. Do NOT proceed without fixing.'}
{WARNING: '⚠️ Some tests failed on non-critical zones. Review before proceeding.'}
{CLEAR: '✅ All safety nets passed. Changes are safe to proceed.'}"

### 6. Write Results to Report

Append to `{outputFile}`:
- Fill "Validation Results" table with all test results
- Fill "Verdict" section with computed verdict and message

Update frontmatter:
- `stepsCompleted`: append `'step-04-validate'`
- `lastStep`: `'step-04-validate'`
- `verdict`: `'{BLOCKED/WARNING/CLEAR}'`

### 7. Proceed to Report

Display: **[C] Continue to final report**

#### Menu Handling Logic:

- IF C: Ensure `{outputFile}` is saved with all results, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Selection protocol loaded and followed exactly
- Safety nets selected by risk score per zone
- Execution order respected (lint → smoke → e2e → visual)
- Parallel execution used where possible (Pattern 4)
- Results collected for every safety net
- Verdict computed correctly based on protocol rules
- Edge cases handled (UNCOVERED zones, ERROR results)
- All results written to impact report

### ❌ SYSTEM FAILURE:

- Not loading the selection protocol
- Selecting tests without risk-based prioritization
- Wrong execution order
- Not reporting UNCOVERED zones
- Modifying code or suggesting fixes
- Computing verdict incorrectly
- Not writing results to report

**Master Rule:** Follow the selection protocol exactly. Execute in order. Compute the verdict correctly. Report everything — no hiding failures.
