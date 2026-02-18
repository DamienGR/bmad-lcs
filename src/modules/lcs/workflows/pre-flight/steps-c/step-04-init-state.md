---
name: 'step-04-init-state'
description: 'Initialize consolidation state and write all pre-flight artifacts'

nextStepFile: './step-05-report.md'
sidecarStateSchema: '{project-root}/src/modules/lcs/data/sidecar-state-schema.yaml'
artifactConventions: '{project-root}/src/modules/lcs/data/artifact-conventions.md'
outputBasePath: '{output_folder}/lcs'
---

# Step 4: Initialize State

## STEP GOAL:

To create the consolidation output directory structure, initialize `sidecar-state.yaml` from the schema template with all data collected in steps 01-03, write the `capability-matrix.md` artifact, and validate all written files before proceeding.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the validation step — re-read every file after writing
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 🎯 Copy from the schema template — do NOT build YAML from scratch
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — methodical and precise for state initialization
- ✅ This step creates the foundation for ALL subsequent LCS workflows
- ✅ Accuracy is critical — a malformed sidecar-state breaks everything

### Step-Specific Rules:

- 🎯 Focus ONLY on directory creation, file writing, and validation
- 🚫 FORBIDDEN to analyze code or run additional checks
- 🚫 FORBIDDEN to proceed to step 05 if validation fails
- 💬 Report each file creation: path, status
- 📋 Use schema template as base — guarantees conformity

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write: sidecar-state.yaml, capability-matrix.md
- 📖 Validate: re-read each file after writing
- 🚫 If validation fails: report error, do NOT proceed

## CONTEXT BOUNDARIES:

- Available: ALL data from steps 01-03 (repo_path, site_url, capabilities, framework, MCP tools)
- Focus: state initialization and artifact creation only
- Limits: do not perform any new checks or analysis
- Dependencies: all data must be collected from previous steps

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load References

Load `{artifactConventions}` to understand the required directory structure.
Load `{sidecarStateSchema}` as the template for sidecar-state.yaml.

### 2. Create Directory Structure

Create the full `{outputBasePath}/` directory tree as specified in artifact conventions:

```
{outputBasePath}/
├── pre-flight/
├── audit/
├── safety-nets/
├── dependency-update/
├── refactoring/
├── deep-testing/
└── guard/
    └── impact-analyses/
```

Report: "Directory structure created at `{outputBasePath}/`"

### 3. Initialize sidecar-state.yaml

**Copy** the schema from `{sidecarStateSchema}` as the base file.

**Populate** with collected data:

```yaml
project:
  name: "{project_name from config or repo directory name}"
  framework: "{detected framework}"
  framework_version: "{detected version}"
  repo_path: "{repo_path from user}"
  site_url: "{site_url from user}"
  ddev_project: "{ddev project name if detected, else empty}"

consolidation:
  current_phase: "pre-flight"
  started_at: "{current date/time}"
  last_session: "{current date/time}"

capabilities:
  git: {true/false from step 01}
  ddev: {true/false from step 01}
  mysql: {true/false — from MCP check or ddev}
  node: {true/false from step 01}
  composer: {true/false from step 01}
  php_codesniffer: {true/false from step 01}
```

**Update pre-flight tasks:**

```yaml
tasks:
  pre-flight:
    - id: "env-check"
      status: "done"
      result: "{summary: X passed, Y missing}"
      artifact: "pre-flight/capability-matrix.md"
    - id: "framework-detection"
      status: "done"
      result: "{framework} {version} (confidence: {level})"
      artifact: null
    - id: "framework-profile-load"
      status: "{done if profile loaded, skipped if no profile}"
      result: "{profile loaded / no profile available}"
      artifact: null
```

Write to: `{outputBasePath}/sidecar-state.yaml`

### 4. Write capability-matrix.md

Create the capability matrix artifact with standard frontmatter:

```markdown
---
phase: pre-flight
task_id: env-check
produced_by: lcs-lead
date: {current date}
status: complete
---

# Pre-flight Capability Matrix

## Project Information

| Field | Value |
|-------|-------|
| Project Name | {name} |
| Framework | {framework} {version} |
| Detection Confidence | {high/medium/low} |
| Repository | {repo_path} |
| Site URL | {site_url} |
| DDEV Project | {name or N/A} |

## Environment Capabilities

| Tool | Status | Category | Version | Notes |
|------|--------|----------|---------|-------|
| Git | {OK/MISSING} | BLOCKER | {version} | {notes} |
| Site Access | {OK/MISSING} | BLOCKER | — | {HTTP status} |
| Repo Access | {OK/MISSING} | BLOCKER | — | {notes} |
| DDEV | {OK/MISSING} | WARNING | {version} | {notes} |
| Node.js | {OK/MISSING} | WARNING | {version} | {notes} |
| Composer | {OK/MISSING} | WARNING | {version} | {notes} |
| PHP_CodeSniffer | {OK/MISSING} | INFO | {version} | {notes} |

## MCP Tools Inventory

| Tool | Status | Smoke Test | Impacted Phase(s) |
|------|--------|------------|-------------------|
{one row per MCP tool from step 03}

## Summary

- **Environment:** {X} checks passed, {Y} missing ({Z} blockers, {W} warnings, {V} info)
- **MCP Tools:** {X} available, {Y} missing, {Z} errors
- **Framework:** {framework} {version} (confidence: {level}, profile: {loaded/not loaded})
```

Write to: `{outputBasePath}/pre-flight/capability-matrix.md`

### 5. Validate Written Files

**Re-read sidecar-state.yaml:**
- Verify it's valid YAML (parseable without errors)
- Verify `project.framework` is populated
- Verify `project.repo_path` is populated
- Verify `consolidation.current_phase` is "pre-flight"
- Verify all pre-flight task statuses are "done" or "skipped"

**Re-read capability-matrix.md:**
- Verify frontmatter is present and valid
- Verify tables are populated (not empty placeholders)

**IF validation passes:**
- Report: "State initialized and validated successfully."

**IF validation fails:**
- Report the specific error
- Attempt to fix the issue
- Re-validate
- If still failing: "State initialization failed: {specific error}. Please review manually."
- Do NOT proceed to step 05

### 6. Proceed to Report

Display: "**State initialized — Proceeding to readiness report...**"

#### Menu Handling Logic:

- After validation passes, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed validation step
- Proceed to next step ONLY after validation passes
- IF validation fails: halt, report, do NOT proceed

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Directory tree created matching artifact conventions
- sidecar-state.yaml written from schema template (not from scratch)
- All fields populated with data from steps 01-03
- Pre-flight tasks marked as done
- capability-matrix.md written with standard frontmatter
- Both files validated by re-reading after write
- No empty placeholders in the written files

### ❌ SYSTEM FAILURE:

- Building YAML from scratch instead of copying schema template
- Leaving placeholder values ({...}) in the written files
- Not validating files after writing
- Proceeding to step 05 despite validation failure
- Missing directory creation for any phase

**Master Rule:** Copy from template, populate with real data, validate after write. Never proceed with invalid state.
