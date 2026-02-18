---
validationDate: 2026-02-10
workflowName: risk-map
workflowPath: _bmad-output/bmb-creations/workflows/risk-map
validationStatus: PASS_WITH_WARNINGS
---

# Validation Report: risk-map

**Validation Started:** 2026-02-10
**Validator:** BMAD Workflow Validation System
**Standards Version:** BMAD Workflow Standards

---

## File Structure & Size

### Folder Structure: ✅ PASS

```
risk-map/
├── workflow.md                          ✅ Entry point
├── steps-c/                             ✅ Step files organized
│   ├── step-01-init-discover.md         ✅ Sequential numbering
│   ├── step-02-score-prioritize.md
│   ├── step-03-validate.md
│   └── step-04-generate-recommend.md
└── data/                                ✅ Supporting data organized
    ├── scoring-rubric.md
    └── audit-data-extraction.md
```

- workflow.md exists ✅
- Step files in well-organized `steps-c/` folder ✅
- Reference files organized in `data/` ✅
- Folder names are clear and descriptive ✅
- No stray files outside organized folders ✅
- No teammates needed (Conrad solo) ✅

### File Sizes: ✅ PASS

| File | Lines | Status |
|------|-------|--------|
| workflow.md | 63 | ✅ Good |
| step-01-init-discover.md | 224 | ✅ Good |
| step-02-score-prioritize.md | 195 | ✅ Good |
| step-03-validate.md | 195 | ✅ Good |
| step-04-generate-recommend.md | 222 | ✅ Good |
| data/scoring-rubric.md | 148 | ✅ N/A (not a step file) |
| data/audit-data-extraction.md | 146 | ✅ N/A (not a step file) |

**No files exceed 250-line maximum.** All step files well within limits.

### File Presence: ✅ PASS

All 4 steps from design are present. Sequential numbering correct (01, 02, 03, 04). Final step (04) has no nextStepFile. No continuable step (single-session workflow).

---

## Frontmatter Validation

### ✅ PASS

| File | name | description | Variables Used | Unused Vars |
|------|------|-------------|----------------|-------------|
| step-01 | ✅ | ✅ | 6/6 | 0 |
| step-02 | ✅ | ✅ | 3/3 | 0 |
| step-03 | ✅ | ✅ | 2/2 | 0 |
| step-04 | ✅ | ✅ | 2/2 | 0 |
| workflow.md | ✅ | ✅ | N/A | N/A |

**Variable usage detail:**

**step-01:** `nextStepFile` (line 189), `sidecarStatePath` (lines 64, 77, 181), `codeAnalysisPath` (lines 81, 97), `siteExplorationPath` (lines 82, 98), `dependencyScanPath` (lines 83, 99), `auditDataExtraction` (line 94) — all 6 used ✅

**step-02:** `nextStepFile` (line 161), `sidecarStatePath` (line 161), `scoringRubric` (line 61) — all 3 used ✅

**step-03:** `nextStepFile` (line 164), `sidecarStatePath` (line 153) — all 2 used ✅

**step-04:** `sidecarStatePath` (lines 170-175), `riskMapOutputPath` (lines 42, 59, 181, 202) — all 2 used ✅

**Config variables** (`{communication_language}`, `{document_output_language}`, `{project_name}`, `{user_name}`) resolved from workflow.md config loading — correctly not in step frontmatter.

**All frontmatter variables are used in their step body.** Relative paths used within workflow folder.

---

## Critical Path Violations

### ✅ PASS — No violations

- All file references use `{variable}` format ✅
- All paths within workflow folder use relative format (`./`, `../`) ✅
- No hardcoded absolute paths ✅
- Cross-references between steps use frontmatter variables ✅
- Data file references use `../data/` relative pattern ✅

---

## Menu Handling Validation

### ✅ PASS

| Step | Menu Type | Handler Follows Display | Halt & Wait | A/P Appropriate | Non-C Redisplay |
|------|-----------|------------------------|-------------|-----------------|-----------------|
| 01 | C only (+ zone adjustments) | ✅ | ✅ | ✅ (excluded — init) | ✅ |
| 02 | C only | ✅ | ✅ | ✅ (excluded — lean) | ✅ |
| 03 | C only (after multi-stage validation) | ✅ | ✅ | ✅ (excluded — lean) | ✅ |
| 04 | Final (no menu) | ✅ (N/A — final) | N/A | N/A | N/A |

**Notes:**
- A/P (Advanced Elicitation / Party Mode) excluded from all menus per design decision (lean technical synthesis workflow)
- Step 03 has intermediate interaction points (overrides, UNCERTAIN classification) before the final C menu — appropriate for its validation role
- Step 04 is a final step — ends with presentation and recommendation, no routing needed

---

## Step Type Validation

### ✅ PASS

| Step | Declared Type | Implementation Matches | Notes |
|------|--------------|----------------------|-------|
| 01 | Init (With Input Discovery) | ✅ | Dual entry mode → prerequisites → artifact loading → zone extraction |
| 02 | Middle (Simple) | ✅ | C-only menu, autonomous scoring, rubric loading |
| 03 | Middle (Standard) | ✅ | C-only menu, collaborative validation with developer |
| 04 | Final Step | ✅ | No nextStepFile, document generation + recommendation |

---

## Output Format Validation

### ✅ PASS

