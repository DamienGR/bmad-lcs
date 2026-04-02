---
name: 'step-02-validate'
description: 'Validate framework profile completeness against quality criteria for each of the 6 sections'

nextStepFile: './step-03-enrich.md'
outputFile: '{output_folder}/lcs/framework-profile-active.md'
validationChecklist: '../data/validation-checklist.md'
---

# Step 2: Validate Profile Completeness

## STEP GOAL:

To validate the completeness and quality of each of the 6 framework profile sections against defined criteria, score the profile, classify gaps by severity, and pass the validation results to step 03 for targeted enrichment.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER modify the output file in this step — validation is in memory only
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 📋 YOU ARE A VALIDATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You bring quality assessment expertise; the profile provides the content
- ✅ Be transparent about gaps — the user needs honest assessment

### Step-Specific Rules:

- 🎯 Focus ONLY on validating the 6 sections against the checklist criteria
- 🚫 FORBIDDEN to write to `{outputFile}` — validation stays in memory
- 🚫 FORBIDDEN to scan the project repo — that's step 03
- 🚫 FORBIDDEN to attempt to fill gaps — that's step 03
- 💬 Present validation results clearly with gap classifications

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store validation_score and gaps[] IN MEMORY ONLY
- 📖 Pass gaps to step 03 via memory for targeted enrichment
- 🚫 Do NOT update `{outputFile}` frontmatter — step 03 handles final write

## CONTEXT BOUNDARIES:

- Available: `{outputFile}` with profile content from step 01; framework, repo_path in memory
- Focus: quality assessment of each section against defined criteria
- Limits: do not attempt enrichment, do not scan project files
- Dependencies: `{outputFile}` must exist from step 01

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Validation Criteria

Load `{validationChecklist}` to get the quality criteria for each of the 6 sections.

Read `{outputFile}` to access the current profile content.

"**Validating profile completeness...**"

### 2. Validate Each Section

For each of the 6 sections, check against the criteria from the checklist:

**Section 1 — Architecture** (Severity: CRITICAL)
- Check: file structure AND design patterns present?
- Status: COMPLETE / PARTIAL / EMPTY

**Section 2 — Common Patterns** (Severity: CRITICAL)
- Check: customization approaches present?
- Status: COMPLETE / PARTIAL / EMPTY

**Section 3 — Known Pitfalls** (Severity: CRITICAL)
- Check: security AND performance items present?
- Status: COMPLETE / PARTIAL / EMPTY

**Section 4 — CLI Tools** (Severity: WARNING)
- Check: primary CLI with specific commands listed?
- Status: COMPLETE / PARTIAL / EMPTY

**Section 5 — Test Patterns** (Severity: CRITICAL)
- Check: critical routes for E2E present?
- Status: COMPLETE / PARTIAL / EMPTY

**Section 6 — Database** (Severity: WARNING)
- Check: key tables listed?
- Status: COMPLETE / PARTIAL / EMPTY

### 3. Calculate Score and Classify Gaps

Count sections with COMPLETE status → `validation_score` (X/6)

For each non-COMPLETE section, create a gap entry:

```
gap: {
  section: '[section name]',
  status: 'PARTIAL' or 'EMPTY',
  severity: 'CRITICAL' or 'WARNING',
  missing: '[what specific criteria are not met]'
}
```

Store all gaps in memory as `gaps[]`.

### 4. Present Validation Report

"**Profile Validation Report**

**Score: {validation_score}/6 sections complete**

| Section | Status | Severity | Missing |
|---------|--------|----------|---------|
| Architecture | {status} | CRITICAL | {what's missing or 'N/A'} |
| Common Patterns | {status} | CRITICAL | {what's missing or 'N/A'} |
| Known Pitfalls | {status} | CRITICAL | {what's missing or 'N/A'} |
| CLI Tools | {status} | WARNING | {what's missing or 'N/A'} |
| Test Patterns | {status} | CRITICAL | {what's missing or 'N/A'} |
| Database | {status} | WARNING | {what's missing or 'N/A'} |

**{count} gaps identified — {critical_count} CRITICAL, {warning_count} WARNING**

**Proceeding to enrichment to address gaps...**"

### 5. Proceed to Enrichment

#### Menu Handling Logic:

- After validation report is presented, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed validation step with no user choices
- Gaps are passed to step 03 via memory for targeted enrichment
- Proceed directly to step 03 after presenting the report

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN all 6 sections have been validated against the checklist criteria and the validation report has been presented will you then load and read fully `{nextStepFile}` to execute profile enrichment.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All 6 sections validated against checklist criteria
- Each section scored: COMPLETE / PARTIAL / EMPTY
- `validation_score` calculated correctly
- Gaps classified with correct severity (CRITICAL / WARNING)
- Validation report presented clearly to user
- All results stored in memory (NOT written to file)
- Gaps passed to step 03 for targeted enrichment

### ❌ SYSTEM FAILURE:

- Writing validation results to `{outputFile}`
- Skipping sections during validation
- Not using the checklist criteria (guessing completeness)
- Attempting to fill gaps or scan the project in this step
- Not presenting the validation report to the user
- Incorrect severity classification

**Master Rule:** Validate against the checklist. Report honestly. Do NOT modify files. Pass gaps to step 03.
