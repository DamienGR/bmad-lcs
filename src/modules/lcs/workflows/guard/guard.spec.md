# Workflow Specification: Guard

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Continuous protection post-consolidation — the daily-use companion.

**Description:** After consolidation phases are complete, Guard mode becomes the developer's permanent companion. Before any modification, it triggers Impact Analysis to identify potentially affected zones. Runs relevant safety nets after changes. The "killer feature" for long-term adoption.

**Workflow Type:** Core (Phase 5)

---

## Workflow Structure

### Entry Point

```yaml
---
name: guard
description: Continuous protection and impact analysis for daily development
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/guard'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Activate Guard | Enter guard mode — monitor for incoming changes |
| 02 | Pre-Change Analysis | Before modification: trigger impact-analysis workflow |
| 03 | Change Monitoring | Track what was actually modified |
| 04 | Post-Change Validation | Run relevant safety nets for impacted zones |
| 05 | Report | Quick summary: what changed, what was tested, pass/fail |

---

## Workflow Inputs

### Required Inputs

- Completed consolidation (all prior phases)
- Sidecar state with full audit + safety net data

### Optional Inputs

- Scope of planned changes (files, features)

---

## Workflow Outputs

### Output Format

- [x] Non-document (validation results)
- [x] Document-producing (impact reports)

### Output Files

- `_bmad-output/lcs/guard/impact-report-{date}.md` — Per-change impact reports

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — runs guard mode directly.

### Other Agents

May spawn Code Analyst for deep impact analysis when needed.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- This is the long-term value proposition — must be fast and non-intrusive
- Impact Analysis is the core mechanism (delegates to impact-analysis workflow)
- Should integrate with git hooks for automatic pre-commit analysis
- Guard mode is lightweight — only deep-scans when changes touch high-risk zones

---

_Spec created on 2026-02-09 via BMAD Module workflow_
