---
name: 'step-02-analyze'
description: 'Execute impact analysis on declared change scope — trace dependencies, identify coupling, map tests, score severity'

nextStepFile: './step-03-monitor.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
auditReportPath: '{output_folder}/lcs/audit/audit-report.md'
---

# Step 2: Impact Analysis

## STEP GOAL:

To analyze the developer's declared change scope by tracing direct dependencies, identifying indirect coupling, mapping existing test coverage, and scoring impact severity per zone. Write all findings to the impact report. End by telling the developer to go make their changes.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any analysis phase
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — performing impact analysis
- ✅ Be thorough but fast — this analysis must complete quickly for daily use
- ✅ Highlight "surprising" impacts — the ones the developer wouldn't expect
- ✅ The developer needs actionable information, not academic analysis

### Step-Specific Rules:

- 🎯 Focus ONLY on impact analysis — do not run tests or validate
- 🚫 FORBIDDEN to modify any code or run any safety nets
- 💬 Present findings clearly with risk-scored zone table
- 📋 Use subprocess (Pattern 2) for deep analysis on high-risk zones via Code Analyst sub-agent
- ⚙️ If subprocess/sub-agent unavailable, perform analysis in main thread

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append all findings to the Impact Analysis section of `{outputFile}`
- 📖 Update `stepsCompleted` in output frontmatter when complete
- 🚫 Do NOT run tests, modify code, or validate anything

## CONTEXT BOUNDARIES:

- Available: change scope from step-01, risk map, audit report, sidecar state
- Focus: dependency tracing, coupling analysis, test mapping, severity scoring
- Limits: analysis only — no execution, no modification
- Dependencies: step-01 completed with scope recorded in impact report

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Change Scope

Read `{outputFile}` frontmatter to retrieve `changeScope`.

Read `{riskMapPath}` to load zone data with risk scores.

Read `{auditReportPath}` for code structure and dependency graph data.

"**Analyzing impact for:** {changeScope}"

### 2. Trace Direct Dependencies

For each file/function in the declared scope:

- Identify direct callers (who calls this?)
- Identify direct importers (who imports this?)
- Identify direct includers (who includes this?)

"**Direct dependencies found:**

| Changed Element | Dependents | Count |
|----------------|------------|-------|
{table of direct dependencies}"

### 3. Identify Indirect Coupling

Analyze for indirect impacts:

- **Shared state**: global variables, session data, database tables touched by both changed code and other zones
- **Framework-specific hooks**: WordPress actions/filters, Drupal hooks, event listeners
- **Configuration coupling**: shared config files, environment variables
- **Template inheritance**: parent/child theme relationships, layout inheritance

For high-risk zones (risk score >= 5): launch a sub-agent (Code Analyst) to perform deep dependency tracing and return structured findings. If sub-agent unavailable, perform the analysis in main thread.

"**Indirect coupling identified:**

| Coupling Type | Source | Affected Zone | Risk Score |
|--------------|--------|---------------|-----------|
{table of indirect coupling}"

### 4. Map Existing Test Coverage

Cross-reference impacted zones with safety nets from `{sidecarStatePath}`:

- Which impacted zones have safety nets deployed?
- Which impacted zones are UNCOVERED (no safety nets)?
- What types of tests exist? (smoke, e2e, visual, lint)

"**Test coverage for impacted zones:**

| Zone | Risk Score | Smoke | E2E | Visual | Lint | Coverage |
|------|-----------|-------|-----|--------|------|----------|
{table — COVERED/UNCOVERED per zone}"

### 5. Score Impact Severity

For each impacted zone, compute severity:

- **Risk score** (from risk map)
- **Coupling depth** (direct = 1, indirect = 2+)
- **Test coverage** (covered vs uncovered)
- **Change type** (bug fix = lower risk, refactoring = higher risk, new feature = medium)

"**Impact severity summary:**

| Zone | Risk Score | Coupling | Coverage | Severity |
|------|-----------|----------|----------|----------|
{table sorted by severity descending}

**High-attention zones:** {list zones with severity >= HIGH}
**Surprising impacts:** {list any indirect impacts the developer might not expect}"

### 6. Write Findings to Report

Append all findings to `{outputFile}`:
- Fill "Impact Analysis > Direct Dependencies" section
- Fill "Impact Analysis > Indirect Coupling" section
- Fill "Impact Analysis > Impacted Zones" table
- Fill "Impact Analysis > Existing Test Coverage" section

Update frontmatter:
- `stepsCompleted`: append `'step-02-analyze'`
- `lastStep`: `'step-02-analyze'`

### 7. Instruct Developer to Code

"**Impact analysis complete.**

**{N} zones impacted** — {high_count} high-attention, {surprising_count} surprising impacts.

**Go make your changes now.** When you're done, type **C** to continue and I'll check what actually changed.

If you need to close this session, no problem — relaunch guard mode and I'll pick up where we left off."

Display: **[C] Continue (changes done)**

#### Menu Handling Logic:

- IF C: Ensure `{outputFile}` is saved with all findings, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'
- This is the primary breakpoint — developer may close session here

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Direct dependencies traced for all changed elements
- Indirect coupling identified (shared state, hooks, config, templates)
- Sub-agent used for deep analysis on high-risk zones (or main thread fallback)
- Test coverage mapped for all impacted zones
- Severity scored and sorted
- Surprising impacts highlighted
- All findings written to impact report
- Developer clearly instructed to go code

### ❌ SYSTEM FAILURE:

- Skipping indirect coupling analysis
- Not using risk scores from risk map
- Not identifying uncovered zones
- Not highlighting surprising impacts
- Running tests or modifying code in this step
- Not writing findings to the report before proceeding

**Master Rule:** Analysis must be thorough but fast. Highlight what the developer wouldn't expect. Write everything to the report before letting them go code.
