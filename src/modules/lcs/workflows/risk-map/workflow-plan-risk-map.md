---
conversionFrom: 'src/modules/lcs/workflows/risk-map/risk-map.spec.md'
originalFormat: 'spec-placeholder'
stepsCompleted: ['step-00-conversion', 'step-02-classification', 'step-03-requirements', 'step-04-tools', 'step-05-plan-review', 'step-06-design', 'step-07-foundation', 'step-08-build-step-01', 'step-09-build-steps', 'step-10-confirmation']
created: 2026-02-10
status: CONFIRMED
confirmationDate: 2026-02-10
confirmationType: conversion
coverageStatus: complete
approvedDate: 2026-02-10
---

# Workflow Creation Plan

## Conversion Source

**Original Path:** src/modules/lcs/workflows/risk-map/risk-map.spec.md
**Original Format:** Spec placeholder (no workflow.md or step files exist yet)
**Detected Structure:** Single specification file defining 5 planned steps, Conrad only (no teammates), inputs from 3 audit artifacts, document output (risk-map.md)

---

## Original Workflow Analysis

### Goal (from source)

Cross audit findings into a prioritized risk map for the entire project. Synthesizes Code Analyst findings, Site Explorer findings, and dependency audit into a unified risk map. Each zone/component gets a risk score based on complexity, coupling, test coverage, known vulnerabilities, and business criticality. The risk map drives prioritization for all subsequent phases.

### Original Steps (Complete List)

**Step 01:** Collect Findings - Load all audit output files (code-analysis, site-exploration, dependency-audit)
**Step 02:** Score Zones - Assign risk score per zone (complexity x coupling x criticality)
**Step 03:** Prioritize - Rank zones by risk score, identify top-10 critical areas
**Step 04:** Generate Map - Produce visual/structured risk map
**Step 05:** Recommendations - Suggest phase order and focus areas for safety nets + refactoring

### Output / Deliverable

- **Document output:** `_bmad-output/lcs/risk-map.md` — Prioritized risk map

### Input Requirements

**Required:**
- Code analysis report (`_bmad-output/lcs/audit/code-analysis.md`)
- Site exploration report (`_bmad-output/lcs/audit/site-exploration.md`)
- Dependency audit (`_bmad-output/lcs/audit/dependency-audit.md`)

**Optional:**
- User-specified business criticality overrides

### Key Instructions to LLM

- Conrad (LCS Lead) executes this workflow alone — no teammates
- Triggered automatically at end of Audit phase (step-05-risk-map-trigger in audit workflow)
- Risk map is the central artifact driving all subsequent phases
- Must be human-readable — developer should understand and validate priorities
- Inspired by TEA: risk-based test prioritization patterns

---

## Conversion Notes

**What works well in original:**
- Clear 5-step structure with logical progression (collect → score → prioritize → generate → recommend)
- Well-defined inputs (3 audit artifacts) and single output (risk-map.md)
- Conrad solo — simple orchestration, no teammate complexity
- Central role in LCS pipeline — feeds safety-nets, refactoring, deep-testing

**What needs improvement:**
- Spec is high-level — steps need detailed scoring methodology
- No scoring formula defined (complexity x coupling x criticality mentioned but no weights)
- No zone definition standard (what constitutes a "zone"?)
- No UNCERTAIN zone handling (ADR-005)
- Step 04 mentions "visual" map but no rendering strategy
- No continuable/resume mechanism defined

**Compliance gaps identified:**
- No workflow.md entry point
- No step files (steps-c/) exist
- No data/ directory (scoring rubric, zone templates)
- Missing sidecar-state integration details
- Missing output template for risk-map.md

---

## Classification Decisions

**Workflow Name:** risk-map
**Target Path:** src/modules/lcs/workflows/risk-map/

**4 Key Decisions:**
1. **Document Output:** true (produces risk-map.md)
2. **Module Affiliation:** lcs (Legacy Consolidation Suite — Feature workflow)
3. **Session Type:** single-session (synthesis workflow, ~15-20 min)
4. **Lifecycle Support:** create-only (steps-c/ only)

