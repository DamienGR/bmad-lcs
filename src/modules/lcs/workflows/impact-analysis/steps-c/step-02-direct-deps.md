---
name: 'step-02-direct-deps'
description: 'Trace direct callers, importers, and includers of the targeted files/functions'

nextStepFile: './step-03-indirect-coupling.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
---

# Step 2: Direct Dependencies

## STEP GOAL:

To trace all direct callers, importers, and includers of the files and functions identified in the scope, building a complete map of first-level dependencies that would be affected by the planned change.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst performing dependency tracing
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ Technical, precise, factual — report what you find, no speculation
- ✅ You bring deep expertise in code dependency analysis

### Step-Specific Rules:

- 🎯 Focus ONLY on direct (first-level) dependencies — indirect coupling is step 03
- 🚫 FORBIDDEN to analyze shared state, globals, or framework hooks — that's step 03
- 💬 Use subprocess Pattern 1 for efficient grep across codebase
- ⚙️ If subprocess unavailable, perform grep operations in main thread

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append findings to the Direct Dependencies section of {outputFile}
- 📖 Update output frontmatter stepsCompleted when this step is done
- 🚫 Do not analyze beyond first-level dependencies

## CONTEXT BOUNDARIES:

- Available: Scope from step 01 (files, functions, change description), audit data (dependency graph)
- Focus: direct callers, importers, includers ONLY
- Limits: do NOT trace indirect coupling or shared state
- Dependencies: step 01 must be complete with populated Scope section

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Scope from Report

Read {outputFile} to get:
- Target files and functions from Scope section
- Audit data references (dependency graph)

"**Analyzing direct dependencies for the scoped change...**"

### 2. Trace Direct Importers/Includers

For each target file in the scope:

**Launch a subprocess that:**
1. Searches the entire codebase for import/require/include/use statements referencing the target file
2. Returns ONLY matching files with the specific import line

**Search patterns (framework-aware):**
- PHP: `require`, `require_once`, `include`, `include_once`, `use`
- JavaScript/TypeScript: `import`, `require`, `from`
- Python: `import`, `from ... import`
- General: file path references in config files

**Return structure:**
```json
{
  "targetFile": "path/to/file.php",
  "importedBy": [
    {"file": "path/to/caller.php", "line": 45, "statement": "require_once 'file.php'"}
  ],
  "totalImporters": 3
}
```

**If subprocess unavailable:** Perform the grep operations directly in main thread using file I/O tools.

### 3. Trace Direct Callers

For each target function/method in the scope:

**Launch a subprocess that:**
1. Searches for direct calls to the target function/method across the codebase
2. Returns ONLY matching call sites with file, line, and context

**Search patterns:**
- Function calls: `functionName(`
- Method calls: `->methodName(`, `::methodName(`, `.methodName(`
- Class instantiation: `new ClassName`

**Return structure:**
```json
{
  "targetFunction": "authenticate()",
  "calledBy": [
    {"file": "path/to/login.php", "line": 78, "context": "if (authenticate($user, $pass))"}
  ],
  "totalCallers": 5
}
```

**If subprocess unavailable:** Perform the search directly in main thread.

### 4. Cross-Reference with Audit Dependency Graph

If audit dependency graph is available:
- Validate grep findings against the dependency graph
- Identify any dependencies in the graph that grep may have missed (dynamic includes, variable-based imports)
- Flag discrepancies

### 5. Compile Direct Dependency Map

Organize findings into:

**By target file/function:**
- File X is imported by: [list]
- Function Y is called by: [list]

**Summary statistics:**
- Total files directly affected: {count}
- Total direct call sites: {count}
- Any unexpected importers: [flag these]

### 6. Append to Report

Append findings to the **## Direct Dependencies** section of {outputFile}:

- List of all direct importers/includers per target file
- List of all direct callers per target function
- Summary statistics
- Any discrepancies with audit data
- Flag any unexpected findings with ⚠️ marker

Update frontmatter:
```yaml
stepsCompleted: ['step-01-init', 'step-02-direct-deps']
lastStep: 'step-02-direct-deps'
```

### 7. Present MENU OPTIONS

Display: "**Direct dependency analysis complete. {count} files directly affected. Proceeding to Indirect Coupling analysis...**"

#### Menu Handling Logic:

- After dependency map is appended to {outputFile} and frontmatter updated, immediately load, read entire file, then execute {nextStepFile}

#### EXECUTION RULES:

- This is an auto-proceed step — analysis is autonomous
- Proceed directly to next step after findings are appended to report

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN the Direct Dependencies section has been populated with complete findings and frontmatter updated, will you then load and read fully `{nextStepFile}` to execute the Indirect Coupling analysis.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All direct importers/includers traced for each target file
- All direct callers traced for each target function
- Cross-referenced with audit dependency graph (if available)
- Findings appended to Direct Dependencies section of report
- Unexpected dependencies flagged
- Frontmatter updated with stepsCompleted
- Auto-proceeded to step 03

### ❌ SYSTEM FAILURE:

- Analyzing indirect coupling (shared state, globals) in this step
- Missing direct dependencies that exist in the codebase
- Not cross-referencing with audit data
- Not flagging unexpected findings
- Not appending to report before proceeding

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
