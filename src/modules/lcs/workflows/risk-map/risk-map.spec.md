# Workflow Specification: Risk Map

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Cross audit findings into a prioritized risk map for the entire project.

**Description:** Synthesizes Code Analyst findings, Site Explorer findings, and dependency audit into a unified risk map. Each zone/component gets a risk score based on complexity, coupling, test coverage, known vulnerabilities, and business criticality. The risk map drives prioritization for all subsequent phases.

**Workflow Type:** Feature

---

## Workflow Structure

### Entry Point

```yaml
---
name: risk-map
description: Generate prioritized risk map from audit findings
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/risk-map'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Collect Findings | Load all audit output files |
| 02 | Score Zones | Assign risk score per zone (complexity × coupling × criticality) |
| 03 | Prioritize | Rank zones by risk score, identify top-10 critical areas |
| 04 | Generate Map | Produce visual/structured risk map |
| 05 | Recommendations | Suggest phase order and focus areas for safety nets + refactoring |

---

## Workflow Inputs

### Required Inputs

- `_bmad-output/lcs/audit/code-analysis.md`
- `_bmad-output/lcs/audit/site-exploration.md`
- `_bmad-output/lcs/audit/dependency-audit.md`

### Optional Inputs

- User-specified business criticality overrides

---

## Workflow Outputs

### Output Format

- [x] Document-producing

### Output Files

- `_bmad-output/lcs/risk-map.md` — Prioritized risk map

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — executes directly after audit synthesis.

### Other Agents

None. Lead synthesizes from audit data.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- # Inspired by TEA: risk-based test prioritization patterns
- Risk map is the central artifact that drives all subsequent phases
- Must be human-readable — the developer should understand and validate priorities
- Triggered automatically at end of Audit phase

---

_Spec created on 2026-02-09 via BMAD Module workflow_