**Structure Implications:**
- Needs `steps-c/` directory with step files
- No `step-01b-continue.md` needed (single-session)
- Needs output template for risk-map.md
- Needs `data/` directory for scoring rubric
- No teammates — Conrad solo

---

## Requirements

**Flow Structure:**
- Pattern: Linear — Init/Discover → Score/Prioritize → Validate → Generate/Recommend
- Phases: 4 phases majeures
  1. Init + Zone Discovery — Verify prerequisites, load 3 audit artifacts, extract/define zones
  2. Score + Prioritize — Apply scoring rubric, calculate scores, sort
  3. User Validation — Present ranking, get business overrides, mark UNCERTAIN
  4. Generate + Recommend — Produce risk-map.md, update sidecar-state
- Estimated steps: 4 step files

**User Interaction:**
- Style: Mixed
  - Prescriptive for scoring (defined formula in data file)
  - Intent-based/collaborative for business priority validation
- Decision points: Business criticality overrides (step 03), UNCERTAIN zone confirmation
- Checkpoint frequency: Before final generation (dev validates ranking)

**Inputs Required:**
- Required:
  - Code analysis report (`_bmad-output/lcs/audit/code-analysis.md`)
  - Site exploration report (`_bmad-output/lcs/audit/site-exploration.md`)
  - Dependency scan (`_bmad-output/lcs/audit/dependency-scan.md`)
  - Sidecar-state with audit phase completed
- Optional:
  - Business criticality overrides (developer adjusts priorities)
- Prerequisites:
  - Pre-flight completed (Phase 0)
  - Audit completed (Phase 1)

**Output Specifications:**
- Type: Document-producing
- Format: Structured — required sections
- Sections:
  - Zone Inventory (3-level hierarchy: functional, component, file)
  - Scoring Matrix (5 dimensions + vulnerability modifier)
  - ASCII Heat Map (visual risk summary)
  - Priority Ranking (top-10 critical areas)
  - UNCERTAIN Zones (ADR-005 markers)
  - Recommendations per Phase (safety-nets, refactoring, deep-testing priorities)
- File: `_bmad-output/lcs/risk-map.md`

**Success Criteria:**
- All zones from code-analysis + site-exploration covered (no zone without score)
- Scoring uses 5-dimension weighted formula (not simple multiplication)
- Vulnerability modifier applied from dependency scan
- 3-level zone hierarchy visible (functional → component → file)
- ASCII heat map included for immediate visual understanding
- UNCERTAIN zones (ADR-005) identified and marked
- Top-10 validated by developer with business overrides applied
- Risk map is human-readable (developer can understand and act on it)
- Sidecar-state updated (risk-map task → done)

**Instruction Style:**
- Overall: Mixed
- Scoring: Prescriptive — formula defined in data/scoring-rubric.md
- Zone extraction: Prescriptive — systematic extraction from audit artifacts
- Priority validation: Intent-based — Conrad discusses with dev to validate/adjust
- Note: Follows Conrad persona (direct, methodical, reassuring)

---

## Party Mode Refinements (Agent Feedback)

### 1. Scoring Formula — 5 Dimensions + Weights (Murat — TEA)

**Problem:** Original spec used simple `complexity × coupling × criticality` — too simplistic, explosive scores.
**Action:** Use 5-dimension weighted sum:

`risk_score = (complexity × 0.2) + (coupling × 0.2) + (volatility × 0.15) + (coverage_gap × 0.2) + (business_criticality × 0.25)`

Each dimension scored 1-5. Weights favor business criticality and coverage gap (most actionable).

### 2. Hierarchical Zone Definition (Dr. Quinn)