- Workflow is document-producing type (correct — risk-map.md) ✅
- Output template embedded in step-04 with full structure ✅
- 6 section anchors defined (`<!-- SECTION:... -->`) ✅
- Output frontmatter with `status: complete` defined ✅
- Document output language directive (`{document_output_language}`) present ✅
- Sidecar-state tracking defined throughout ✅

---

## Validation Design Check

### ✅ PASS

- Single-session workflow (no step-01b needed) ✅
- Sidecar-state used for progress tracking ✅
- Dual entry mode (chained/standalone) handled in step-01 ✅
- Prerequisite verification for standalone mode ✅
- Error messages defined for missing artifacts ✅

---

## Instruction Style Check

### ✅ PASS

All step files include required sections:
- ✅ STEP GOAL — clear, contextual
- ✅ MANDATORY EXECUTION RULES with Universal, Role Reinforcement, Step-Specific subsections
- ✅ EXECUTION PROTOCOLS
- ✅ CONTEXT BOUNDARIES (available, focus, limits, dependencies)
- ✅ MANDATORY SEQUENCE with numbered sections
- ✅ SYSTEM SUCCESS/FAILURE METRICS with Master Rule
- ✅ `{communication_language}` directive in all steps

**Role Reinforcement consistency:** Conrad persona maintained across all steps with appropriate role adaptation:
- Step 01: methodical data synthesizer
- Step 02: risk analyst, systematic scorer
- Step 03: collaborative risk advisor
- Step 04: delivering the central artifact

---

## Collaborative Experience Check

### ✅ PASS

| Step | Collaboration Pattern | Quality |
|------|----------------------|---------|
| 01 | Zone inventory confirmation + adjustment options | ✅ Light collaboration |
| 02 | Scoring results presentation (90% autonomous) | ✅ Informative, transparent |
| 03 | Business overrides + UNCERTAIN classification + top-10 confirmation | ✅ True collaboration (key moment) |
| 04 | Completion presentation + recommendation | ✅ Reassuring, clear next steps |

---

## Subprocess Optimization Opportunities

### ✅ PASS

| Step | Pattern | Implemented |
|------|---------|-------------|
| 01 | Pattern 3 (Data Ops) — load 3 audit artifacts | ✅ Subprocess with main-thread fallback |
| 02 | Pattern 3 (Data Ops) — load scoring rubric | ✅ Subprocess with main-thread fallback |

All subprocess references include fallback instructions.

---

## Cohesive Review

### ✅ PASS

**Data flow integrity:**
- Step 01 outputs (zones_l1, zones_l2, zones_l3_candidates, dep_zone_mapping) → stored in memory → used by step 02 ✅
- Step 02 outputs (scored_zones, heat_map, priority_ranking) → stored in memory → used by step 03 ✅
- Step 03 outputs (validated_zones, overrides, uncertain_resolved) → stored in memory → used by step 04 ✅
- Step 04 outputs → risk-map.md + sidecar-state update ✅

**Step transitions:**
- workflow.md → step-01 → step-02 → step-03 → step-04 (linear) ✅
- step-01: chained mode skip to section 3 (internal) ✅
- step-03: override loop back to section 2 (internal) ✅

**No orphaned steps or dead-end routes.**

**Cross-file consistency:**
- Scoring formula identical in: scoring-rubric.md, step-02, step-04 output template ✅
- Vulnerability modifier values consistent: scoring-rubric.md, step-02 section 3 ✅
- Zone hierarchy consistent: audit-data-extraction.md, step-01, step-02, step-04 output ✅
- Risk tier thresholds consistent: scoring-rubric.md, step-02 section 4 ✅

---

## Plan Quality Validation

### ✅ PASS

- Plan file exists with complete frontmatter ✅
- All classification decisions documented ✅
- Requirements section complete ✅
- Party Mode refinements tracked (12 points across 3 rounds) ✅
- Design section matches implementation ✅
- Build summary documents all files ✅
- Status: CONFIRMED ✅

---

## Minor Inconsistency Found

### ⚠️ WARNING — L3 Pre-tagging Wording

**Location:** `data/audit-data-extraction.md` Phase 3 rule vs `step-01` section 6

**Issue:** The data file says "Do not pre-tag during zone discovery" but step-01 section 6 tags L3 candidates during zone discovery.

**Analysis:** The intent is consistent — step-01 identifies candidates, and step-02 section 5 applies the actual drill-down only for CRITICAL/HIGH zones. The data file wording is slightly stricter than the step implementation.

**Fix:** Update data file rule to: "Identify L3 candidates during zone discovery. Only include in drill-down output after scoring determines the zone is CRITICAL or HIGH."

---

## Summary

**Overall Status: ✅ PASS WITH WARNINGS**

| Check | Result |
|-------|--------|
| File Structure & Size | ✅ PASS |
| Frontmatter Validation | ✅ PASS |
| Critical Path Violations | ✅ PASS |
| Menu Handling | ✅ PASS |
| Step Type Validation | ✅ PASS |
| Output Format | ✅ PASS |
| Validation Design | ✅ PASS |
| Instruction Style | ✅ PASS |
| Collaborative Experience | ✅ PASS |
| Subprocess Optimization | ✅ PASS |
| Cohesive Review | ✅ PASS |
| Plan Quality | ✅ PASS |

**Warnings (non-blocking):**
- 1 minor wording inconsistency in data file (L3 pre-tagging rule)

**Recommendation:** Workflow is ready for use. Apply the minor wording fix in audit-data-extraction.md for consistency.
