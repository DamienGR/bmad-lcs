---
name: 'step-01-init'
description: 'Initialize impact analysis, discover inputs, validate and refine scope'

nextStepFile: './step-02-direct-deps.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
templateFile: '../templates/impact-report.template.md'
auditInputContract: '../data/audit-input-contract.md'
moduleInputFolder: '{output_folder}/lcs'
---

# Step 1: Init, Scope Refinement & History Loading

## STEP GOAL:

To initialize the impact analysis by discovering required inputs (audit data, framework profile), loading any previous analyses for context, collecting the planned change description from the user, and validating the scope is specific enough for meaningful analysis.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst preparing to analyze a planned change
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ We engage in collaborative dialogue, not command-response
- ✅ You bring expertise in dependency analysis and risk assessment, user brings knowledge of their planned change and codebase
- ✅ Maintain a technical, precise, factual tone throughout

### Step-Specific Rules:

- 🎯 Focus only on initialization: input discovery, scope definition, and history loading
- 🚫 FORBIDDEN to start analyzing dependencies — that's step 02
- 💬 If the user's scope description is too vague, push back and request specifics
- 📋 Validate audit data against the input contract before proceeding

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Create the output report from template and populate the Scope section
- 📖 Update output frontmatter with stepsCompleted when this step is done
- 🚫 Do not proceed if required inputs are missing — guide the user

## CONTEXT BOUNDARIES:

- Available context: workflow config, module config, audit input contract
- Focus: discovering inputs, defining scope, establishing analysis context
- Limits: do NOT begin dependency analysis
- Dependencies: none — this is the first step

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Welcome and Explain

"**Impact Analysis Workflow**

I'll analyze the potential impact of your planned change to identify dependencies, coupling, related tests, and risks before you modify anything.

Let me first check for the required inputs..."

### 2. Discover Audit Data

Load {auditInputContract} to understand expected formats.

Search {moduleInputFolder} for audit data:

**Search order:**
1. `{moduleInputFolder}/audit/audit-report-*.md`
2. `{moduleInputFolder}/audit/dependency-graph-*.md`
3. `{moduleInputFolder}/audit/framework-profile-*.md`

**Present findings:**

"**Input Discovery:**
- [Found/Missing] Audit report: {filename} ({age})
- [Found/Missing] Dependency graph: {filename} ({age})
- [Found/Missing] Framework profile: {filename} ({age})"

**Validation:**
- If no audit data found → Error: "No audit data found. Run the audit workflow first to generate codebase analysis."
- If partial data → Warning: "Audit data is incomplete. Impact analysis will be limited. Proceed anyway?"
- If stale (>7 days) → Warning: "Audit data is {N} days old. Results may not reflect recent changes. Proceed anyway?"

### 3. Discover Previous Impact Reports

Search `{moduleInputFolder}/guard/impact-report-*.md` for previous analyses.

**If found:**
"**Previous analyses found:**
- {filename} ({date}) — Scope: {extract scope summary}

I'll cross-reference these for historical context."

**If none found:**
"No previous impact analyses found. Starting fresh."

### 4. Collect Change Description

"**What change are you planning?**

Please describe:
1. **What files or features** will you modify?
2. **What is the nature of the change?** (refactor, bug fix, new feature, dependency update, etc.)
3. **Specific functions or classes** affected, if known"

### 5. Validate and Refine Scope

**Analyze the user's description for specificity:**

**Too vague indicators:**
- "I'm changing the auth module" (which files? which functions?)
- "Updating the frontend" (which components? which pages?)
- "Fixing a bug" (where? in what function?)

**If too vague:**
"Your scope is too broad for a meaningful analysis. Can you narrow it down?

Specifically, I need:
- At least one **specific file path** or **function name**
- The **type of change** (adding, removing, modifying behavior)

Example: 'I'm modifying the `authenticate()` function in `wp-includes/user.php` to add OAuth2 support'"

**If specific enough:**
"**Scope confirmed.** I have enough detail to begin the analysis."

### 6. Optional: Depth Limit

"**Depth limit for indirect coupling analysis?**

Default is 3 levels deep. Higher values find more distant impacts but take longer.
- **1-2:** Quick scan, direct impacts only
- **3:** Standard (recommended)
- **4-5:** Deep scan, may surface surprising impacts

Enter a number or press Enter for default (3):"

### 7. Create Output Report

Create {outputFile} from {templateFile}.

Populate the frontmatter:
```yaml
stepsCompleted: ['step-01-init']
lastStep: 'step-01-init'
date: '{date}'
user_name: '{user_name}'
project_name: '{project_name}'
changeDescription: '{user description}'
depthLimit: {selected or default 3}
previousReports: [{list of discovered reports}]
```

Populate the **## Scope** section with:
- Change description (from user)
- Files/functions targeted
- Scope boundaries
- Nature of the change
- Depth limit setting
- Audit data status (age, completeness)
- Previous analysis references (if any)

### 8. Present MENU OPTIONS

Display: "**Scope defined. Select:** [C] Continue to Dependency Analysis"

#### Menu Handling Logic:

- IF C: Save Scope section to {outputFile}, update frontmatter stepsCompleted, then load, read entire file, then execute {nextStepFile}
- IF Any other: help user respond, then [Redisplay Menu Options](#8-present-menu-options)

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN the output report has been created with a complete Scope section, and the user selects 'C', will you then load and read fully `{nextStepFile}` to execute the Direct Dependencies analysis.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Audit data discovered and validated against input contract
- Previous impact reports loaded for context (if available)
- User's change description collected with sufficient specificity
- Scope validated — not too vague
- Output report created from template with populated Scope section
- Frontmatter updated with stepsCompleted

### ❌ SYSTEM FAILURE:

- Proceeding without audit data and no user acknowledgment
- Accepting a vague scope without pushing for specifics
- Not validating audit data freshness
- Not creating the output report before proceeding
- Starting dependency analysis in this step

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