**Problem:** "Zone" was undefined — could mean file, folder, or functional area.
**Action:** 3-level hierarchy:
- **Level 1 — Functional:** User journey (checkout, login, admin dashboard)
- **Level 2 — Component:** Group of files forming a logical unit (payment module, user management) — PRIMARY scoring unit
- **Level 3 — File:** Individual high-risk files (the 3000-line functions.php)

Scoring at Level 2, roll-up to Level 1, drill-down to Level 3 for critical files.

### 3. Vulnerability Modifier (Dr. Quinn)

**Problem:** Dependency audit is not an independent dimension — it's a risk multiplier.
**Action:** After base score calculation, apply vulnerability modifier:
- 0 critical CVEs → ×1.0
- 1-2 critical CVEs → ×1.2
- 3+ critical CVEs → ×1.5

### 4. Revised Step Structure (Wendy)

**Problem:** Original 5 steps had artificial separation between Score and Prioritize, and missing User Validation.
**Action:** 4 steps:
- Step 01: Init + Zone Discovery
- Step 02: Score + Prioritize
- Step 03: User Validation (CRUCIAL — business overrides)
- Step 04: Generate + Recommend

### 5. ASCII Heat Map (Murat — TEA)

**Problem:** Risk map needs immediate visual understanding.
**Action:** Include ASCII heat map in output:
```
CRITICAL ████████ payment-gateway, checkout-flow
HIGH     ██████   user-auth, admin-panel, search
MEDIUM   ████     blog-engine, media-library
LOW      ██       static-pages, footer-widgets
```

### 6. Data File: scoring-rubric.md

**Action:** Create `data/scoring-rubric.md` containing:
- 5 dimension definitions with scoring guide (1-5 per dimension)
- Weight table
- Vulnerability modifier table
- Zone level definitions
- Example scored zone

### 7. Input Contracts Data File (Morgan — Round 2)

**Problem:** Risk-map consumes 3 audit artifacts but doesn't define expected format. If audit changes structure, risk-map breaks silently.
**Action:** Create `data/input-contracts.md` documenting:
- From code-analysis: file list with complexity metrics, coupling counts
- From site-exploration: journey list with URLs, interactive elements
- From dependency-scan: dependency list with CVE details
Protects against drift between workflows.

### 8. Automatic Scoring (Murat — Round 2)

**Problem:** Manually scoring 40+ zones on 5 dimensions is slow even for an LLM.
**Action:** Step 02 is 90% autonomous — Conrad scores ALL zones automatically from audit data, then presents results. No manual scoring by developer.

### 9. Drill-down Threshold (Murat — Round 2)

**Problem:** Level 3 drill-down for every zone wastes context.
**Action:** Only drill down to Level 3 (individual files) for CRITICAL and HIGH zones. LOW/MEDIUM get Level 2 only.

### 10. Dual Entry Mode (Wendy — Round 2)

**Problem:** Risk-map is triggered automatically from audit (chained) OR launched manually (standalone). Same prerequisite checks in both cases is redundant.
**Action:** Step 01 detects entry mode:
- **Chained mode** (`{triggered_by}` set): Skip prerequisite verification, artifacts just written. Faster.
- **Standalone mode** (no trigger): Full prerequisite check, artifact freshness validation.

### 11. Section Anchors for Consumers (Morgan — Round 2)

**Problem:** 3 downstream workflows consume risk-map sections. If section titles change, consumers break.
**Action:** Add HTML anchor comments in risk-map.md template:
```markdown
<!-- SECTION:zone-inventory -->
<!-- SECTION:scoring-matrix -->
<!-- SECTION:heat-map -->
<!-- SECTION:priority-ranking -->
<!-- SECTION:uncertain-zones -->
<!-- SECTION:recommendations -->
```
Consumers target anchors, not titles.

### 12. Dependency-to-Zone Mapping (Murat — Round 2)

**Problem:** CVE data in dependency-scan is per-dependency, not per-zone. Vulnerability modifier needs mapping.
**Action:** Add sub-step in Step 01 (Zone Discovery): after identifying zones, map vulnerable dependencies to zones. Step 02 uses this mapping for the vulnerability modifier.

