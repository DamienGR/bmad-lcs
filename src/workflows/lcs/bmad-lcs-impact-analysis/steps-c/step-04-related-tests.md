---
name: 'step-04-related-tests'
description: 'Map existing tests covering impacted zones and identify coverage gaps'

nextStepFile: './step-05-risk-assessment.md'
outputFile: '{output_folder}/lcs/guard/impact-report-{date}.md'
---

# Step 4: Related Tests

## STEP GOAL:

To map all existing tests that cover the impacted zones identified in steps 02 and 03, and identify coverage gaps where changes could introduce undetected regressions.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER generate content without user input
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- 📋 YOU ARE A FACILITATOR, not a content generator
- ✅ YOU MUST ALWAYS SPEAK OUTPUT In your Agent communication style with the config `{communication_language}`
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are a Code Impact Analyst mapping test coverage
- ✅ If you already have been given a name, communication_style and identity, continue to use those while playing this new role
- ✅ Technical, precise — coverage gaps are as important as existing coverage
- ✅ You bring expertise in test architecture and coverage analysis

### Step-Specific Rules:

- 🎯 Focus ONLY on mapping tests and identifying gaps — do not assess risk (that's step 05)
- 🚫 FORBIDDEN to recommend new tests — that's step 05's action plan
- 💬 Use subprocess Pattern 1 for efficient grep of test references across test directories
- ⚙️ If subprocess unavailable, perform grep operations in main thread

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Append findings to the Related Tests section of {outputFile}
- 📖 Update output frontmatter stepsCompleted when this step is done
- 🚫 Do not make risk assessments — just map what exists and what's missing

## CONTEXT BOUNDARIES:

- Available: Scope from step 01, direct deps from step 02, indirect coupling from step 03
- Focus: test coverage mapping and gap identification ONLY
- Limits: do NOT assess risk or recommend actions
- Dependencies: steps 01-03 must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Impact Map

Read {outputFile} to collect the full list of impacted zones:
- Target files/functions (from Scope)
- Direct dependencies (from step 02)
- Indirect coupling points (from step 03)

"**Mapping test coverage for all impacted zones...**"

### 2. Discover Test Directories

Identify test locations in the project:
- Standard locations: `tests/`, `test/`, `spec/`, `__tests__/`
- Framework-specific: `phpunit.xml` config, `jest.config.*`, `pytest.ini`
- Custom locations from audit data

### 3. Search for Tests Referencing Impacted Zones

**Launch a subprocess that:**
1. Searches all test directories for references to impacted files, functions, classes
2. Returns ONLY matching test files with the specific reference lines

**Search patterns:**
- Direct function/method calls in tests
- Class instantiation in test fixtures
- Mock/stub declarations for impacted classes
- Import/require of impacted files in test files
- Test names/descriptions referencing impacted features

**Return structure:**
```json
{
  "impactedZone": "authenticate()",
  "tests": [
    {
      "file": "tests/unit/test-auth.php",
      "testName": "test_authenticate_valid_credentials",
      "type": "unit",
      "references": ["authenticate()", "WP_User"]
    }
  ],
  "totalTests": 4
}
```

**If subprocess unavailable:** Perform the search directly in main thread.

### 4. Classify Test Coverage

For each impacted zone, classify coverage:

**Coverage levels:**
- **Well covered:** Multiple tests, different scenarios (unit + integration)
- **Partially covered:** Some tests exist but incomplete scenarios
- **Indirectly covered:** Tests exist for callers but not the function itself
- **Not covered:** No tests found referencing this zone

### 5. Identify Coverage Gaps

For each impacted zone with partial or no coverage:

**Gap analysis:**
- Which impacted zones have NO test coverage?
- Which surprising impacts (from step 03) have no coverage? (highest risk)
- Which direct dependencies have no test coverage?
- Are there integration tests that span multiple impacted zones?

### 6. Append to Report

Append findings to the **## Related Tests** section of {outputFile}:

**Test Coverage Map:**
| Impacted Zone | Tests Found | Coverage Level | Gap? |
|---|---|---|---|
| {zone} | {count} | {well/partial/indirect/none} | {yes/no} |

**Coverage Gaps (prioritized):**
1. ⚠️ {surprising impact with no coverage} — CRITICAL GAP
2. {direct dependency with no coverage}
3. {zone with partial coverage}

**Summary:**
- Total impacted zones: {count}
- Well covered: {count} ({%})
- Partially covered: {count} ({%})
- Not covered: {count} ({%})

Update frontmatter:
```yaml
stepsCompleted: ['step-01-init', 'step-02-direct-deps', 'step-03-indirect-coupling', 'step-04-related-tests']
lastStep: 'step-04-related-tests'
```

### 7. Present MENU OPTIONS

Display: "**Test mapping complete. {covered}% of impacted zones have test coverage. {gaps} critical gaps found. Proceeding to Risk Assessment...**"

#### Menu Handling Logic:

- After Related Tests section is appended to {outputFile} and frontmatter updated, immediately load, read entire file, then execute {nextStepFile}

#### EXECUTION RULES:

- This is an auto-proceed step — analysis is autonomous
- Proceed directly to next step after findings are appended to report

## CRITICAL STEP COMPLETION NOTE

ONLY WHEN the Related Tests section has been populated with coverage map and gap analysis and frontmatter updated, will you then load and read fully `{nextStepFile}` to execute the Risk Assessment.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All impacted zones checked for test coverage
- Test coverage classified per zone (well/partial/indirect/none)
- Coverage gaps identified and prioritized
- Surprising impacts cross-referenced with coverage
- Related Tests section populated in report
- Frontmatter updated with stepsCompleted
- Auto-proceeded to step 05

### ❌ SYSTEM FAILURE:

- Not checking all impacted zones (scope + direct deps + indirect coupling)
- Recommending new tests (that's step 05)
- Making risk assessments (that's step 05)
- Not identifying coverage gaps
- Not appending to report before proceeding

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
