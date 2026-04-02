---
name: 'step-03-indirect-coupling'
description: 'Identify indirect coupling across three dimensions: code, data, and framework'

nextStepFile: './step-04-related-tests.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
couplingPatterns: '../data/coupling-analysis-patterns.md'
---

# Step 3: Indirect Coupling (Code + Data + Framework)

## STEP GOAL:

To identify all indirect coupling that could be affected by the planned change, analyzing three distinct dimensions: code coupling (shared state, globals, events), data coupling (DB triggers, stored procedures, SQL, cache), and framework coupling (hooks, filters, action chains).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst performing deep coupling analysis
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ Technical, precise — this is where "surprising" impacts hide
- ✅ You bring expertise in hidden coupling patterns and side effects

### Step-Specific Rules:

- 🎯 Analyze coupling across ALL THREE dimensions — do not skip any
- 🚫 FORBIDDEN to skip data coupling or framework coupling — these are where surprises hide
- 💬 Use subprocess Pattern 2 + 4: launch 3 parallel subprocesses (code, data, framework), each performs deep per-file analysis and returns structured findings
- ⚙️ If subprocess unavailable, perform all three analyses sequentially in main thread
- 🔍 DO NOT BE LAZY — analyze EACH dimension thoroughly

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append findings to all three Indirect Coupling subsections of {outputFile}
- 📖 Update output frontmatter stepsCompleted when this step is done
- 🚫 Honor the depth limit from step 01 frontmatter

## CONTEXT BOUNDARIES:

- Available: Scope from step 01, direct dependencies from step 02, audit data, framework profile, depth limit setting
- Focus: indirect coupling ONLY — direct dependencies were covered in step 02
- Limits: respect depth limit setting, do NOT map tests (that's step 04)
- Dependencies: steps 01 and 02 must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Context

Read {outputFile} to get:
- Scope (target files/functions)
- Direct dependencies from step 02
- Depth limit from frontmatter
- Framework profile from audit data

"**Analyzing indirect coupling across code, data, and framework dimensions...**"

### 2. Launch Parallel Coupling Analysis

Load {couplingPatterns} to get the analysis checkpoints and return structures for each dimension.

**Launch 3 subprocesses in parallel** (Pattern 4), each analyzing one coupling dimension:

**Subprocess A: Code Coupling**
DO NOT BE LAZY — For EACH file in scope and direct dependencies, analyze all code coupling checkpoints from {couplingPatterns} Dimension A (shared state, shared objects, event systems, config dependencies, session/request state). Return structured findings using the return structure defined in {couplingPatterns}.

**Subprocess B: Data Coupling**
DO NOT BE LAZY — Analyze all data coupling checkpoints from {couplingPatterns} Dimension B (DB triggers, stored procedures, SQL queries, migrations, cache invalidation, search indexes). Return structured findings using the return structure defined in {couplingPatterns}.

**Subprocess C: Framework Coupling**
DO NOT BE LAZY — Analyze all framework-specific checkpoints from {couplingPatterns} Dimension C, using the appropriate framework section (WordPress, Drupal, Laravel, General) based on the framework profile. Return structured findings using the return structure defined in {couplingPatterns}.

**If subprocess unavailable:** Perform all three analyses sequentially in main thread, following the same checkpoints and return structures from {couplingPatterns}.

### 3. Aggregate and Analyze Results

Combine findings from all 3 subprocesses:

- **Total indirect coupling points:** {sum of all findings}
- **Surprising impacts:** {collect all findings where surprising: true}
- **Risk distribution:** HIGH / MEDIUM / LOW counts per dimension
- **Cross-dimension impacts:** Where a finding in one dimension compounds with another

### 4. Highlight Surprising Impacts

For each finding marked `surprising: true`:

"⚠️ **SURPRISING IMPACT:** {explanation}
- **Dimension:** {code/data/framework}
- **Risk:** {HIGH/MEDIUM/LOW}
- **Why surprising:** {developer would not typically expect this coupling}"

### 5. Append to Report

Append findings to {outputFile} in three subsections:

**### Code Coupling**
- List all code coupling findings with risk levels
- Flag surprising impacts with ⚠️

**### Data Coupling**
- List all data coupling findings with risk levels
- Flag surprising impacts with ⚠️

**### Framework Coupling**
- List all framework coupling findings with risk levels
- Flag surprising impacts with ⚠️

Update frontmatter:
```yaml
stepsCompleted: ['step-01-init', 'step-02-direct-deps', 'step-03-indirect-coupling']
lastStep: 'step-03-indirect-coupling'
```

### 6. Present MENU OPTIONS

Display: "**Indirect coupling analysis complete. {total} coupling points found ({surprising} surprising). Proceeding to Related Tests mapping...**"

#### Menu Handling Logic:

- After all three subsections are appended to {outputFile} and frontmatter updated, immediately load, read entire file, then execute {nextStepFile}

#### EXECUTION RULES:

- This is an auto-proceed step — analysis is autonomous
- Proceed directly to next step after findings are appended to report

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN all three Indirect Coupling subsections (Code, Data, Framework) have been populated with findings and frontmatter updated, will you then load and read fully `{nextStepFile}` to execute the Related Tests mapping.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All three coupling dimensions analyzed (code, data, framework)
- Depth limit from step 01 respected
- Surprising impacts identified and flagged with ⚠️
- Cross-dimension impacts noted
- All three subsections populated in report
- Frontmatter updated with stepsCompleted
- Auto-proceeded to step 04

### ❌ SYSTEM FAILURE:

- Skipping any of the three coupling dimensions
- Being lazy — not analyzing each dimension thoroughly
- Not flagging surprising impacts
- Not respecting the depth limit
- Analyzing test coverage (that's step 04)
- Not appending to report before proceeding

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