---

## Tools Configuration

**Core BMAD Tools:**
- **Party Mode:** Excluded — technical synthesis workflow, no creative exploration needed
- **Advanced Elicitation:** Excluded — data comes from audit artifacts, no deep elicitation needed
- **Brainstorming:** Excluded — not relevant for factual scoring

**LLM Features:**
- **File I/O:** Included — core requirement. Load 3 audit artifacts, read sidecar-state, write risk-map.md
- **Web-Browsing:** Excluded — all data comes from local artifacts
- **Sub-Agents:** Excluded — Conrad works alone, no teammates
- **Sub-Processes:** Excluded — linear workflow, no parallelism

**Memory:**
- Type: Single-session
- Tracking: sidecar-state.yaml (risk-map task status)
- No stepsCompleted in output document (tracking via sidecar-state)

**External Integrations (MCP):**
- None required
- Git CLI not needed (analysis only, no branching)

**Installation Requirements:**
- None

---

## Workflow Structure Design

### Step Files

| Step | Type | Name | Goal | Menu |
|------|------|------|------|------|
| 01 | Init (With Input Discovery) | `step-01-init-discover` | Detect entry mode (chained/standalone), verify prerequisites, load 3 audit artifacts, extract zones (3 levels), map dependencies→zones | C only |
| 02 | Middle (Simple) | `step-02-score-prioritize` | Load scoring-rubric, auto-score all L2 zones, apply vulnerability modifier, sort, produce ASCII heat map | C only |
| 03 | Middle (Standard) | `step-03-validate` | Present ranking to dev, allow business overrides, confirm UNCERTAIN zones, validate top-10 | C only |
| 04 | Final Step | `step-04-generate-recommend` | Produce risk-map.md with section anchors, update sidecar-state, recommend safety-nets | Final |

### Data Files

| File | Purpose |
|------|---------|
| `data/scoring-rubric.md` | 5 dimensions (1-5) with data sources + fallbacks, weights, vulnerability modifier, zone levels, example |
| `data/audit-data-extraction.md` | Input contracts + zone extraction guide + dependency-to-zone mapping (merged per Morgan Round 3) |

### File Structure

```
risk-map/
├── risk-map.spec.md          (existing)
├── workflow.md
├── steps-c/
│   ├── step-01-init-discover.md
│   ├── step-02-score-prioritize.md
│   ├── step-03-validate.md
│   └── step-04-generate-recommend.md
└── data/
    ├── scoring-rubric.md
    └── audit-data-extraction.md
```

### Interaction Patterns

- **Step 01:** Autonomous (loading) + light feedback (confirm zones found)
- **Step 02:** 90% autonomous (auto-scoring), 10% presentation of results
- **Step 03:** Collaborative — key moment where dev validates/adjusts business priorities
- **Step 04:** Autonomous (generation) + final presentation + recommendation

No A/P in menus (lean workflow, no creative refinement needed).

### Data Flow

```
[Audit: code-analysis] ─┐
[Audit: site-exploration]├→ Step 01 → zones[] + dep_mapping{}
[Audit: dependency-scan] ┘       [audit-data-extraction.md]
[Sidecar-state] ──────────→

Step 01 → Step 02 → scored_zones[] + heat_map
          [scoring-rubric.md]

Step 02 → Step 03 → validated_zones[] + overrides{}
          [user decisions]

Step 03 → Step 04 → risk-map.md + sidecar-state update
          → recommend safety-nets (no auto-trigger)
```

### Role and Persona

Conrad (LCS Lead) — already defined:
- Methodical, direct, reassuring
- Communicates in `{communication_language}`
- In this workflow: risk analyst, synthesizer

### Validation and Error Handling

