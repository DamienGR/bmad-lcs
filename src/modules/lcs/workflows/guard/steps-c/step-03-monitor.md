---
name: 'step-03-monitor'
description: 'Track actual changes via git diff, compare against declared scope, flag drift'

nextStepFile: './step-04-validate.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
---

# Step 3: Change Monitoring

## STEP GOAL:

To analyze what the developer actually changed using git diff, compare actual changes against the declared scope from step-01, flag any scope drift (changes outside the declared scope), and update the impact report with actual change data.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the git diff analysis
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — monitoring what actually changed
- ✅ Be factual — report what changed, flag surprises, don't judge
- ✅ Scope drift is not an error — it's information that affects validation scope

### Step-Specific Rules:

- 🎯 Focus ONLY on change detection and scope comparison
- 🚫 FORBIDDEN to run tests or validate — that's step 04
- 💬 Present changes clearly with scope drift highlighted
- 📋 If new zones are impacted (not in original scope), flag them for additional validation

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append actual changes to the "Actual Changes" section of `{outputFile}`
- 📖 Update `stepsCompleted` in output frontmatter when complete
- 🚫 Do NOT validate, test, or modify any code

## CONTEXT BOUNDARIES:

- Available: impact report with declared scope and impact analysis from step-02
- Focus: git diff analysis, scope comparison, drift detection
- Limits: detection only — no validation, no testing
- Dependencies: step-02 completed, developer has made their changes

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Run Git Diff

Execute `git diff` (or `git diff --staged` if changes are staged) to identify all modified files.

Also check `git status` for new/deleted files.

"**Analyzing your changes...**"

### 2. Summarize Actual Changes

"**Changes detected:**

| File | Change Type | Lines Changed |
|------|------------|---------------|
{table of all modified/added/deleted files}

**Total:** {N} files modified, {added} added, {deleted} deleted"

### 3. Compare Against Declared Scope

Read `{outputFile}` frontmatter `changeScope` (declared scope from step-01).

Compare actual changes vs declared scope:

**In scope:** Files/changes that match the declared scope
**Scope drift:** Files/changes NOT in the original declared scope

"**Scope comparison:**

**In scope ({N} files):**
{list files that match declared scope}

**Scope drift ({N} files):**
{list files outside declared scope — if any}

{If drift detected: '⚠️ **Scope drift detected.** {N} files were changed outside the declared scope. These will be included in the validation.'}
{If no drift: '✅ **All changes within declared scope.** No drift detected.'}"

### 4. Assess Additional Impact from Drift

If scope drift is detected:

- Load `{riskMapPath}` to check risk scores of newly impacted zones
- Cross-reference drifted files with zone mappings
- Identify any new high-risk zones not covered by step-02 analysis

"**Additional zones impacted by drift:**

| Zone | Risk Score | Originally Analyzed | New |
|------|-----------|--------------------|----|
{table — mark new zones that weren't in step-02 analysis}"

If no drift: skip this section.

### 5. Write to Report

Append to `{outputFile}`:
- Fill "Actual Changes > Git Diff Summary" with change table
- Fill "Actual Changes > Scope Drift" with comparison results
- If drift: add new zones to the "Impact Analysis > Impacted Zones" table

Update frontmatter:
- `stepsCompleted`: append `'step-03-monitor'`
- `lastStep`: `'step-03-monitor'`

### 6. Proceed to Validation

"**Change monitoring complete.**

**{N} files changed** — {drift_status}.
{If drift: '{N} additional zones flagged for validation.'}

Proceeding to safety net validation..."

Display: **[C] Continue to validation**

#### Menu Handling Logic:

- IF C: Ensure `{outputFile}` is saved, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Git diff executed and all changes captured
- Actual changes compared against declared scope
- Scope drift detected and flagged (if any)
- Additional impact zones identified from drift
- All findings written to impact report
- Proceeding to validation

### ❌ SYSTEM FAILURE:

- Not running git diff
- Not comparing against declared scope
- Ignoring scope drift
- Not flagging new zones from drift
- Running tests in this step
- Not writing changes to the report

**Master Rule:** Detect everything, flag drift, write to report. No judgement, no validation — just accurate change tracking.
