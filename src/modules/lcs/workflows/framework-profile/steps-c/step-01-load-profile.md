---
name: 'step-01-load-profile'
description: 'Collect inputs and load or create the framework profile'

nextStepFile: './step-02-validate.md'
outputFile: '{output_folder}/lcs/framework-profile-active.md'
frameworksPath: '{project-root}/src/modules/lcs/data/frameworks/'
frameworkTemplatePath: '{project-root}/src/modules/lcs/data/frameworks/_template.md'
---

# Step 1: Load Framework Profile

## STEP GOAL:

To collect the required inputs (framework name and repo path), load the corresponding framework profile from `{frameworksPath}`, and create the initial `{outputFile}` that will be validated and enriched in subsequent steps.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER guess the framework — use confirmed detection from pre-flight or ask the user
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You bring framework knowledge; the developer brings their project context
- ✅ If inputs are already in memory from pre-flight, use them — don't re-ask

### Step-Specific Rules:

- 🎯 Focus ONLY on collecting inputs and loading the profile
- 🚫 FORBIDDEN to validate profile sections — that's step 02
- 🚫 FORBIDDEN to scan the project for enrichment data — that's step 03
- 💬 If profile doesn't exist, copy the template as-is — no elaborate dialogue

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Create `{outputFile}` from profile content or template
- 📖 Store `framework`, `framework_version`, `repo_path`, `profile_source` in memory
- 🚫 Do NOT write to sidecar-state — that's step 03

## CONTEXT BOUNDARIES:

- Available: `framework`, `repo_path` from pre-flight step 02 memory (or user provides)
- Focus: loading the right profile file and creating the initial output
- Limits: do not analyze profile quality, project files, or enrichment data
- Dependencies: framework must be detected/confirmed before this workflow runs

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Collect Inputs

**IF framework and repo_path are already in memory** (from pre-flight):

"**Framework Profile — Loading profile for {framework} {framework_version}**

Using inputs from pre-flight:
- Framework: {framework} {framework_version}
- Repository: {repo_path}"

**IF inputs are NOT in memory:**

"**Framework Profile — I need a few inputs to get started.**

1. **What framework is this project using?** (e.g., wordpress, drupal)
2. **What version?** (e.g., 6.4, 10.x — optional)
3. **What is the repository path?**"

Wait for user response. Store: `framework`, `framework_version`, `repo_path`.

### 2. Construct Profile Path and Load

Construct the profile path: `{frameworksPath}{framework}.md`

**Attempt to load the profile file.**

**IF profile file exists:**

Read the profile completely. Store contents in memory.

"**Profile found:** `{framework}.md` loaded successfully."

Set `profile_source: 'existing'`

**IF profile file does NOT exist:**

"**No profile found for {framework}.** Creating a minimal profile from the template."

Load `{frameworkTemplatePath}` and use its contents as the base profile.

Set `profile_source: 'template'`

### 3. Create Initial Output File

Create `{outputFile}` with:

**Frontmatter:**
```yaml
---
workflowType: 'framework-profile'
stepsCompleted: ['step-01-load-profile']
framework: '{framework}'
framework_version: '{framework_version}'
profile_source: '{profile_source}'
validation_score: ''
date: '{date}'
---
```

**Body:** The loaded profile content (from existing profile or template).

### 4. Summary and Proceed

"**Profile loaded.**

- **Framework:** {framework} {framework_version}
- **Source:** {existing profile / template copy}
- **Output:** `{outputFile}` created

**Proceeding to profile validation...**"

#### Menu Handling Logic:

- After output file is created, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed init step with no user choices needed after inputs are collected
- Proceed directly to step 02 after creating the output file

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN `{outputFile}` has been created with the profile content and valid frontmatter will you then load and read fully `{nextStepFile}` to execute profile validation.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Framework and repo_path collected (from memory or user)
- Profile loaded from `{frameworksPath}` or created from template
- `{outputFile}` created with correct frontmatter and profile content
- `stepsCompleted` includes 'step-01-load-profile'
- `profile_source` correctly set (existing/template)

### ❌ SYSTEM FAILURE:

- Guessing the framework without confirmation
- Attempting to validate or enrich the profile in this step
- Not creating `{outputFile}` before proceeding
- Hardcoded paths instead of variables
- Elaborate dialogue when profile is missing — just copy the template

**Master Rule:** Load the profile and create the output file. Nothing more. Validation and enrichment happen in steps 02 and 03.