- **Missing input:** Step 01 — standalone mode checks all 3 artifacts. If missing → error: "Complete audit first"
- **Chained mode:** Step 01 detects `{triggered_by}`, skips redundant checks
- **Missing dimension data:** Step 02 — if a dimension can't be scored (e.g., volatility), use default 3 (neutral). Document in scoring-rubric.md with data source per dimension + fallback.
- **Invalid override:** Step 03 — bound scores to 1-5, no out-of-range values
- **Drill-down threshold:** Level 3 only for CRITICAL/HIGH zones. LOW/MEDIUM get Level 2 only.

### Subprocess Optimization

- **Step 01 — Pattern 3 (Data Ops):** Load 3 audit artifacts via subprocess, extract only zones and metrics. Return structured data, not full content. Fallback: main thread.
- **Step 02 — Pattern 3 (Data Ops):** Load scoring-rubric.md via subprocess. Fallback: main thread.
- All steps: fallback to main thread if subprocess unavailable.

### Workflow Chaining

**Input Discovery (Step 01):**
- Required: `{output_folder}/lcs/audit/code-analysis.md`, `site-exploration.md`, `dependency-scan.md`
- Validation: check frontmatter `status: complete`
- If missing → "Complete audit (Phase 1) first"

**LCS Pipeline:**
```
pre-flight (Phase 0) → audit (Phase 1) → risk-map → safety-nets (Phase 2) → dependency-update (Phase 2b)
```

**Step 04 recommends:** `safety-nets` as next workflow (recommend, no auto-trigger — dev confirms)

### Party Mode Round 3 Refinements

1. **Merged data file** (Morgan + Wendy): `data/audit-data-extraction.md` combines input contracts + zone extraction guide + dependency mapping. One file instead of two.
2. **Scoring-rubric data sources** (Murat): Each dimension documents its data source in audit artifacts + fallback value if data absent. Volatility default = 3 (neutral).
3. **No auto-trigger** (Wendy): Step 04 recommends safety-nets but does NOT auto-trigger. Dev confirms before launching.

---

## Build Summary

### Foundation (Step 07)

**Created:**
- `workflow.md` — Entry point with LCS module config, sidecar-state paths, role definition
- `data/scoring-rubric.md` — 5 dimensions (1-5) with weights, vulnerability modifier, zone levels, data sources + fallbacks, example
- `data/audit-data-extraction.md` — Input contracts + zone extraction guide + dependency-to-zone mapping

### Step 01 Build (Step 08)

**Created:** `steps-c/step-01-init-discover.md` (224 lines)
- Type: Init (With Input Discovery), non-continuable
- Dual entry mode: chained (skip prereqs) / standalone (full verification)
- Loads 3 audit artifacts, extracts 3-level zone hierarchy, maps dependencies→zones
- Subprocess Pattern 3 for artifact loading
- Menu: C-only (zone inventory confirmation)
- Next: step-02-score-prioritize

### Step 02 Build (Step 09, iteration 1)

**Created:** `steps-c/step-02-score-prioritize.md` (195 lines)
- Type: Middle (Simple)
- Auto-scores all L2 zones on 5 dimensions, applies vulnerability modifier
- Sorts, classifies tiers, generates ASCII heat map
- Subprocess Pattern 3 for scoring rubric loading
- 90% autonomous — no manual scoring
- Menu: C-only
- Next: step-03-validate

### Step 03 Build (Step 09, iteration 2)

**Created:** `steps-c/step-03-validate.md` (195 lines)
- Type: Middle (Standard)
- Developer validates ranking, applies business criticality overrides
- Classifies UNCERTAIN zones, confirms final top-10
- Key collaborative moment — intent-based interaction
- Menu: C-only (after explicit top-10 confirmation)
- Next: step-04-generate-recommend

### Step 04 Build (Step 09, iteration 3)

**Created:** `steps-c/step-04-generate-recommend.md` (222 lines)
- Type: Final Step
- Generates risk-map.md with 6 section anchors for downstream consumers
- Updates sidecar-state with phase completion
- Recommends safety-nets as next phase (NO auto-trigger)
- Menu: Final (workflow ends)
